---
title: 初始化iview项目
date: 2020-05-21 19:05:04
tags: 前端
categories: 前端
---
## 前言
最近公司提供的技术栈里面，包括前端样式框架iview，在逛官网的时候，发现官网给的项目里面有些坑，在面向百度开发之后，终于把项目跑起来了.

## 主要操作
* 把远程仓库的项目克隆下来
```
git clone https://github.com/view-design/view-ui-project.git
```
* 下载依赖包
```
npm  install //下载得到node_modules
```
* 初始化项目
```
npm run init
```
但是有可能会出现报错`ERR_INVALID_CALLBACK`的情况，因为node版本的原因。

`node v10` 以上 `fs.write 的callback `是必须的，降低Node版本可解决。
不安装node也可以，可以将`webpack.dev.config.js` 和 `webpack.prod.config.js` 中的代码修改即可：给`fs.write`添加必要的callback函数，具体操作是在以上两个文件中找到下面这行代码：
```
fs.write(fd, buf, 0, buf.length, 0, function(err, written, buffer) {});
```
修改为：
```
fs.write(fd, buf, 0, 'utf-8', function(err, written, buffer) {});
```
然后重新` npm run init` 即可.

需要记住的是，` npm run init` 之后的项目结构多了`dist`目录和`index.html`两个

* 把项目跑起来
```
npm run dev
```
这里需要注意的点是，iview官网中提供了一种可以按需导入iview组件的方式，但是这样按需导入的方式跟全局导入的方式是不可以一起使用的，所以要么使用全局引用，要么使用按需导入。

* 全局引入

```
import ViewUI from 'view-design';
import 'view-design/dist/styles/iview.css';

Vue.use(ViewUI);
```
删除`.babelrc`配置文件中的
```
  // "plugins": [
  //   [
  //     "import",
  //     {
  //       "libraryName": "view-design",
  //       "libraryDirectory": "src/components"
  //     }
  //   ]
  // ]
  ```
  * 按需导入
  删除前面全局引入的方式，变成按需导入
  ```
  import { Button, Table } from 'iview';
  Vue.component('Button', Button);
  Vue.component('Table', Table);
```

然后把 `.babelrc` 中删掉的重新复用
这个插件使用的前提是先需要 `npm install babel-plugin-import --save-dev`
```
  "plugins": [
	//......
    [
      "import",
      {
        "libraryName": "iview",
        "libraryDirectory": "src/components"
      }
    ]
  ] //babel插件
  ```

