---
title: hexo categories和tags页面不显示解决办法
date: 2020-01-12 17:22:40
tags: 前端
categories: Hexo
---

## 解决 hexo categories 和 tags 页面不显示解决办法

### 第一步 需要新建 tags 和 categories 页面

在终端 powershell 下，确保此时是在 hexo 目录下，命令行输入

```bash
hexo new page tags //新建tags页面
```

然后会在 hexo 下的 source 目录看到 tags 文件夹，在 index.md 里输入

```bash
---
title: tags
date: 2020-01-10 16:14:33
type: "tags"
layout: "tags"   #增加tags的布局
---
```

然后再用同样的方法，新建一个 categories

```bash
hexo new page categories //新建categories页面
```

然后配置 categories 文件夹下的 index.md

```bash
---
title: categories
date: 2020-01-10 16:15:43
type: "categories"
layout: "categories"    #增加categories的布局
---
```

### 第二步 给自己的帖子新加配置,增加 tags 和 categories

例如我自己的一篇博客

```bash
---
title: hexo categories和tags页面不显示解决办法
date: 2020-01-12 17:22:40
tags: 前端
categories: Hexo
---
```

但是就这样部署到 github 上是，访问自己的域名服务器，打开分类界面和标签界面都是 404

### 解决方法

打开 theme 目录下的主题配置文件\_config.yml,按 ctrl-f，搜索 menu:，然后把/后面的空格去掉（刚开始的时候默认后面是加了空格所以导致的点击页面出现的 404）

```bash
menu:
   #这/后面的空格得去掉，否则点击访问标签页和分类页的时候会404报错
  home: /|| home #首页
  #about: /about/ || user
  tags: /tags/|| tags
  categories: /categories/|| th
  archives: /archives/|| archive
```

## 给自己的 post 中的代码块增加一键复制功能

### 第一步 下载 clipboard.js

下载第三方插件[clipboard.js](https://raw.githubusercontent.com/zenorocha/clipboard.js/master/dist/clipboard.min.js)，打开之后，右键另存为，保存文件再 theme/next/source/js/src 目录下

### 第二步 clipboard.js 的使用

也是在 theme/next/source/js/src 目录下，创建 clipboard-use.js，添加内容如下：

```bash
/*页面载入完成后，创建复制按钮*/
!function (e, t, a) {
  /* code */
  var initCopyCode = function(){
    var copyHtml = '';
    copyHtml += '<button class="btn-copy" data-clipboard-snippet="">';
    copyHtml += '<span>复制</span>';
    copyHtml += '</button>';
    $(".highlight .code pre").before(copyHtml);
    new ClipboardJS('.btn-copy', {
        target: function(trigger) {
            return trigger.nextElementSibling;
        }
    });
  }
  initCopyCode();
}(window, document);
```

然后再 theme/next/source/css/\_custom/custom.styl 样式中添加如下代码：

```bash
//代码块复制按钮
.highlight{
  //方便copy代码按钮（btn-copy）的定位
  position: relative;
}
.btn-copy {
    display: inline-block;
    cursor: pointer;
    background-color: #eee;
    background-image: linear-gradient(#fcfcfc,#eee);
    border: 1px solid #d5d5d5;
    border-radius: 3px;
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
    -webkit-appearance: none;
    font-size: 13px;
    font-weight: 700;
    line-height: 20px;
    color: #333;
    -webkit-transition: opacity .3s ease-in-out;
    -o-transition: opacity .3s ease-in-out;
    transition: opacity .3s ease-in-out;
    padding: 2px 6px;
    position: absolute;
    right: 5px;
    top: 5px;
    opacity: 0;
}
.btn-copy span {
    margin-left: 5px;
}
.highlight:hover .btn-copy{
  opacity: 1;
}
```

### 第三步 引用

在 theme/next/layout/\_layout.swig 文件中引用(/body)结束标签之前添加：

```bash
<!-- 代码块复制功能 -->
<script type="text/javascript" src="/js/src/clipboard.min.js"></script>
<script type="text/javascript" src="/js/src/clipboard-use.js"></script>
```

### 第四步 运行

重新 执行如下指令：

```bash
hexo clean //清理之前的文件
hexo g //生成
hexo s //跑在本地
```

打开本地 localhost:4000，看复制功能是否可以正常实现。

## 关于解决 hexo 不兼容 {% raw%} {{}} {{% endraw%}}的问题

http://xcoding.tech/2020/01/18/hexo/%E5%A6%82%E4%BD%95%E4%BB%8E%E6%A0%B9%E6%9C%AC%E8%A7%A3%E5%86%B3hexo%E4%B8%8D%E5%85%BC%E5%AE%B9%7B%7B%7D%7D%E6%A0%87%E7%AD%BE%E9%97%AE%E9%A2%98/
