title: ES6 模块加载机制
date: 2018-01-08 22:53:49
tags: ES6模块加载机制
---
近来在工作中需要使用angular，并且使用了一个表格组件，对，juqery的插件datatable，遇到了文件引用问题。才发现其实自己还没有很了解ES6。趁此机会，又去了解了一次es6模块加载机制，记录如下。
<!-- more -->
## 探究
### 问题
工作中使用了kibana这个 es 的前端库，使用angualr写的。二次开发需要用到表格，就选了`angular-datatable`，就是用angular的写法封装了jquery的datatable 插件。但是，搞不懂angular的逻辑，居然，包了一层还需要自己引入datatable插件。直接import就不管用。运行起来就报datatable找不到的错误。
### 原因
源码中直接使用了datatable组件，这也就是说，angular-datatable默认全局下有datatable对象。这确实是之前传统juqery插件的用法，用script标签引用插件进来，都是挂在window对象上的。但是对于es6模块加载的方式，import的对象都是对当前文件有用的局部对象，所以`import datatables from 'datatables.net'` 的方式，是无效的。需要自己挂载在window对象上。

另外一种方式。就是以文件方式引入 `import '../node_modules/datatables.net'`这种方式会执行改文件，文件中有挂载到window对象上的逻辑，所以只要执行了。也会实现挂载到window对象上的效果。
```
window.datatables = datatables
```

