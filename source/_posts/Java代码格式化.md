---
title: Java代码格式化
date: 2020-07-14 00:17:25
tags:  monaco
categories: monaco
---
## 前言
最近在项目开发中需要用到内嵌IDE，推荐一款比较友好的ide，`Monaco editor`，这个编辑器是vscode使用的编辑器，功能十分的丰富。

但是遇到的问题是我们需要对Java代码进行格式化，而Monaco自身语言是支持Java高亮的，但是并不自带对Java代码的格式化。但是Monaco提供了一个API让我们自己自定义注册一个格式化插件，以此来实现对不同语言的格式化功能。


## 踩坑
首先，Monaco提供的API是：
```
  monaco.languages.registerDocumentRangeFormattingEditProvider("java", {
        provideDocumentRangeFormattingEdits(model) {
          let formatted = js_beautify(model.getValue(), {
            indent_size: 2,
            indent_char: "\t",
          });
          console.log(formatted);
          return [
            {
              range: model.getFullModelRange(),
              text: formatted,
            },
          ];
        },
      });
```
通过上面的代码我们知道，我们其实是通过`registerDocumentRangeFormattingEditProvider`来注册一个格式化provider，这个函数需要两个参数，第一个语言，第二是一个`provideDocumentRangeFormattingEdits`函数，这个函数是实现格式化的所在，原理是通过我们自行下载的格式化包，把`source`源码通过格式化插件转换成格式化后的代码，然后返回。

但是其中被坑到的是，在npm官网上找相关的Java格式化插件，寥寥无几，然后找到了`prettier`，这个格式化插件前端开发的同学比较熟悉，因为这个格式化插件基本自己不用配置便可以方便地使用。但是这个`prettier`本身并不支持Java代码的格式化，需要下载一个`java-format-plugin`，这个插件并不是官方的，是社区版的，而社区版的文档给的很迷，很迷。几经尝试之后，没有成功。

最后觉得快要无望的时候，无聊地试了`js_beautify`这个js代码美化插件，没想到，可以了，真滴没有想到。挺搞siao的。真的把Java代码格式化了。


## 后记
至于Monaco编辑器，更详细地还是参考它的官方文档，而且官网本身也有比较好的`playground`，方便我们快速上手.

https://microsoft.github.io/monaco-editor/
