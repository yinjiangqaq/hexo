---
title: Js中的函数
date: 2020-02-06 13:04:20
tags: JavaScript
categories: JavaScript
---
## 函数柯里化

柯里化概念：只传递给函数一部分参数来调用它，让它返回一个函数去处理剩下的参数   
看一个例子：
```
var add = function(x) {
  return function(y) {
    return x + y;
  };
};

var increment = add(1);
var addTen = add(10);

increment(2);
// 3

addTen(2);
// 12
```
我们定义了一个`add`函数，它接受一个参数并返回一个新的函数。调用了`add`之后，返回的函数就通过闭包的方式记住了`add`的第一个参数。

因为一次性地调用它有点繁琐，所以使用一个特殊的`curry`帮助函数使得这类函数的定义和调用更加容易。

### curry的封装
```
// 初步封装
var currying = function(fn) {
    // args 获取第一个方法内的全部参数
    var args = Array.prototype.slice.call(arguments, 1)
    return function() {
        // 将后面方法里的全部参数和args进行合并
        var newArgs = args.concat(Array.prototype.slice.call(arguments))
        // 把合并后的参数通过apply作为fn的参数并执行
        return fn.apply(this, newArgs)
    }
}
```
这边首先是初步封装,通过闭包把初步参数给保存下来，然后通过获取剩下的`arguments`(这里的arguments是函数function自带的一个隐式参数，表现为所有参数组成的数组)进行拼接，最后执行需要`currying`的函数。

但是好像还有些什么缺陷，这样返回的话其实只能多扩展一个参数，`currying(a)(b)(c)`这样的话，貌似就不支持了（不支持多参数调用），一般这种情况都会想到使用递归再进行封装一层。
```
// 支持多参数传递
function progressCurrying(fn, args) {

    var _this = this
    var len = fn.length;
    var args = args || [];

    return function() {
        var _args = Array.prototype.slice.call(arguments);
        Array.prototype.push.apply(args, _args);

        // 如果参数个数小于最初的fn.length，则递归调用，继续收集参数
        if (_args.length < len) {
            return progressCurrying.call(_this, fn, _args);
        }

        // 参数收集完毕，则执行fn
        return fn.apply(this, _args);
    }
}
```

### 拓展一道经典的面试题
```
// 实现一个add方法，使计算结果能够满足如下预期：
// add(1)(2)(3) = 6;
// add(1, 2, 3)(4) = 10;
// add(1)(2)(3)(4)(5) = 15;

function add() {
    // 第一次执行时，定义一个数组专门用来存储所有的参数
    var _args = Array.prototype.slice.call(arguments);

    // 在内部声明一个函数，利用闭包的特性保存_args并收集所有的参数值
    var _adder = function() {
        console.log("接收了一个()");
        _args.push(...arguments); //...arguments在ES6中表示剩下所有的参数，接受剩下的所有参数
        return _adder; //再返回adder,进行下一次递归，把上次接收的结果作为第一个参数，
        //再接受下一个参数，这里相当于接受下一个()里的参数,直到接收完毕

    };

    // 利用toString隐式转换的特性，当最后执行时隐式转换，并计算最终的值返回
    _adder.toString = function() {
         // return _args.reduce(function(a, b) {
        //     return a + b;
        // });
        return _args.reduce((a, b) => a + b);
        //reduce是ES5中的API，利用其能够遍历到数组的每一个元素，这里的a+b相当于当前所有的参数
    }
    return _adder; //这里相当于返回最后计算好的结果
}

var res = add(1, 2)(1)(2)(5);
console.log(res + '');
console.log(add(1)(2)(3) + '');
/*
接收了一个()
接收了一个()
接收了一个()
11
接收了一个()
接收了一个()
6
*/

```
#### 为什么要进行隐式转化
因为当我们将函数参与其他的计算时，函数会默认调用toString方法，直接将函数体转换为字符串参与计算。
```
function fn() { return 20 }
console.log(fn + 10);     // 输出结果 function fn() { return 20 }10
```
我们可以重写函数的toString方法，让函数参与计算，输出我们想要的结果：,就像上面我们所写的_adder.toString =function(){},
```
function fn() { return 20; }
fn.toString = function() { return 30 }

console.log(fn + 10); // 40
```
除此之外，当我们重写函数的valueOf方法也能够改变函数的隐式转换结果：
```
function fn() { return 20; }
fn.valueOf = function() { return 60 }

console.log(fn + 10); // 70
```
### 函数柯里化的好处

