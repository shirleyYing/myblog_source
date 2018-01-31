title: 浏览器缓存原理
date: 2018-01-31 22:13:31
tags:
---

http1中利用缓存机制可以节约http请求数量，减少请求数据
<!-- more -->
弱缓存（协商缓存）
1.第一次请求， 服务端通过 last-Modified 或 Etag标记实体

再次发起请求，带上If-Modified-Since: 上次 Last-Modified 的内容或If-None-Match: 上次 ETag 的内容，询问服务端资源是否过期。（服务端此时会对比该文件在服务器上的last-modified和Etag，若没有变化，则表示没有过期）

没有过期，直接返回一个状态码为 304、正文为空的响应，告知浏览器使用本地缓存(可以节约返回数据)

过期，返回资源，状态码200，新的last-modified，ETag，和正文。

用两个字段完成标识的意义是？

last-modified有问题，第一，只能精确到秒，第二，负载均衡算法中，各机器读到的文件修改时间不一致，会导致文件无故失效或着得不到更新。ETap是由服务端生成的摘要，更准确。

强缓存
用 expire 和cache-control（max-age= xxx） 前者规定文件到期时间，后者规定时间段。expire会依赖客户端时间，cache-control是相对时间，更准确一点，若两者同时设定 cache-control会覆盖expire

浏览器刷新清缓存规则
F5(cmd+r):清楚一般缓存，就是弱缓存，可能看到304

control+F5(shift+cmd+r):强制刷新，强刷会忽略浏览器所有缓存（并且请求头会携带 Cache-Control:no-cache 和 Pragma:no-cache，用来通知所有中间节点忽略缓存）。只有从地址栏或收藏夹输入网址、点击链接等情况下，浏览器才会使用强缓存。

综合使用
一般服务端返回的头部都会带上上述字段，那是如何配合使用的呢？

浏览器缓存流程
第一次发起请求（www.baidu.com）

request header：无

response header：status：200，last-modified:xxx,ETag:'xxx',cache-control:max-age=3600

第二次发起同样的请求（浏览器刷新）

根据cache-control判断是否使用缓存，若未过期，不向服务端发起请求，如图




图1
若过期，则向服务端发起请求，服务端端根据last-modified和ETag判断文件是否有改动，若没有，则返回304，浏览器继续使用缓存

若文件有改动，则返回200，新的last-modified，ETag 以及正文
