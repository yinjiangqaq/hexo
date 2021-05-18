---
title: Node--Gulp基于流的自动化构建工具
date: 2020-04-06 14:46:19
tags: Node
categories: Node
---
## 什么是Gulp
**一个服务于前端的管道式构建系统**  
Gulp是基于流的构建系统，我们可以通过对这些流的引导来创建构建过程，除了**转译**和**缩码**，还能做更多的事情。  
它有这些优点：
* 易于使用：通过代码优于配置的策略，Gulp让简单的任务更简单，复杂的任务可管理
* 构建快速： 利用Node.js流的威力，你可以快速的构建项目并减少频繁的IO操作。
* 插件高质： Gulp严格的插件指南确保插件如你期望的那样简洁高效地工作。
* 易于学习： 通过最少的API，掌握Gulp毫不费力。
* 高度重用： 主要归功于两项技术，**使用插件和自定义构建任务**。Gulp的过程是一个流，所以这些任务插件是可以一个接一个拼在一起的。




## 使用Gulp
首先我们需要把Gulp添加到项目中，添加Gulp需要用`npm`安装`gulp-cli`和`gulp`两个包。下面这段代码中，全局安装`gulp-cli`，并创建一个带有Gulp开发依赖项的新node项目.
```
npm i --g gulp-cli
npm init -y
npm i -save-dev gulp
```
接着创建`gulpfile.js`
```
touch gulpfile.js
```
`touch为linux指令`
我们用Gulp构建一个小型的React项目，我们会用到`gulp-babel`、`gulp-sourcemaps`和`gulp-concat`:
```
npm i --save-dev gulp-sourcemaps gulp-babel babel-preset-es2015
npm i --save-dev gulp-concat react react-dom babel-preset-react
```
这里注意的是，安装`gulp-babel`的时候要安装7版本的，后续还要安装`babel-core`。如果直接安装，是安装8版本，`babel-core`是7，就会出错。
```
npm i gulp-babel@7 babel-core --save-dev
```

这其中`sourcemap`的作用着重讲一下  
sourcemap解决的问题：当我们使用webpack打包代码出错时，如果不用sourcemap，我们只能知道**打包后的代码第几行出错**，并不知道**对应的源代码错在哪里**，所以source的作用是做一个**源代码和目标代码的映射**，即可以通过打包后的（目标）代码错在哪里**定位到错误的源代码的位置**


## Gulp任务的创建及运行
创建Gulp任务需要在`gulpfile.js`中编写Node代码，调用Gulp的API。Gulp的API可以做很多的事情，比如查找文件，把文件进行某种转换的插件频道一起等。

打开`gulpfile.js`设置一个构建任务，用`gulp.src`查找JSX文件，用`babel`处理ES6和React,然后把这些文件拼到一起。

`gulpfile.js`
```
const gulp=require('gulp');
const sourcemaps=require('gulp-sourcemaps');
const babel=require('gulp-babel');
const concat=require('gulp-concat');

gulp.task('default',()=>{
    return gulp.src('app/*.jsx')
        .pipe(sourcemaps.init())
        .pipe(babel({
            presets:['es2015','react']
        }))
        .pipe(concat('all.js'))
        .pipe(sourcemaps.write('.'))
        .pipe(gulp.dest('dist'));
})
```
* [gulp.src()](https://www.gulpjs.com.cn/docs/api/src/): Gulp自带的文件聚集工具，查找app目录下所有的React jsx文件。
* sourcemaps.init()： 开始监控源文件，为调试构建源码映射
* concat('all.js'):把所有源码文件拼到一个all.js中
* sourcemaps.write('.'): 单独写入源码映射文件
* gulp.dest('dist'): 将所有文件放到dist/目录下
* 那些babel插件是在把源码拼到到一个all.js文件之前，加一个管道，添加一些babel插件做一些处理，例如ES6转ES5

然后在终端运行` gulp`，我们会看到
```
[21:13:00] Using gulpfile D:\Node_learn\test_Gulp\gulpfile.js
[21:13:00] Starting 'default'...
[21:13:02] Finished 'default' after 1.82 s
```
gulp构建完成之后我们会看到我们的项目结构多了dist目录，dist目录下多了`all.js`和`all.js.map`文件标识构建成功

all.js文件的内容
```
'use strict';

var _react = require('react');

var _react2 = _interopRequireDefault(_react);

var _reactDom = require('react-dom');

var _reactDom2 = _interopRequireDefault(_reactDom);

function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; }

ReactDOM.render(_react2.default.createElement(
    'h1',
    null,
    'Hello,world'
), document.getElementById('example'));
//# sourceMappingURL=all.js.map
```


## 总结
在Gulp中，用JavaScript表示构建阶段很容易。并且我们可以用gulp.task()往这个文件里添加自己的任务。这些任务通常都遵循相同的模式。
* 源文件： 收集输入文件，`gulp.src()`
* 转译： 让他们依次通过一个个对他们继续进行转换的插件，例如babel插件，这转译是发生在合并之前的，这合并的pipe之前增加这些插件的pipe
* 合并： 把这些文件合到一起，创建一个整体构建文件
* 输出： 设定文件的目标地址或移动输出文件

前面那个例子中，`sourcemaps`是个特例，因为它需要两次`pipe`：第一次是配置，最后一次是输出文件

## 参考
https://delaprada.com/2020/04/03/Node%E2%80%94%E2%80%94Gulp%E5%9F%BA%E4%BA%8E%E6%B5%81%E7%9A%84%E8%87%AA%E5%8A%A8%E5%8C%96%E6%9E%84%E5%BB%BA%E5%B7%A5%E5%85%B7/#more