#### 参数复用
```
// 正常正则验证字符串 reg.test(txt)

// 函数封装后
function check(reg, txt) {
    return reg.test(txt)
}

check(/\d+/g, 'test')       //false
check(/[a-z]+/g, 'test')    //true

// Currying后
function curryingCheck(reg) {
    return function(txt) {
        return reg.test(txt)
    }
}

var hasNumber = curryingCheck(/\d+/g)
var hasLetter = curryingCheck(/[a-z]+/g)

hasNumber('test1')      // true
hasNumber('testtest')   // false
hasLetter('21212')      // false
```
上面的示例是一个正则的校验，正常来说直接调用check函数就可以了，但是如果我有很多地方都要校验是否有数字，其实就是需要将第一个参数reg进行复用，这样别的地方就能够直接调用hasNumber，hasLetter等函数，让参数能够复用，调用起来也更方便。

#### 提前确认
```
var on = function(element, event, handler) {
    if (document.addEventListener) {
        if (element && event && handler) {
            element.addEventListener(event, handler, false);
        }
    } else {
        if (element && event && handler) {
            element.attachEvent('on' + event, handler);
        }
    }
}

var on = (function() {
    if (document.addEventListener) {
        return function(element, event, handler) {
            if (element && event && handler) {
                element.addEventListener(event, handler, false);
            }
        };
    } else {
        return function(element, event, handler) {
            if (element && event && handler) {
                element.attachEvent('on' + event, handler);
            }
        };
    }
})();

//换一种写法可能比较好理解一点，上面就是把isSupport这个参数给先确定下来了
var on = function(isSupport, element, event, handler) {
    isSupport = isSupport || document.addEventListener;
    if (isSupport) {
        return element.addEventListener(event, handler, false);
    } else {
        return element.attachEvent('on' + event, handler);
    }
}
```
我们在做项目的过程中，封装一些dom操作可以说再常见不过，上面第一种写法也是比较常见，但是我们看看第二种写法，它相对一第一种写法就是自执行然后返回一个新的函数，这样其实就是提前确定了会走哪一个方法，避免每次都进行判断。  
#### 延迟运行
```
Function.prototype.bind = function (context) {
    var _this = this
    var args = Array.prototype.slice.call(arguments, 1)
 
    return function() {
        return _this.apply(context, args)
    }
}
```
像我们js中经常使用的bind，实现的机制就是Currying.

## Js中的高阶函数
JavaScript的函数其实都指向某个变量。既然变量可以指向函数，**函数的参数能接收变量，那么一个函数就可以接收另一个函数作为参数，这种函数就称之为高阶函数**。  
一个最简单的高阶函数：
```
function add(x, y, f) {
    return f(x) + f(y);
}
```
当我们调用`add(-5, 6, Math.abs)`时，参数x，y和f分别接收`-5，6`和函数`Math.abs`，根据函数定义，我们可以推导计算过程为：
```
x = -5;
y = 6;
f = Math.abs;
f(x) + f(y) ==> Math.abs(-5) + Math.abs(6) ==> 11;
return 11;
```
### map/reduce
由于`map()`方法定义在JavaScript的`Array`中，我们调用`Array的map()`方法，传入我们自己的函数，就得到了一个新的`Array`作为结果：
```
'use strict';
function pow(x) {
    return x * x;
}
var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
var results = arr.map(pow); // [1, 4, 9, 16, 25, 36, 49, 64, 81]
console.log(results);
```

再看reduce的用法。Array的`reduce()`把一个函数作用在这个Array的`[x1, x2, x3...]`上，这个函数必须接收两个参数，`reduce()`把结果继续和序列的下一个元素做累积计算，其效果就是：
```
var arr = [1, 3, 5, 7, 9];
arr.reduce(function (x, y) {
    return x + y;
});
//还可以写成箭头函数形式
arr.reduce((a,b)=> a+b);
```
### filter(筛选器)
filter也是一个常用的操作，它用于把Array的某些元素过滤掉，然后返回剩下的元素。  

和`map()`类似，Array的`filter()`也接收一个函数。和`map()`不同的是，`filter()`把传入的函数依次作用于每个元素，然后根据返回值是`true`还是`false`决定保留还是丢弃该元素。  
把一个Array中的空字符串删掉，可以这么写：
```
var arr = ['A', '', 'B', null, undefined, 'C', '  '];
var r = arr.filter(function (s) {
    return s && s.trim(); // 注意：IE9以下的版本没有trim()方法
});
r; // ['A', 'B', 'C']
```
### sort
幸运的是，sort()方法也是一个高阶函数，它还可以接收一个比较函数来实现自定义的排序。

要按数字大小排序，我们可以这么写：
```
'use strict';

var arr = [10, 20, 1, 2];
arr.sort(function (x, y) {
    if (x < y) {
        return -1;
    }
    if (x > y) {
        return 1;
    }
    return 0;
});
console.log(arr); // [1, 2, 10, 20]
//对包含字母的排序
var arr = ['Google', 'apple', 'Microsoft'];
arr.sort(function (s1, s2) {
    x1 = s1.toUpperCase();
    x2 = s2.toUpperCase();
    if (x1 < x2) {
        return -1;
    }
    if (x1 > x2) {
        return 1;
    }
    return 0;
}); // ['apple', 'Google', 'Microsoft']
```
### Array

