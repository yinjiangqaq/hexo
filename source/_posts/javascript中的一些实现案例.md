---
title: javascript中的一些实现案例
date: 2020-04-10 00:53:32
tags: JavaScript
categories: JavaScript
---
## 前言
手打了一些可能在面试中会问到的JavaScript功能的一些实现案例

## call的实现
```
function test(a) {
    console.log(this)
    b = 1;

    return this.b + a
}//测试用的函数

Function.prototype.mycall = function(obj) {
    obj.fn = this;//绑定this的指向
    // console.log(...[...arguments].slice(1))
    return obj.fn(...[...arguments].slice(1)) //先把参数整理成一个数组，然后再展开
}

console.log('mycall', test.mycall({ b: 2 }, 3))
//{ b: 2, fn: [Function: test] }
//mycall 5
```
## apply的实现
```
function test(a) {
    console.log(this)
    b = 1;

    return this.b + a
} //测试用的函数
Function.prototype.myapply = function(obj) {
    obj.fn = this; //将this指向指向了obj，绑定到了obj上
    console.log(obj.fn)
    if (arguments[1]) {
        return obj.fn(...arguments[1]) //单独调用的函数的参数，不支持参数数组的，
            //需要把参数数组拓展开，用到拓展运算符
    } else {
        return obj.fn()
    }

}
//{ b: 2, fn: [Function: test] }
//myapply 5
```
## bind的实现
```
function test(a) {
    console.log(this)
    b = 1;

    return this.b + a
} //测试用的函数
Function.prototype.mybind = function(obj) {
    var _this = this;
    return function(...args) {
        return _this.apply(obj, args)
    }
}

const obj = {
    b: 2
}


console.log('mybind', test.mybind(obj)(3))
//{ b: 2 }
//mybind 5
```
## instance的实现
```
function myinstance(left, right) {

    left = left.__proto__
    let prototype = right.prototype;
    while (left !== null) {

        if (prototype === left) return true;
        left = left.__proto__
    }
    return false
}


var a = [1, 2];
console.log(myinstance(a, Array))
//true
```
## new的实现
```
//ES5
function mynew() {
    let obj = {}; //新建一个对象
    let con = [].shift.call([...arguments]); //拿第一个参数，也就是构造函数
    obj.__proto__ = con.prototype;
    let result = con.call([...arguments].slice(1))
    return typeof result === 'object' ? result : obj
}

//ES6
function myNew(fn, ...args) {
    let obj = Object.create(fn.prototype); //创建一个对象，将要实例化对象的原型链指向它
    fn.call(obj, ...args); //将函数指向该对象
    return obj;
}

function fun1() {
    this.name = "?"
}
console.log(myNew(fun1), new fun1()) //fun1 { name: '?' } fun1 { name: '?' }

console.log(JSON.stringify(myNew(fun1)) === JSON.stringify(new fun1())) //true
```
