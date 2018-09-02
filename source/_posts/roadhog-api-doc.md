title: roadhog-api-doc 玩法介绍
date: 2018-09-02 11:45:23
tags: apiest roadhog-api-doc
---
接口测试好用工具，roadhog-api-doc 用法介绍
<!-- more -->

最近工作中遇到了排期比较紧张的项目，前后端分离并行开发成为必然选择。所谓前后端分离，即约定好一套接口标准，前后端各自独立开发，就不会被对方的技术难点给阻塞住，从而保证项目进度。这样的并行开发看似美好，实际使用起来也遇到了不少痛点:

1. 在联调测试中，接口不稳定，甚至约定接口字段变化的现象时有发生。直接反应是页面报错或显示不正常，比较难快速定位问题。
2. 约定的接口总是开后端开发自己约定，只言片语或零散的json文件，不能形成文档，不利于维护与交接。

项目主要使用了组件库`ant-design`.以及基于此的中台前端解决方案 `ant-design-pro`. 该解决方案底层工具使用`roadhog`，该工具除了包装webpack，webpack-dev-server 完成构建,和开发中的自动同步构建，还增加了代理转发的功能，通过代理请求就能够轻松处理数据模拟的功能。使得在项目中可以轻松集成模拟数据。

当然只是使用模拟数据还是不够的，使用`roadhog-api-doc`自动生成文档工具，通过读取假数据配置文件，可以自动生成文档。恰当使用配置，指定ip，端口，还可以在文档上直接发起请求访问真实数据，自己写个循环脚本就可以批量测试接口啦。糟糕~ 是心动的感觉。

前戏这么久，终于进入正题了。下面进入使用攻略环节。

### 基本使用
`roadhog-api-doc` 主要是靠读取 `.roadhog.mock.js`文件生成文档。所有的mock数据都要写在这里。形式如下
```javascript
import { postRule } from './mock/rule';
import { format } from 'roadhog-api-doc';
const proxy = {
  'GET /api/currentUser': {
    // 接口描述
    $desc: "获取当前用户接口",
    // 接口参数
    $params: {
      pageSize: {
        desc: '分页',
        exp: 2,
      },
    },
    // 接口返回
    $body: {
      name: 'momo.zxy',
      avatar: imgMap.user,
      userid: '00000001',
      notifyCount: 12,
    },
  },
  'POST /api/rule': {
    $params: {
      pageSize: {
        desc: '分页',
        exp: 2,
      },
    },
    $body: postRule,
  },
};

// format 函数用于保证本地的模拟正常，如果写了文档，这个函数转换是必要的
export default format(proxy);

```
生成结果如下
![api-doc](/imgs/roadhog-api-doc.png)

文档中请求地址，参数，一应俱全。通过在mock接口中增加描述字段，就可以直接在文档中显示了。点击 send 按钮,及发送请求。弹框输出请求结果
使用 `roadhog-api-doc start [port] ` 即启动服务，指定端口，相当于开发环境，自动监控文件变化刷新页面
使用 `roadhog-api-doc build [port] ` 构建文档，输出到dist目录，生成 api.html, api.js, api.css 三个文件


### 进阶使用
输出了api文档，但是还想要请求真实环境地址，集中测试接口怎么办？
看了源码后发现，请求地址的ip部分写死了 `localhost`,只有端口可变。在`lib/`目录下，源码如下
```javascript
 const configContent = `
  export default {
    port: "${projectServerPort}",
    docPort: "${port}",
    isStatic: ${isStatic},
  }
  `;

    fs.writeFileSync(path.join(tempDir, './src/config.js'), configContent, 'utf-8');

    if (projectServerPort) {
      const mockjsContent = `
export default {
  'GET /api/*': 'http://localhost:${projectServerPort}/',
  'POST /api/*': 'http://localhost:${projectServerPort}/'
};
  `;
      fs.writeFileSync(path.join(tempDir, './.roadhogrc.mock.js'), mockjsContent, 'utf-8');
    }
```
`projectServerPort`是命令行输入的端口号，我们想要修改ip，需要修改一下源码。将localhost 改为参数，在命令行中输入，指定IP和端口号，就可以自由发起请求了。
修改代码

```javascript
const mockjsContent = `
export default {
  'GET /api/*': '${projectIP}:${projectServerPort}/',
  'POST /api/*': '${projectIP}:${projectServerPort}/'
};
```
启动命令改为
`roadhog-api-doc start [port] [ip] `
`roadhog-api-doc build [port] [ip] `

需要注意的是，start命令和build命令分别对应两个执行文件 start.js build.js 两个文件都要修改。

### 总结
`roadhog-api-doc` 的实现原理很简单，就是靠读取配置文件实现一个简单的页面。在`ant-design-pro`中，配合`roadhog`一起使用,可以使用一套mock数据，既可用于前端开发，又可生成接口文档。一次修改，两边同步，非常便捷。不用特别维护接口文档，为开发省了不少力气，也避免了接口文档因为年久失修而被丢弃的命运。