#### every
`every()`方法可以判断数组的所有元素是否满足测试条件。
```
var arr = ['Apple', 'pear', 'orange'];
console.log(arr.every(function (s) {
    return s.length > 0;
})); // true, 因为每个元素都满足s.length>0

console.log(arr.every(function (s) {
    return s.toLowerCase() === s;
})); // false, 因为不是每个元素都全部是小写
```
#### find
`find()`方法用于查找**符合条件的第一个元素**，如果找到了，返回这个元素，否则，返回`undefined`：
```
var arr = ['Apple', 'pear', 'orange'];
console.log(arr.find(function (s) {
    return s.toLowerCase() === s;
})); // 'pear', 因为pear全部是小写

console.log(arr.find(function (s) {
    return s.toUpperCase() === s;
})); // undefined, 因为没有全部是大写的元素
```
#### findIndex
`findIndex()`和`find()`类似，也是查找符合条件的第一个元素，不同之处在于`findIndex()`会返回这个元素的索引，如果没有找到，返回`-1`：
```
var arr = ['Apple', 'pear', 'orange'];
console.log(arr.findIndex(function (s) {
    return s.toLowerCase() === s;
})); // 1, 因为'pear'的索引是1

console.log(arr.findIndex(function (s) {
    return s.toUpperCase() === s;
})); // -1
```
#### forEach
forEach()和map()类似，它也把每个元素依次作用于传入的函数，但不会返回新的数组。forEach()常用于遍历数组，因此，传入的函数不需要返回值：
```
var arr = ['Apple', 'pear', 'orange'];
arr.forEach(console.log); // 依次打印每个元素
```
## Generator函数 async函数
概念：
* `Generator` 函数是 ES6 提供的一种异步编程解决方案
* `generator` `由function *`定义（注意多出的`*`号）;函数内部使用`yield`表达式，定义不同的内部状态
* 调用`Generator`函数后，该函数并不执行，返回的不是函数运行结果，而是一个指向内部状态的指针对象
* 必须调用遍历器对象的`next`方法，使得指针移向下一个状态，输出返回的结果
* `next`方法返回的是一个对象。它的`value`属性就是当前`yield`表达式的值hello，`done`属性的值`false`则表示遍历还没有结束（即没有遇到`return`）。

```
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}

var hw = helloWorldGenerator();
hw.next()
// { value: 'hello', done: false }

hw.next()
// { value: 'world', done: false }

hw.next()
// { value: 'ending', done: true }

hw.next()
// { value: undefined, done: true }
```
next方法可以传参数：
```
function* gen(x){
 const y = yield x + 6;
 return y;
}
const g = gen(1);
g.next() // { value: 7, done: false }
g.next(2) // { value: 2, done: true } 
// next 的参数是作为上个阶段异步任务的返回结果
```
### 异步应用

因为`yield`能够中断执行代码的特性，可以帮助我们来控制异步代码的执行顺序。

例如有两个异步的函数`A `和` B`, 并且` B `的参数是 `A `的返回值，也就是说，如果 `A `没有执行结束，我们不能执行` B`。
```
function* effect() {
  const { param } = yield A();
  const { result } = yield B(param);
  console.table(result);
}
//看到结果
const iterator = effect()
iterator.next()
iterator.next()
```
### async和await
这两个相当于generator函数的另一种语义表示
* `async`函数将Generator函数的`星号（*）`替换成`async`
* 将`yield`替换成`await`

async函数对Generator函数的改进体现在以下3点：
1.内置执行器

也就是说`async函数`的执行，和普通函数一样，只需要一行就可以。不用像`Generator`函数需要调用`next`方法才能真正执行。
```
const asyncReadFile = async function () {
  const f1 = await readFile('/etc/fstab');
  const f2 = await readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
//调用只需要一行代码，而且自带执行器，不需要next()
asyncReadFile();
```

2.更好的语义

`async`和`await`比起星号和`yield`，语义更加清楚了。`async`表示函数里有**异步操作**，`await`表示紧跟在后面的表达式需要等待结果。

3.返回值是promise

`async函数`的返回值是 `Promise 对象`，这比 `Generator 函数`的返回值是` Iterator 对象`方便多了。你可以用`then`方法指定下一步的操作。
```
async function getStockPriceByName(name) {
  const symbol = await getStockSymbol(name);
  const stockPrice = await getStockPrice(symbol);
  return stockPrice;
}

getStockPriceByName('goog').then(function (result) {
  console.log(result);
});
```
先执行了第一个`await`后的`getStockSymbol(name)`函数；得到了股票的名称`symbol`后，将`symbol`传给第二个await后面的`getStockPrice(symbol)`作为参数；最后返回股票价格`stockPrice`。

## 参考
https://www.jianshu.com/p/2975c25e4d71  
https://delaprada.com/2020/01/14/Generator%E5%87%BD%E6%95%B0-async%E5%87%BD%E6%95%B0/#more
