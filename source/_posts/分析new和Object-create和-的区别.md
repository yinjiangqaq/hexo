---
title: '分析new和Object.create和{}的区别'
date: 2020-05-07 17:02:42
tags: JavaScript
categories: JavaScript
---

## 直接字面量创建
```
var objA = {};
objA.name = 'a';
objA.sayName = function() {
    console.log(`My name is ${this.name} !`);
}
// var objA = {
//     name: 'a',
//     sayName: function() {
//         console.log(`My name is ${this.name} !`);
//     }
// }
objA.sayName();
console.log(objA.__proto__ === Object.prototype); // true
console.log(objA instanceof Object); // true
```
这样的直接字面量创建对象的方法等价于`new Object()`

## new关键字创建
首先我们知道new关键字的原理
```
 function mynew(fn){
     let obj={};//新建一个对象
     obj.__proto__=fn.prototype;//链接到原型
     let result =fn.call(arguments);//执行构造函数
     return typeof result ==="object" ? result : obj
     //返回这个对象
 }
 ```
 ```
 var objB = new Object();
// var objB = Object();
objB.name = 'b';
objB.sayName = function() {
    console.log(`My name is ${this.name} !`);
}
objB.sayName();
console.log(objB.__proto__ === Object.prototype); // true
console.log(objB instanceof Object); // true

```
## Object.create() 

`Object.create(arg, pro)`创建的对象的原型取决于arg，`arg`为`null`，新对象是空对象，没有原型，不继承任何对象；`arg`为指定对象，新对象的原型指向指定对象，继承指定对象


`Object.create(proto[, propertiesObject])`  
`proto`必填参数，是新对象的原型对象，如上面代码里新对象me的`__proto__`指向`person`。注意，如果这个参数是`null`，那新对象就彻彻底底是个**空对象**，没有继承`Object.prototype`上的任何属性和方法，如`hasOwnProperty()`、`toString()`等。

```
var a = Object.create(null);
console.dir(a); // {}
console.log(a.__proto__); // undefined
console.log(a.__proto__ === Object.prototype); // false
console.log(a instanceof Object); // false 没有继承`Object.prototype`上的任何属性和方法，所以原型链上不会出现Object
```
`propertiesObject`是可选参数，指定要添加到新对象上的可枚举的属性（即其自定义的属性和方法，可用`hasOwnProperty()`获取的，而不是原型对象上的）的描述符及相应的属性名称。
```
var bb = Object.create(null, {
    a: {
        value: 2,
        writable: true,
        configurable: true
    }
});
console.dir(bb); // {a: 2}
console.log(bb.__proto__); // undefined
console.log(bb.__proto__ === Object.prototype); // false
console.log(bb instanceof Object); // false 没有继承`Object.prototype`上的任何属性和方法，所以原型链上不会出现Object

// ----------------------------------------------------------

var cc = Object.create({b: 1}, {
    a: {
        value: 3,
        writable: true,
        configurable: true
    }
});
console.log(cc); // {a: 3}
console.log(cc.hasOwnProperty('a'), cc.hasOwnProperty('b')); // true false 说明第二个参数设置的是新对象自身可枚举的属性
console.log(cc.__proto__); // {b: 1} 新对象cc的__proto__指向{b: 1}
console.log(cc.__proto__ === Object.protorype); // false
console.log(cc instanceof Object); // true cc是对象，原型链上肯定会出现Object
```
自己实现一个Object.create()
```
Object.mycreate = function (proto, properties) {
    function F() { };
    let obj = new F();
    obj.__proto__ = proto;
    if (properties) {
        console.log(Object.keys(properties)[0])
        Object.defineProperties(obj, properties);

    }
    return obj;
}
var hh = Object.mycreate({a: 11}, {mm: {value: 10}});
console.log(hh.mm);//10
```



