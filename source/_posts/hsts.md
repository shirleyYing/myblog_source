title: HSTS详解-让信息传递更安全
date: 2018-01-31 22:10:46
tags:
---
http站点重定向问题优化
<!-- more -->
随着网络信息传输安全越来越被重视，https越来越普及，大量网站开始全面部署https。为了做到无痛切换，旧的`http://`必须要保持能够访问，比如，用户直接输入url进入网站，或者保存了之前使用http协议的书签，直接点击链接进入。那怎么办呢？最简单的办法就是使用302重定向，服务端返回302，在`location`中设置新的`https`的网址，浏览器再一次发起请求。像这样

![网络请求.png](http://upload-images.jianshu.io/upload_images/3301631-a9066e5a8c88fafc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这样的方式存在两点不足：
1. 第一个请求是明文，若包含敏感信息，容易泄露，很难抵御SSL剥离攻击。
2. 第一个请求无效，浪费了网络请求

**SSL剥离攻击**，就是利用用户很少直接在地址栏输入`https://`，用户总是通过点击链接或**3xx重定向**，从HTTP页面进入HTTPS页面,攻击利用302重定向，将所有https的重定向链接修改为http，从而阻止建立https的安全链接。为了解决这一问题，互联网工程任务组发布了一个互联网安全策略机制：HSTS-HTTP严格传输安全。下面，让我们一探究竟。
## 含义
网站选择使用HSTS策略，来让浏览器强制使用HTTPS会话劫持风险。
## 工作原理
该策略主要依靠服务端实现，当客户端(浏览器)通过`https`发出请求时，服务端在响应头中包含`Strict-Transport-Security`字段，例如：`Strict-Transport-Security: max-age =31536000 `

这意味着：
1. 在接下来的一年中（max-age），所有通过http访问www.example.com及子域名时，比如，访问`http://www.example.com`浏览器应当自动将 http 转写成 https，使用307 Internal Redirect ，直接向` https://www.example.com/` 发送请求。
2. 在接下来的一年中，如果 example.com 服务器发送的TLS证书无效，用户不能忽略浏览器警告继续访问网站

好处是显而易见的：
1. 避免SSL剥离攻击。**只要浏览器曾经与服务器创建过一次安全连接**，之后浏览器会强制使用HTTPS，即使链接被换成了HTTP。
2. 避免假冒证书攻击。如果中间人使用自己的自签名证书来进行攻击，浏览器会给出警告，但是许多用户会忽略警告。HSTS解决了这一问题，一旦服务器发送了HSTS字段，用户将不再允许忽略警告。

看到这里，或许你也发现了，HSTS还有一点没有解决：用户首次访问某网站怎么办？还有办法：浏览器预置HSTS域名列表（HSTS preload list）
## HSTS preload list
### 含义
HSTS preload list是Chrome浏览器中的HSTS预载入列表，在该列表中的网站，使用Chrome浏览器访问时，会自动转换成HTTPS。Firefox、Safari、Edge浏览器也在采用这个列表。
### 使用
加入HSTS preload list不但不麻烦，而且Chrome也鼓励HTTPS网站能够主动加入。申请的方法和需要满足的条件在https://hstspreload.appspot.com网站上都有具体说明。
服务端返回字段需要满足以下条件：
- `Strict-Transport-Security` max-age至少需要18周，10886400秒
- 必须指定includeSubdomains参数
- 必须支持preload参数
例如：`Strict-Transport-Security “max-age=31536000; includeSubDomains; preload`

## 参考
[解决缺陷，让HSTS变得完美](https://blog.wilddog.com/?page_id=1493)