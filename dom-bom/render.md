# 浏览器渲染机制

## 从浏览器地址栏到显示页面的步骤

### 基本版：
1.  浏览器根据url交给dns域名解析IP，请求服务器
2.  服务器交给后台处理返回业务数据，浏览器接收html、js、css、图片信息
3.  浏览器解析和解压资源（html、js、css、图片），建立内部数据结构如DOM、CSS树
4.  载入资源文件，渲染页面，完成

### 详细版

#### 请求阶段
1. 浏览器输入url
2. 浏览器查看缓存，判断强缓存/协商缓存
3. 解析url、协议、主机ip、端口、路径
4. 组装Http（GET）请求报文
5. 浏览器获取主机ip地址(DNS服务器，本机host文件)

#### 建立tcp
1. socket与目标ip建立TCP链接，三次握手
   1. 客户端 -> SYN=1, Seq=X -> 服务器
   2. 服务器 -> SYN=1, ACK=1, Seq=Y; ACKnum=X+1 -> 客户端
   3. 客户端 -> ACK=1 ,ACKnum=y+1
2. TCP链接后发送HTTP请求
3. 服务器检查协商缓存，有缓存返回304，无缓存交给处理程序读取请求并返回Http响应报文，期间可能还需要查询数据库
4. 处理数据（压缩Gzip），将响应报文通过TCP链接发回浏览器

#### 处理响应
1. 浏览器接收HTTP响应，根据情况（keep-alive判断）关闭TCP连接或者保留重用，关闭TCP链接四次握手
   1. 主动发送 -> Fin=1, Seq=X 报文, 主动方进入FIN_WAIT_1
   2. 被动方发送 -> ACK=1, ACKnum=X+1-> 报文，被动方进入CLOSE_WAIT， 主动方进入FIN_WAIT_2
   3. 被动方发送 -> Fin=1, Seq=Y -> 报文 被动方进入LAST_ACK
   4. 主动发送 -> ACK=1 ACKnum=Y+1 -> 报文 主动方进入TIME_WAIT状态， 被动方进入CLOSED 状态，主动方等待某个固定时间触发CLOSED 状态
2. 检查状态码 1XX 3XX 4XX 5XX 分别做不同的处理，其中如果资源可缓存就建立缓存，304使用缓存数据
3. 对缓存进行解码（gzip）

#### 构建数据结构
1. 解析html文档，构建dom树，下载解析css，构造cssom，执行html文本，构建渲染渲染树，执行js脚本
2. 显示页面

## DOM树构建过程
1. Tokenizing: 根据HTML规范解析为标记
2. Lexing: 词法分析将标记转换为对象并定于属性和规则
3. DOM construction: 根据HTML标记关系组成DOM树

## CSSOM树构建过程
1. Tokenizing: 字符流转为标记流
2. Node: 根据标记创建CSSOM树
3. CSSOM: 节点创建CSSOM树

## 构建渲染树过程
1. DOM树根节点遍历所有可见节点，排除不可见节点
  1. script
  2. meta
  3. css设置为display:none节点
2. 对每一个可见节点，找到恰当的CSSOM规则并应用
3. 发布可视节点和计算样式

## script元素解析过程
1. 【浏览器解析】浏览器是一边下载html网页，一边开始解析
2. 解析过程中发现script元素就会暂停解析，浏览器会吧网页渲染控制权交给js引擎
3. 如果遇到外部脚本，会同步下载该剧本再执行，否则直接执行（原因js可以修改DOM）
4. js引擎执行完毕，控制权交还渲染引擎，继续往下解析html网页

### script元素解析解析规则
- 加载外部script的过程是同步过程，浏览器会出现”阻塞“现象
  - 加载外部脚本时，浏览器会暂停渲染，等待剧本下载完成执行完毕再继续渲染，如果加载时间或执行时间很长，会造成网页长时间失去响应，这称为”阻塞效应“
- script标签不能解析他自身之后的DOM结构
  - 最理想做法就是script标签放在页面底部，就算脚本失去响应，可有效避免网页出现空白期，并且防止因为DOM结构未完整导致js发生错误
  - 以下代码会报错
   ```html
   <head>
      <script>
         console.log(document.body.innerHTML);
      </script>
   </head>
   <body>
   </body>
   ```
- 脚本的执行顺序由它们在页面中的出现顺序决定
  - 这是为了保证脚本之间的依赖关系不受到破坏, 加载这两个脚本都会产生“阻塞效应”
  - 浏览器会同时并行下载a.js和b.js，但是，执行时会保证先执行a.js，然后再执行b.js，即使后者先下载完成，也是如此。
  - 脚本的执行顺序由它们在页面中的出现顺序决定，这是为了保证脚本之间的依赖关系不受到破坏。
  - ```js
   <script src="a.js"></script>
   <script src="b.js"></script>
   ```
- defer属性，延迟脚本执行
   - 加载过程：
      1. 开始解析html网页
      2. 解析过程中发现defer属性script
      3. 浏览器继续往下解析html网页，同时并行下载script元素加载的外部脚本
      4. 浏览器完成解析html网页，此时再回头执行已下载完成的脚本
   - 作用延迟脚本执行，不会阻塞网页渲染和脚本下载，只有DOM加载完成后再执行脚本
   - 可以保证执行顺序就是它们在页面上出现的顺序
   - 对于内置脚本以及动态生成script标签，该属性不起任何作用
- async属性，使用另一个进程下载脚本，下载时候不会阻塞渲染，但下载完成时候会阻塞
   - 适用与脚本之间没有依赖关系的script
   - 加载过程：
      1. 开始解析html网页
      2. 解析过程中发现async属性script
      3. 浏览器继续往下解析html网页，同时并行下载script元素加载的外部脚本
      4. 浏览器下载完成，浏览器器暂停解析html，开始执行下载剧本
      5. 剧本执行完毕，浏览器继续解析HTML网页
- 同时使用async和defer属性，浏览器只会按async执行
- 浏览器默认采用 HTTP 协议下载
### 动态加载脚本
动态加载脚本不会阻塞页面渲染，但是无法保证脚本执行顺序哪个脚本先下载完成，就先执行，可以设置async属性为false
```js
function loadScript(src, done) {
  var js = document.createElement('script');
  js.src = src;
  js.onload = function() {
     // 完成回调
    done();
  };
  js.onerror = function() {
    done(new Error('Failed to load script ' + src));
  };
  document.head.appendChild(js);
}
```
