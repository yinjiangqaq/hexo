---
title: iview中的upload组件怎么拿到file对象的内容
date: 2020-07-15 00:15:21
tags: javascript iview
categories: javascript iview
---
## 前言
在项目开发过程中有个需求，是需要一个上传文件的组件，而前端上传文件的组件大家都知道`<input type="file"/>`来实现上传文件，但是原生的样式不得行，所以采用了iview中的upload组件，但是问题来了，怎么读取file对象的内容呢。

## input
`<input type="file"/>`读取file对象的内容需要通过`FileReader`来实现
```
<input type="file" webkitdirectory directory multiple onchange="jsReadFiles(this.files)"/>

function jsReadFiles(files) {
        if (files.length) {
            for (let i in files) {
                let file = files[i];
                console.log(file);
                let reader = new FileReader();//new一个FileReader实例
                if (/javascript+/.test(file.type)) {//判断文件类型，是不是JavaScript类型
                    reader.onload = function () {
                       console.log(reader.result)
                    };
                    reader.readAsText(file);
                }
            }
        }
    }
```

## upload
参照上面的原理，我们实际上就是监听到文件的加载时，给他加一个filereader，文件加载完毕之后，便可调用 `reader.readAsText(file);`把文件内容读出来。既然这样我们可以使用`upload`中的`before-upload`属性给他一个函数
```
  handleUpload(file) {
      console.log(file);
      var reader = new FileReader();
      reader.addEventListener(
        "load",
        () => {
          console.log(reader.result);
          this.leftEditor.getModel().setValue(reader.result);
        },
//变成箭头函数是为了拿到外层的this,在vue项目中方面给data中的属性赋值
        false
      );
      reader.readAsText(file);
    },
```
通过这样，我们可以读到文件的内容。
