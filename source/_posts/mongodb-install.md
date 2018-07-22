title: MAC下mongodb 安装及权限配置
date: 2018-07-22 23:03:06
tags: mongodb
---
mongodb 安装及用户配置流水账式记录
<!-- more -->
mac下直接使用了homeBrew安装。
#### 安装

```
brew update 
brew install mongodb
brew install mongodb --with-openssl //带TLS/SSL支持
brew install mongodb --devel //获取最新版本的
```
安装版本为 4.0
通过homebrew安装的会自动在`/usr/local/etc/`下创建`mongod.conf`文件。内容如下
```
systemLog:
  destination: file
  path: /usr/local/var/log/mongodb/mongo.log
  logAppend: true
storage:
  dbPath: /usr/local/var/mongodb
net:
  bindIp: 127.0.0.1
```
可以看到，指定了log和数据库文件位置。
#### 启动
启动指令：`mongod --config /usr/local/etc/mongod.conf -auth`
启动后的输出比以前少了不少。
```
2018-07-22T22:58:03.137+0800 I CONTROL  [main] Automatically disabling TLS 1.0, to force-enable TLS 1.0 specify --sslDisabledProtocols 'none'
```
#### 登录创建用户
1. 为了使用账号密码登录，在上面的启动命令中，加了 `-auth` ，作用是开启权限验证。
2. 切换到admin 数据库
```
> db.createUser({
     user:"admin",
     pwd:"admin",
     roles:[{
         role:"root",
         db:"admin"
     }]
 })
> db.auth("admin", "admin")
```
创建最高权限的管理员
3. 切换到所在数据库，创建用户
```
> db.createUser({user:"user",pwd:"123456",roles:[{role:"dbOwner",db:"mock"}]})
> db.auth("user", "123456")

```
