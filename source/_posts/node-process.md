title: Node多进程学习(一)
date: 2018-05-13 09:48:02
tags: node
---
node 多进程基础学习及demo
<!-- more -->
## 基础概念
### cluster
`nodejs`在单个进程中运行单个实例，也就是只会占用一个cpu资源，但是为了进一步榨干计算机性能，想要利用计算机的多核同步计算，就要用到`cluster`,`cluster` 模块允许简单容易的创建共享服务器端口的子进程.
### worker
子进程实例，也叫工作进程，在一个主进程里，可以使用`cluster.workers`来获取Worker对象。在一个工作进程里，可以使用`cluster.worker`来获取Worker对象,执行监听方法等事件,只能在主进程中调用。
### 方法
1. `cluster.fork()` 用来创建子进程，每调用一次，就会生成一个worker的id标识，保存在`cluster.workers`中
2. `exit()` cluster和worker 都有这个方法
    - `cluster.exit`在任何工作进程关闭时，都会触发该方法，所以可以用来监听工作进程活跃状态，并重启工作进程。 
    - `worker.exit`监听特定进程是否关闭

```javascript
cluster.on('exit', (worker, code, signal) => {
  console.log('worker %d died (%s). restarting...',
              worker.process.pid, signal || code);
  cluster.fork();
});
```
```javascript
const worker = cluster.fork();
worker.on('exit', (code, signal) => {
  if (signal) {
    // 引发进程被kill的信号名称（如'SIGHUP'）.
    console.log(`worker was killed by signal: ${signal}`);
  } else if (code !== 0) {
    //若正常退出，表示退出代码.
    console.log(`worker exited with error code: ${code}`);
  } else {
    console.log('worker success!');
  }
});
```
3. `send()` 用于主进程和子进程间的信息交换。主进程调用该方法可以给工作进程发送消息，工作进程调用，给主进程发送消息。
```javascript
if (cluster.isMaster) {
  const worker = cluster.fork();
  worker.send('hi there');

} else if (cluster.isWorker) {
  process.on('message', (msg) => {
    process.send(msg);
  });
}
```
4. 事件监听
比如`message`,`error`,在工作进程中使用监听事件，可以用 `process.on()`,主进程中调用，用`cluster.on('...')`,
在主进程中，监听特定工作进程发送消息给自己，使用`cluster.workers[id].on('message', messageHandler);`
例子
```javascript
const cluster = require('cluster');
const http = require('http');

if (cluster.isMaster) {

  // 跟踪 http 请求
  let numReqs = 0;
  setInterval(() => {
    console.log(`numReqs = ${numReqs}`);
  }, 1000);

  // 计算请求数目
  function messageHandler(msg) {
    if (msg.cmd && msg.cmd === 'notifyRequest') {
      numReqs += 1;
    }
  }

  // 启动 worker 并监听包含 notifyRequest 的消息
  const numCPUs = require('os').cpus().length;
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }

  for (const id in cluster.workers) {
    cluster.workers[id].on('message', messageHandler);
  }

} else {

  // Worker 进程有一个http服务器
  http.Server((req, res) => {
    res.writeHead(200);
    res.end('hello world\n');

    // 通知 master 进程接收到了请求
    process.send({ cmd: 'notifyRequest' });
  }).listen(8000);
}
```

## demo实例
1. 多进程简单实现

```javascript
console.log('####====START====###');
const cluster = require('cluster');
const numCPUs = require('os').cpus().length;
function fibo(n) {
  return n == 0 ? 0 : n > 1 ? fibo(n - 1) + fibo(n - 2) : 1;
}
 
if (cluster.isMaster) {
 var collection = [44, 42, 42, 43];
 var st = Date.now();
 for (var i = 0; i < Math.min(numCPUs, collection.length); i++) {
 var wk = cluster.fork();
 wk.send(collection[i]);
 }
 cluster.on('fork', function (worker) {
 console.log(`[master] : fork worker ${worker.id}`);
 });
 cluster.on('exit', function (worker, code, signal) {
 console.log(`[master] : worker ${worker.id} died`);
 });
 var numOfCompelete = 0
 Object.keys(cluster.workers).forEach(function (id) {
 cluster.workers[id].on('message', function (msg) {
 console.log(`[master] receive message from [worker ${id}]: ${msg}`);
 numOfCompelete++;
 if (numOfCompelete === collection.length) {
 console.log(`[master] finish all work and using ${Date.now() -
 st} ms`);
 cluster.disconnect();
 }
 });
 });
 
} else {
 process.on('message', function (msg) {
  console.log(`[worker ${cluster.worker.id}] receive msg ${msg}`);
   
 var st = Date.now();
 var result = fibo(msg);
 console.log(`[worker ${cluster.worker.id}] finish work and using
 ${Date.now() - st} ms`);
 process.send(result);
 });
}

```
这个例子完成了创建多进程，子进程执行完成后和主进程通信的功能。
1. 主进程：主进程监听了`fork, exit`方法，创建工作进程后，通过`message`方法监听每个进程发送给自己的消息。当所有工作进程都发送消息给主进程后，主进程关闭进程。
2. 工作进程： 监听消息，执行任务，并将执行结果send给主进程。执行完任务后，工作进程会正常退出。

本例子只是用于学习理解的简单demo,还存在很多问题,代码执行后会发现`####====START====###`会执行4次，不是我们想要的结果。后续下篇继续。

## 参考
[Node.js 多进程实战](https://www.ibm.com/developerworks/cn/opensource/os-cn-nodejs-practice/index.html)

