# 优化措施

## 网络方面
1.  缓存
    - 缓存性能优化可以降低资源重复加载提高网页整体加载速度
    - 浏览器缓存分为：强缓存与协商缓存
    - serviceWorker做离线缓存
2.  使用HTTP2.0
    - HTTP/1.1每次个请求需要建立与断开TCP链接，会有时间损耗，并且浏览器对相同IP有并行请求的会有限制（6个）
    - HTTP2.0引用多路复用，可以让多个请求使用同一个TCP链接，没有并行请求限制，支持Header压缩和服务端推送资源等优势


## 外部资源方面
1.  DNS预解析，预链接
    - DNS解析需要时间，可以预先在html预解析域名对应ip（仅对跨域有效，同源多此一举）
    ```html
    <link rel="dns-prefectch" href="//abc.com" />
    ```
    - dns-prefetch 仅执行 DNS查找，preconnect会建立与服务器的连接，将两者结合起来可提供进一步减少跨域请求的感知延迟的机会
    - preconnect 提示最好仅用于最关键的连接，过多则会适得其反
    ```html
    <link rel="preconnect" href="https://fonts.gstatic.com/" crossorigin>
    <link rel="dns-prefetch" href="https://fonts.gstatic.com/">
    ```
    - subresource 可以用来指定资源是最高优先级的。
    - rel=prefetch 指定了下载后续页面用到资源的低优先级，而 rel=subresource 则是指定当前页面资源的提前加载
    ```html
    <link rel="subresource" href="styles.css">
    ```
2.  预加载
    - 有些资源不是马上用到，但是需要尽早获取，这时需要预加载(js、css、图片文件)
    - 强制浏览器请求资源，不会阻塞onload事件
    - 使用 as 来指定将要预加载的内容的类型，将使得浏览器能够精确处理资源与accept请求头
    ```html
    <link rel="preload" href="http://abc.com/styles/main.css" as="style" />
    ```
3.  预渲染
    - 通过预渲染将下载文件预先在后台渲染，可以在html开启预渲染
    - 它可以让浏览器提前加载指定页面的所有资源。一定要在十分确定用户回点某个链接时才用这个特性，否则客户端就会无端的下载很多资源和渲染这个页面。
    ```html
    <link rel="prerender" href="http://abc.com" />
    ```
4.  懒执行与懒加载
    - 【懒执行】延迟执行耗时较高或者不在首屏使用的函数，用于首屏优化,一般用定时器或事件触发
    - 【懒加载】把不关键的资源延后加载，例如图片和视频（不如进入可视区开始播发视频）


## 静态资源方面
1.  文件优化
    - 压缩文件，GZIP
    - 图片优化：webp格式，小图base64、小图使用png，大图标使用svg, 多张图片合并（雪碧图）
    - js文件优化：标签加载按需使用defer与async
2.  CDN
    - 使用最短节点请求资源，并且使用cdn的缓存
    - 在请求链接添加图片裁剪参数（看cdn是否提供），减少静态资源大小
3.  webpack优化
    - 开启代码压缩
    - 使用tree shaking移除无用代码
    - 按路由拆分代码chunk，实现按需加载，但要控制chunk大小，过大js线程执行时间会过长
    - 添加文件名哈希，利用浏览器缓存，缓存文件（不改文件内容就使用缓存文件）
    - 使用wepack --report 查看chunk大小是否合理