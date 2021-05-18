---
title: iframe中的跨域问题
date: 2020-07-19 23:23:51
tags: iframe
categories: iframe
---

## 同域

我们知道，浏览器处于安全考虑是支持同源策略的，当协议、域名、端口不同时，称为跨域。
而浏览器的同源策略是**第一种**限制就是不能通过 ajax 的方法去请求不同源的文档。**第二种**限制是不能浏览器中不同域的框架(iframe)之间是不能进行 js 的交互操作的。

而 iframe 技术，其实就是在一个 window 下，开另外一个 window。而同域情况下，才是可以访问得到各自的属性和方法的

## 跨域

iframe 的跨域：不同框架之间（父子框架和同辈框架），是能够获取到彼此的 window 对象的，但是却不能获取到 window 对象的属性和方法（html5 中的的`postMessage`方法是一个例外，还有些浏览器比如 ie6 也可以使用 top、parent 等少数几个属性），总之，你可以当做是只能获取到一个几乎无用的 window 对象。比如，有一个页面，它的地址是`http://wwww.example.com/a.html`, 在这个页面里边有一个 iframe，它的 src 是`http://example.com/b.html`,很显然，这个页面与它里边的 iframe 框架是不同域的，所以我们是无法通过在页面中书写 js 代码来获取 iframe 中的东西的，同样 iframe 中的内容也无法直接获取到 a.html 中的内容。

而不同域下的 iframe 怎么信息交互呢

### 通过修改 document.domain 来跨子域

子域无法获取父级域的数据时，可以利用`document.domain`都设置成相同的域名,但是要注意的是，`document.domain`的设置是有限制的，我们把`document.domain`设置成自身或者更高一级的父域，且主域必须相同。例如：`a.b.example.com`中某个文档的`document.domain`可以设成`a.b.example.com`、`b.example.com`、`example.com`中的任意一个，但是不可以设成`c.a.b.example.com`，因为这是当前作用于的子域，也不能设成`baidu.com`，因为主域已经不相同了。

总结起来一是不能设置为更下级的子域，而是不能改变主域。

```
现在是子域拿到父级域的数据
//父域的运行环境是http://localhost:8087/
//同样在部署在同一台服务器上的不同端口的应用也是适用的

<iframe src="http://localhost:8086/" id="iframepage" width="100%" height="100%" frameborder="0" scrolling="yes" onLoad="getData"></iframe>

<script>
    window.parentDate = {
        "name": "hello world!",
        "age": 18
    }
    /**
     * 使用document.domain解决iframe父子模块跨域的问题
     */
    let parentDomain = window.location.hostname;
    console.log("domain",parentDomain); //localhost
    document.domain = parentDomain;
</script>

//子域的运行环境是http://localhost:8086/

<script>
    /**
     * 使用document.domain解决iframe父子模块跨域的问题
     */
    console.log(document.domain); //localhost
    let childDomain = document.domain;
    document.domain = childDomain;
    let parentDate = top.parentDate;//父级的parentDate
    console.log("从父域获取到的数据",parentDate);
    // 此处打印数据为
    // {
    //     "name": "hello world!",
    //     "age": 18
    // }
</script>
```

父级拿到子域的数据

```
//子域的获取到top之后给top上添加属性

<script>
    let childDomain = document.domain;
    document.domain = childDomain;

    top.childData = {   //获取到top之后给top添加属性
        "name": "你好世界！",
        "age": 26
    }
</script>




//父域在iframe加载完成之后就可以获取到子域添加的属性

<iframe src="http://localhost:8086/" id="iframepage" width="100%" height="100%" frameborder="0" scrolling="yes" onLoad="getData"></iframe>

<script>
    getData(){
        console.log("子域传递给父域的数据",top.childData);
        // 此处打印数据
        // {
        //      "name": "你好世界！",
        //      "age": 26
        // }
    }
</script>
```

不过如果你想在`http://www.example.com/a.html`页面中通过 ajax 直接请求`http://example.com/b.html`页面，即使你设置了相同的`document.domain`也还是不行的，所以修改`document.domain`的方法只适用于不同子域的框架间(iframe)的交互。

### window.postMessage

父页面
`window.postMessage(message, targetOrgin)`方法是 html5 新引进的特性，可以使用它来想其他的 window 对象发送消息，无论这个 window 对象是属于同源或者不同源，目前`IE8+、FireFox、Chrome、Opera`等浏览器都已经支持 window.postMessage 方法。

调用`postMessage`方法的`window`对象是指要 j 接受消息的那一个`window`对象，该方法的第一个参数`message`为要发送的消息，类型只能为字符串；第二个参数`targetOrgin`用来限定接收消息的那个 window 对象所在的域，如果不想限定域，可以使用通配符\*。

需要接收消息的`window`对象，可是通过监听自身的`message`时间来获取传过来的消息，消息内容存储在该事件对象的`data`属性中。

上面所有的向其他`window`对象发送消息，其中就是指一个页面有几个框架的那种情况，因为每一个框架都有一个 window 对象。在讨论第二种方法的时候，我们说过，不同域的框架间是可以获取到对象的`window`对象的，而且也可以使用`window.postMessage`这个方法

```
    /**
     * 使用postMessage解决iframe父子模块跨域的问题
     */
//父域的运行环境是http://localhost:8087/

<iframe src="http://127.0.0.1:8086/" id="iframepage" width="100%" height="100%" frameborder="0" scrolling="yes" onLoad="getData"></iframe>

<script>
    getData(){
        let parentDate={
             "name": "hello world!",
             "age": 18
            }
        let iframe = document.getElementById('iframepage');
        let win = iframe.contentWindow;
        win.postMessage(JSON.stringify(parentDate),"*");
    }
</script>
//或者自己在项目中实现的，为的是说明postMessage的调用者是接收信息的那个window对象，在这里也就是iframe
      var diff = document.createElement("iframe");
      diff.style.height = "95%";
      diff.style.width = "100%";
      diff.src = "http://local.huya-diff.info:8081/";
      diff.frameBorder = "no";
      diff.onload = () => {
        let data = {
          left: this.left,
          right: this.right,
          type: this.type,
        };
        diff.contentWindow.postMessage(JSON.stringify(data), "*");
      };



//子域的运行环境是http://127.0.0.1:8086/


    window.onmessage = function(e){
        e = e || event;
        console.log("从父域获取到的数据",JSON.parse(e.data));
        // 此处打印的数据为
        // {
        //     "name": "hello world!",
        //     "age": 18
        // }
    }
```

## 总结

跨域的总类分两种，一种 iframe 跨域，解决办法为`document.domain`和`window.postMessage`。另一种是 ajax 请求不同源导致的跨域,解决办法，`jsonp`,`cors`,`node端作代理`
