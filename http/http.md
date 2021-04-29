# http协议

## HTTP协议特点
- 简单快速
  - 每个资源是固定的，如果想访问某个资源可以简单输入一个uri即可，请求方法常用的有GET、HEAD、POST
- 灵活
  - HTTP允许传输任意类型的数据对象，正在传输的类型由Content-Type加以标记
- 无连接
  - 限制每次连接只处理一个请求，服务器处理完客户的请求，并收到客户的应答后，即断开连接
- 无状态
  - HTTP协议是无状态协议，于事务处理没有记忆能力，下次链接需要前一个链接的数据

## HTTP请求报文
![image](/assest/http-request.png)
- 请求行
  - http方法 (GET)
  - 请求URL地址 (/home/xman/data/....)
  - HTTP协议及版本 (HTTP/1.1)
- 请求头（常有）
  - accept属性 告诉服务端 客户端接受什么类型的响应
  - referer属性 这个请求是从哪个URL过来的，即是表明当前网页的url
  - Cache-Control属性 对缓存进行控制，希望响应返回的内容在客户端要被缓存多长时间或不缓存
  - Content-Encodingp属性 压缩类型
- 空行
- 请求体
  - 请求内容（自定）

## HTTP响应报文
![image](/assest/http-response.png)
- 状态行
  - 报文协议 (HTTP)
  - HTTP版本号 (1.1)
  - 状态码 (200)
  - 状态描述 (OK)
- 响应头
  - Server //服务器应用程序软件的名称和版本
  - Content-Type  //响应正文的类型（是图片还是文本还是二进制字符串）
  - Content-Length  //响应正文长度
- 空行
- 响应体
  - 响应内容（自定）

## HTTP方法
- GET  获取资源
- POST 传输资源
- OPTIONS（CROS相关）
  - 获取服务器支持的HTTP请求方法
  - AJAX进行跨域请求时的预检，用以判断实际发送的请求是否安全
- PUT 更新资源
- DELETE 删除资源
- HEAD 获得报文首部

## POST和GET的区别
重要
- GET后退按钮/刷新无害，POST数据会被重新提交（浏览器应该告知用户数据会被重新提交）。
- GET书签可收藏，POST为书签不可收藏。
- GET历史参数保留在浏览器历史中。POST参数不会保存在浏览器历史中。
- GET对数据长度有限制，当发送数据时，GET 方法向 URL 添加数据；URL 的长度是受限制的（URL 的最大长度是 2048 个字符）。POST无限制。
- GET的数据在 URL 中对所有人都是可见的。POST的数据不会显示在 URL 中。
- 感信息时绝不要使用 GET ！POST 比 GET 更安全，因为参数不会被保存在浏览器历史或 web 服务器日志中。

了解
- GET编码类型application/x-www-form-url，POST编码类型encodedapplication/x-www-form-urlencoded 或 multipart/form-data。为二进制数据使用多重编码
- GET只允许 ASCII 字符。POST没有限制。也允许二进制数据。