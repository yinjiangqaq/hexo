---
title: Node的串行化和并行化流程控制
date: 2020-04-06 21:58:04
tags: Node
categories: Node
---

## 异步逻辑顺序化
在异步程序中，有些任务可能是随时发生的，跟程序中其他部分在做什么没关系，什么时候做这些任务都不会出问题。但有些任务只能在某些特定的任务的之前或之后做。  
让一组异步任务的执行任务按顺序执行的概念成为**流程控制**。这种控制分为**串行**和**并行**   
串行任务： 需要一个任务接着一个任务
并行任务： 不需要一个接着一个执行的任务。这些任务彼此之间开始和结束时间并不重要，**但在后续逻辑执行前它们应该全部执行完成**。
## 实现串行化流程控制
原理：  
为了让一组异步任务按顺序执行，需要把这些任务按预期的执行顺序放到一个数组中去。这个数组起到队列的作用，完成一个任务之后按顺序从数组中取出下一个。

### 串行化的实现实例
做一个小程序，让他从一个随机选择的RSS预定源中获取一篇文章的标题和URL，并显示出来。RSS预定源列表放在一个文本文件中。

初始化项目：  
新建一个一个项目文件夹，然后用vscode打开文件夹，在powershell输入：
```
npm init -y
npm i request --save-dev
npm i htmlparser --save-dev
```
request 模块是一个经过简化的` HTTP` 客户端，这里用它来获取` RSS` 数据。  
htmlparser 模块能把原始的 RSS 数据转换成 `JavaScript` 数据结构。  

新建一个txt文本文件
输入的RSS预定源列表`rss_feeds.txt`
```
http://blog.nodejs.org/feed/
http://blog.npmjs.org/rss
```
新建一个index.js文件  
```
'use strict';

const fs = require('fs');
const path = require('path');
const request = require('request');
const htmlparser = require('htmlparser');
const configFilename = path.join(__dirname, './rss_feeds.txt');

// 【任务一】确保包含 RSS 预定源 URL 列表的文件存在
function checkForRSSFile() {
    fs.exists(configFilename, (exists) => {
        if (!exists)
        // 只要有错误就尽早返回
            return next(new Error(`Missing RSS file: ${configFilename}`));
        next(null, configFilename);
    })
}

// 【任务二】读取并解析包含预定源 URL 的文件
function readRSSFile(configFilename) {
    fs.readFile(configFilename, (err, feedList) => {
        if (err) return next(err);
        // 将预定源 URL 列表转换成字符串，然后分隔成一个数组
        feedList = feedList
            .toString()
            .replace(/^\s+|\s+$/g, '') // 字符串替换
            .split('\n'); // split() 方法用于把一个字符串分割成字符串数组。按（\n）分割字符串
        // 从预定源 URL 数组中随机选择一个预定源 URL
        console.log(feedList)
        const random = Math.floor(Math.random() * feedList.length);
        next(null, feedList[random]);
    })
}

// 【任务三】向选定的预定源发送 HTTP 请求以获取数据
function downloadRSSFeed(feedUrl) {
    request({ uri: feedUrl }, (err, res, body) => {
        if (err) return next(err);
        if (res.statusCode !== 200)
            return next(new Error('Abnormal response status code'));
        next(null, body);
    })
}

// 【任务四】将预定源数据解析到一个条目数组中
function parseRSSFeed(rss) {
    const handler = new htmlparser.RssHandler();
    const parser = new htmlparser.Parser(handler);
    parser.parseComplete(rss);
    if (!handler.dom.items.length)
        return next(new Error('No RSS items found'));
    // 如果有数据，显示第一个预定源条目的标题和 URL
    const item = handler.dom.items.shift();
    console.log(item.title);
    console.log(item.link);
}

// 把所有要做的任务按执行顺序添加到一个数组中
const tasks = [
    checkForRSSFile,
    readRSSFile,
    downloadRSSFeed,
    parseRSSFeed,
];

// 负责执行任务的 next 函数
function next(err, result) {
    if (err) throw err; // 如果任务出错，则中断程序，抛出异常。
    // shift()：把数组的第一个元素从其中删除，并返回第一个元素的值。
    const currentTask = tasks.shift(); // 从任务数组中取出下一个任务。
    if (currentTask) {
        currentTask(result); // 执行当前任务
    }
}

next(); // 开始任务的串行化执行。
```
然后在终端输入`node index.js`指令，我们可以看到输出：
```
![CDATA[Changes to Release Schedule]]
https://nodejs.org/en/blog/announcements/adjusted-release-schedule-covid
```
## 实现并行化流程控制
原理：  
为了让异步任务并行执行，仍然是要把任务放到**数组**中，但任务的**存放顺序无关紧要**。每个任务都应该调用处理器函数增加已完成任务的计数值。当**所有任务都完成**后，处理器函数**应该执行后续的逻辑**。
### 并行化流程控制的实例
实例程序的功能： 读取几个文本文件的内容，并输出单词在整个文件中出现的次数。

同样先初始化项目,新建另一个项目，打开终端，输入如下命令行
```
npm init -y
```
建立如下的目录结构：
![project.png](Node的串行化和并行化流程控制/project.png)
`1.txt`
```
how many offers do you have
```
2.txt
```
i have no offer
```
`word_count.js`
```
'use strict';

const fs = require('fs');
const path = require('path');
const tasks = [];
const wordCounts = {};
const filesDir = './text';
let completedTasks = 0;

// 检查并行任务是否完成
function checkIfComplete() {
    completedTasks++;
    if (completedTasks === tasks.length) {
        // 当所有任务全部完成后，列出文件中用到的每个单词以及用了多少次。
        for (let index in wordCounts) {
            console.log(`${index}: ${wordCounts[index]}`);
        }
    }
}

// 添加单次计数次数
function addWordCount(word) {
    wordCounts[word] = (wordCounts[word]) ? wordCounts[word] + 1 : 1;
}

function countWordsInText(text) {
    const words = text
        .toString()
        .toLowerCase()
        .split(/\W+/)
        .sort();

    words
        .filter(word => word)
        .forEach(word => addWordCount(word)); // 对文本中出现的单次计数

}

fs.readdir(filesDir, (err, files) => { //得出 text 目录中的文件列表
    if (err) throw err;

    files.forEach(file => {
        // 定义处理每个文件的任务。
        // 每个任务中都会调用一个异步读取文件的函数并对文件中使用的单次计数。
        //是立即执行函数，获取filename
        const task = (file => {
            return () => {
                fs.readFile(file, (err, text) => {
                    if (err) throw err;
                    countWordsInText(text);
                    checkIfComplete();
                });
            };
        })(`${filesDir}/${file}`);
        tasks.push(task); // 把所有任务都添加到函数调用数组中。
    });

    tasks.forEach(task => task()); // 开始并行执行所有任务。
});
```
命令行输入` node word_count.js`,得到如下结果
```
have: 2
i: 1
no: 1
offer: 1
do: 1
how: 1
many: 1
offers: 1
you: 1
```

