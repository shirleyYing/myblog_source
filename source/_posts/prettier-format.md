title: Prettier+VScode 治好你的代码洁癖
date: 2018-10-01 16:16:53
tags: JSformat Prettier

---

试想一个多人开发的项目，每次同步代码，看到各个风格迥异，换行空格混乱，4 格，2 格缩进交替上演的代码文件，分分钟逼死强迫症啊。忍无可忍只能拔枪相见了~~。统一的代码风格规范，对于多人开发的大项目一定是必不可少的，但是口头约定又是一定没有用的（nice 脸）。这时候就需要 Prettier 上场了，一个配置文件，配合 VScode，保存即格式化，你的同事都不需要知道代码风格是什么，，整个团队只要 clone 项目，自动保持统一风格。搞定！

<!-- more -->

## Prettier 是什么

> Prettier is an opinionated code formatter. It enforces a consistent style by parsing your code and re-printing it with its own rules that take the maximum line length into account, wrapping code when necessary.

以上是 github 上的官方解释，其实就是简单的代码格式工具，和 esLint 不同在于，ESLint 只是一个代码质量工具(确保没有未使用的变量、没有全局变量，等等)。而 Prettier 只关心格式化文件(最大长度、混合标签和空格、引用样式等)。可见，代码格式统一的问题，交给 Prettirer 再合适不过了。和 Eslint 配合使用，风味更佳。

## Prettier 怎么用

- 编辑器插件
- CLI 命令行

### 编辑器

这里介绍 VSCode 中如何配置 Prettier

#### 1.安装 Prettier 插件 [Prettier - Code formatter][1]

安装插件后，调出系统设置就能看到 prettier 相关选项了，如图
![prettier配置项](/imgs/prettier.jpeg)

#### 2.添加自定义配置文件.prettierrc

作为项目的整体代码规范，如果依赖本地配置，显然是不科学的，所以需要`.prettierrc` 文件覆盖本地配置。我的配置文件如下

```
  {
    "singleQuote": true,
    "trailingComma": "es5",
    "printWidth": 140,
    "overrides": [
      {
        "files": ".prettierrc",
        "options": { "parser": "json" }
      }]
  }
```

到此，prettier 安装完毕，使用`shift+alt+f`就可格式化代码。当然每次手动格式化还是很费力啊，怎么办？自动保存。系统设置中增加`"editor.formatOnSave": true`即可自动保存，还要注意的一点是，如果同时设置了`"files.autoSave": "autoSaveDelay",`保存及格式化会失效。`files.autoSave`配置成别的选项即可。

#### 3.`.editorConfig`文件与`.prettierrc` 文件同时存在

另外，如果项目配置了`.editorConfig`文件，在配置了`"editor.formatOnSave": true`后，如果项目成员没有安装 Prettier 插件，保存时就会读取`.editorConfig`文件，同样可以格式化代码。启用 Prettier 插件后，`.editorConfig`的配置就会失效，读取`.prettierrc` 文件的配置

### 命令行

命令行用法需要安装 prettier,`npm install prettier --save-dev`，使用`prettier write **.js` 就可以格式化文件。将添加到你的 NPM 脚本中，`prettier --write './src/**/*.js`,这样就可以批量格式化项目中的所有文件了。

## 结语

Prettier 只关心代码格式，显然是不够的。项目中还是要引入 ESlint。两者配合才能使项目代码优雅健壮。

[1]: https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode
