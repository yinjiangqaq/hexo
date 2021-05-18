---
title: JavaScript基本知识
date: 2020-01-17 10:50:52
tags: JavaScript
categories: JavaScript
---
##  JavaScript
### 前言
JavaScript是一种运行在浏览器中的解释型的编程语言。在Web世界里，只有JavaScript能跨平台、跨浏览器驱动网页，与用户交互。
### JavaScript和ES6的区别
ES6， 全称 ECMAScript 6.0 ，是 JavaScript 的下一个版本标准，2015.06 发版。ES6适应更复杂的应用；实现代码库之间的共享。目前只有chrome和firefox两个浏览器对ES6比较友好。

## JavaScript快速入门
### 引入
1.JavaScript代码可以直接嵌在网页的任何地方，不过通常我们都把JavaScript代码放到` <head> `中  
2.第二种方法是把JavaScript代码放到一个单独的.js文件，然后在HTML中通过`<script src="..."></script>`引入这个文件,在这个.js文件中编写代码，更容易维护。
### 基本语法
JavaScript的语法和Java语言类似，每个语句以`;`结束，语句块用`{...}`。但是，JavaScript并不强制要求在每个语句的结尾加`;`，浏览器中负责执行JavaScript代码的引擎会自动在每个语句的结尾补上`;`。但是我们自己编写代码的时候还有养成加`;`的习惯，避免因为引擎自动加引号，导致语义出错，而运行结果不一致的
### 数据类型

#### Number
JavaScript不区分整数和浮点数，统一用Number表示，以下都是合法的Number类型：
```
123; // 整数123
0.456; // 浮点数0.456
1.2345e3; // 科学计数法表示1.2345x1000，等同于1234.5
-99; // 负数
NaN; // NaN表示Not a Number，当无法计算结果时用NaN表示
Infinity; // Infinity表示无限大，当数值超过了JavaScript的Number所能表示的最大值时，就表示为Infinity
```
Number可以直接做四则运算，规则和数学一致：
```
1 + 2; // 3
(1 + 2) * 5 / 2; // 7.5
2 / 0; // Infinity
0 / 0; // NaN
10 % 3; // 1  % 表示求余运算
10.5 % 3; // 1.5
```
#### String
字符串是用单引号或双引号括起来的任意文本,如果`'`本身也是一个字符，那就可以用`""`括起来，比如`"I'm OK"`包含的字符是`I`，`'`，`m`，空格，`O`，`K`这6个字符,如果字符串内部既包含`'`,又包含`"`怎么办，可以用转义字符`\`标识，比如
```
'I\'m \"OK\"!';
```
表示的字符串内容是: `I'm "OK"!`
##### 字符串操作
获取字符串的长度
```
var s = 'Hello world!';
s.length; //12
```
获取字符串指定位置的字符，使用类似Array的下标操作，索引号从0开始：
```
var s = 'Hello world!';
s[0]; //'H'
s[6]; //' '
s[12]; //undefined,超出范围不会报错，一律返回undefined
```
需要特别注意的是，字符串是不可变的，如果对字符串的某个索引赋值，不会有错误，但是也没有任何效果：
```
var s = 'Test';
s[0] = 'X';
alert(s); // s仍然为'Test'
```
`toUpperCase()`把一个字符串全部变大写：
```
var s = 'Hello';
s.toUpperCase(); // 返回'HELLO'
```
`toLowerCase()`把一个字符串全部变小写：
```
var s = 'Hello';
var lower = s.toLowerCase(); // 返回'hello'并赋值给变量lower
lower; // 'hello'
```
`indexOf()`会搜索指定字符串中出现的位置：
```
var s = 'hello, world';
s.indexOf('world'); // 返回7
s.indexOf('World'); // 没有找到指定的子串，返回-1
```
`substring()`返回指定索引区间的子串：
```
var s = 'hello, world'
s.substring(0, 5); // 从索引0开始到5（不包括5），返回'hello'
s.substring(7); // 从索引7开始到结束，返回'world'
```
ES6新增了子串的识别方法：
* includes()：返回布尔值，判断是否找到参数字符串。
* startsWith()：返回布尔值，判断参数字符串是否在原字符串的头部。
* endsWith()：返回布尔值，判断参数字符串是否在原字符串的尾部。  

以上三个方法都可以接受两个参数，需要搜索的字符串，和可选的搜索起始位置索引。
```
let string = "apple,banana,orange";
string.includes("banana");     // true
string.startsWith("apple");    // true
string.endsWith("apple");      // false
string.startsWith("banana",6)  // true
```
除此之外还有字符串补全的方法：
 * padStart：返回新的字符串，表示用参数字符串从头部（左侧）补全原字符串。
 * padEnd：返回新的字符串，表示用参数字符串从尾部（右侧）补全原字符串。  
 ```
console.log("h".padStart(5,"o"));  // "ooooh"
console.log("h".padEnd(5,"o"));    // "hoooo"
console.log("h".padStart(5));      // "    h"
```
如果指定的长度小于或等于原字符串的长度，则返回原字符串:
```
console.log("hello".padStart(5,"A"));  // "hello"
```
如果原字符串加上补全的字符串长度大于指定长度，则截去超出位数的字符串：
```
console.log("hello".padEnd(10,",world!"));  // "hello,worl"
```
常用于补全位数:
```
console.log("123".padStart(10,"0"));  // "0000000123"
```

##### 模板字符串
模板字符串相当于加强版的字符串，用反引号\`,除了作为普通字符串，还可以用来定义多行字符串，还可以在字符串中加入变量和表达式。  

普通字符串
```
let string = `Hello'\n'world`;
console.log(string); 
// "Hello'
// 'world"
```
ES6支持多行字符串,省去了加`\n`：
```
let string1 =  `Hey,
can you stop angry now?`;
console.log(string1);
// Hey,
// can you stop angry now?
```
之前要把多个字符串连接起来，需要用`+`号连接：
```
var name = '小明';
var age = 20;
var message = '你好, ' + name + ', 你今年' + age + '岁了!';
alert(message);   // 你好小明，你今年20岁了
```
但是ES6的模板字符串，**支持在字符串中加入变量和表达式**  
变量名写在`${}`中，`${}`还可以放入JavaScript 表达式。
```
let name = "Mike";
let age = 27;
let info = `My Name is ${name},I am ${age+1} years old next year.`
console.log(info);
// My Name is Mike,I am 28 years old next year.
```
字符串中调用函数：
```
function f(){
  return "have fun!";
}
let string2= `Game start,${f()}`;
console.log(string2);  // Game start,have fun!
```
**`注意要点:模板字符串中的换行和空格都是会被保留的`**
#### bool
布尔值和布尔代数的表示完全一致，一个布尔值只有`true`、`false`两种值，要么是`true`，要么是`false`，可以直接用`true`、`false`表示布尔值，也可以通过布尔运算计算出来：
```
true; // 这是一个true值
false; // 这是一个false值
2 > 1; // 这是一个true值
2 >= 3; // 这是一个false值
```
在JavaScript在设计时，有两种比较运算符：  
* 第一种是`==`比较，它会自动转换数据类型再比较，很多时候，会得到非常诡异的结果；  
* 第二种是`===`比较，它不会自动转换数据类型，如果数据类型不一致，返回false，如果一致，再比较。  
由于JavaScript这个设计缺陷，不要使用`==`比较，始终坚持使用`===`比较。  
另一个例外是`NaN`这个特殊的Number与所有其他值都不相等，包括它自己：
```
NaN === NaN; // false
```
唯一能判断`NaN`的方法是通过`isNaN()`函数：
```
isNaN(NaN); // true
```
而需要注意的是浮点数的比较：
```
1 / 3 === (1 - 2 / 3); // false
```
这不是JavaScript的设计缺陷。浮点数在运算过程中会产生误差，因为计算机无法精确表示无限循环小数。要比较两个浮点数是否相等，只能计算它们之差的绝对值，看是否小于某个阈值：
```
Math.abs(1 / 3 - (1 - 2 / 3)) < 0.0000001; // true
```
#### null和undefined
null 表示一个"空"的值，它和 `0`和空字符串` '' `,`0`表示一个数值，` '' `表示长度为0的字符串，而null表示为"空"。  
JavaScript的设计者希望用`null`表示一个空的值，而`undefined`表示值未定义。然鹅区分的意义不是很大，大多数情况下，我们都应该用`null`。`undefined`仅仅在判断函数参数是否传递的情况下有用。
#### 数组
数组是一组按顺序排列的集合，集合的每个值称为元素。JavaScript的数组可以包括任意数据类型。例如：
```
[1, 2, 3.14, 'Hello', null, true];
```
另一种创建数组的方法是通过`Array()`函数实现的
```
new Array(1, 2, 3); // 创建了数组[1, 2, 3]
```
然鹅，出于对代码的可读性考虑，建议直接使用`[]`  
数组的值可以用索引来访问，索引的起始值是0：
```
var arr = [1, 2, 3.14, 'Hello', null, true];
arr[0]; // 返回索引为0的元素，即1
arr[5]; // 返回索引为5的元素，即true
arr[6]; // 索引超出了范围，返回undefined
```

**indexOf**  
与String类似，`Array`也可以通过`indexOf()`来搜索一个指定的元素的位置：
```
var arr = [10, 20, '30', 'xyz'];
arr.indexOf(10); // 元素10的索引为0
arr.indexOf(20); // 元素20的索引为1
arr.indexOf(30); // 元素30没有找到，返回-1
arr.indexOf('30'); // 元素'30'的索引为2
```

**slice**  
`slice()`就是对应String的`substring()`版本，它截取`Array`的部分元素，然后返回一个新的`Array`：
```
var arr = ['A', 'B', 'C', 'D', 'E', 'F', 'G'];
arr.slice(0, 3); // 从索引0开始，到索引3结束，但不包括索引3: ['A', 'B', 'C']
arr.slice(3); // 从索引3开始到结束: ['D', 'E', 'F', 'G']
```
如果不给`slice()`指定参数，默认是从头截到尾，我们也可以用这个方法来复制一个数组  

**push和pop**  

`push()`向`Array`的末尾添加若干元素，`pop()`则把`Array`的最后一个元素删除掉：
```
var arr = [1, 2];
arr.push('A', 'B'); // 返回Array新的长度: 4
arr; // [1, 2, 'A', 'B']
arr.pop(); // pop()返回'B'
arr; // [1, 2, 'A']
arr.pop(); arr.pop(); arr.pop(); // 连续pop 3次
arr; // []
arr.pop(); // 空数组继续pop不会报错，而是返回undefined
arr; // []
```
**unshift和shift**  
如果要往Array的头部添加若干元素，使用unshift()方法，shift()方法则把Array的第一个元素删掉：
```
var arr = [1, 2];
arr.unshift('A', 'B'); // 返回Array新的长度: 4
arr; // ['A', 'B', 1, 2]
arr.shift(); // 'A'
arr; // ['B', 1, 2]
arr.shift(); arr.shift(); arr.shift(); // 连续shift 3次
arr; // []
arr.shift(); // 空数组继续shift不会报错，而是返回undefined
arr; // []
```

**reverse**  
`reverse()`把整个`Array`的元素给掉个个，也就是反转:
```
var arr = ['one', 'two', 'three'];
arr.reverse(); 
arr; // ['three', 'two', 'one']
```

**splice**  
`splice()`方法是修改`Array`的“万能方法”，它可以从指定的索引开始删除若干元素，然后再从该位置添加若干元素：
```
var arr = ['Microsoft', 'Apple', 'Yahoo', 'AOL', 'Excite', 'Oracle'];
// 从索引2开始删除3个元素,然后再添加两个元素:
arr.splice(2, 3, 'Google', 'Facebook'); // 返回删除的元素 ['Yahoo', 'AOL', 'Excite']
arr; // ['Microsoft', 'Apple', 'Google', 'Facebook', 'Oracle']
// 只删除,不添加:
arr.splice(2, 2); // ['Google', 'Facebook']
arr; // ['Microsoft', 'Apple', 'Oracle']
// 只添加,不删除:
arr.splice(2, 0, 'Google', 'Facebook'); // 返回[],因为没有删除任何元素
arr; // ['Microsoft', 'Apple', 'Google', 'Facebook', 'Oracle']
```

**concat** 
 `concat()`方法把当前的`Array`和另一个`Array`连接起来，并返回一个新的`Array`：
```
var arr = ['A', 'B', 'C'];
var added = arr.concat([1, 2, 3]);
added; // ['A', 'B', 'C', 1, 2, 3]
arr; // ['A', 'B', 'C']
```
请注意 `concat()`方法并没有修改原来的`Array`,只是返回一个新的`Array`  
实际上，concat()方法可以接收任意个元素和Array，并且自动把Array拆开，然后全部添加到新的Array里：
```
var arr = ['A', 'B', 'C'];
arr.concat(1, 2, [3, 4]); // ['A', 'B', 'C', 1, 2, 3, 4]
```

**join** 
`join()`方法是一个非常实用的方法，它把当前Array的每个元素都用指定的字符串连接起来，然后**返回连接后的字符串**：
```
'use strict';
var arr = ['A', 'B', 'C'];
arr.concat(1, 2, [3, 4]); // ['A', 'B', 'C', 1, 2, 3, 4]

var arr = ['小明', '小红', '大军', '阿黄'];
console.log('欢迎'+arr.join(',')); //欢迎小明,小红,大军,阿黄
```
#### 对象
JavaScript的对象是一组由键-值组成的无序集合，例如：
```
var person = {
    name: 'Bob',
    age: 20,
    tags: ['js', 'web', 'mobile'],
    city: 'Beijing',
    hasCar: true,
    zipcode: null
};
```
要访问到对象的属性，我们只需要用`对象名.属性名`的方式。  
由于JavaScript的对象是动态类型，你可以自由地给一个对象添加或删除属性：
```
var xiaoming = {
    name: '小明'
};
xiaoming.age; // undefined
xiaoming.age = 18; // 新增一个age属性
xiaoming.age; // 18
delete xiaoming.age; // 删除age属性
xiaoming.age; // undefined
delete xiaoming['name']; // 删除name属性
xiaoming.name; // undefined
delete xiaoming.school; // 删除一个不存在的school属性也不会报错
```
如果我们要检测`xiaoming`是否拥有某一属性，可以用`in`操作符：
```
var xiaoming = {
    name: '小明',
    birth: 1990,
    school: 'No.1 Middle School',
    height: 1.70,
    weight: 65,
    score: null
};
'name' in xiaoming; // true
'grade' in xiaoming; // false
```
但是`in`判断一个属性是否存在，这个属性可能是这个对象继承得到的，并非自己有的：
```
'toString' in xiaoming; // true toString是每个object对象都有的属性
```
所以要判断一个属性是否是对象自身拥有的，而不是继承得到的，可以用`hasOwnProperty()`方法
```
var xiaoming = {
    name: '小明'
};
xiaoming.hasOwnProperty('name'); // true
xiaoming.hasOwnProperty('toString'); // false
```

#### 变量
变量在JavaScript中就是用一个变量名表示，变量名是大小写英文、数字、`$`和`_`的组合，且不能用数字开头。变量名也不能是JavaScript的关键字，如`if`、`while`等。申明一个变量用`var`语句，例如：
```
var a; // 申明了变量a，此时a的值为undefined
var $b = 1; // 申明了变量$b，同时给$b赋值，此时$b的值为1
var s_007 = '007'; // s_007是一个字符串
var Answer = true; // Answer是一个布尔值true
var t = null; // t的值是null
```
在JavaScript中，使用等号=对变量进行赋值。可以把任意数据类型赋值给变量，同一个变量可以反复赋值，而且可以是不同类型的变量，**但是要注意只能用`var`申明一次**，例如：
```
var a = 123; // a的值是整数123
a = 'ABC'; // a变为字符串
```
这种变量本身类型不固定的语言称之为动态语言，与之相对应的是静态语言，静态语言在定义变量的时候必须指定变量类型，如果赋值的时候，类型不匹配，就会报错，例如：
```
int a = 123; // a是整数类型变量，类型用int申明
a = "ABC"; // 错误：不能把字符串赋给整型变量
```
与静态语言相比，动态语言更灵活就是这样的原因。
#### strict模式
JavaScript在设计之初，为了方便初学者学习，并不强制要求用`var`申明变量。这个设计错误带来了严重的后果：如果一个变量没有通过`var`申明就被使用，那么该变量就自动被申明为**全局变量**：(这是一个设计缺陷)
```
i = 10; // i现在是全局变量
```
为了修补JavaScript这一严重设计缺陷，ECMA在后续规范中推出了strict模式，在strict模式下运行的JavaScript代码，**强制通过`var`申明变量**，未使用`var`申明变量就使用的，将导致运行错误。  
启用strict模式的方法是在JavaScript代码的第一行写上：
```
'use strict';
```
### 循环

#### for...in
`for`循环的一个变体是`for ... in`循环，它可以把一个对象的所有属性依次循环出来：
```
var o = {
    name: 'Jack',
    age: 20,
    city: 'Beijing'
};
for (var key in o) {
    if (o.hasOwnProperty(key)) { //过滤掉非继承得来的属性
        console.log(key); // 'name', 'age', 'city'
    }
}
```
由于`Array`也是对象，而它的每个元素的索引被视为对象的属性，因此，`for ... in`循环可以直接循环出`Array`的索引：
```
var a = ['A', 'B', 'C'];
for (var i in a) {
    console.log(i); // '0', '1', '2'
    console.log(a[i]); // 'A', 'B', 'C'
}
```
**请注意，`for ... in`对`Array`的循环得到的是`String`而不是`Number`。**

### Map和Set
`Map`和`Set`是ES6标准新增的数据类型，请根据浏览器的支持情况决定是否要使用。
#### Map
`Map`是一组键值对的结构，具有极快的查找速度。用JavaScript写一个Map如下：
```
var m = new Map([['Michael', 95], ['Bob', 75], ['Tracy', 85]]);
m.get('Michael'); // 95
```
初始化`Map`需要一个二维数组，或者直接初始化一个空`Map`。`Map`具有以下方法：
```
var m = new Map(); // 空Map
m.set('Adam', 67); // 添加新的key-value
m.set('Bob', 59);
m.has('Adam'); // 是否存在key 'Adam': true
m.get('Adam'); // 67
m.delete('Adam'); // 删除key 'Adam'
m.get('Adam'); // undefined
```
由于一个key只能对应一个value，所以，多次对一个key放入value，后面的值会把前面的值冲掉：
```
var m = new Map();
m.set('Adam', 67);
m.set('Adam', 88);
m.get('Adam'); // 88
```
#### Set
`Set`和`Map`类似，也是一组`key`的集合，但不存储`value`。由于`key`不能重复，所以，在`Set`中，没有重复的`key`。  
要创建一个`Set`，需要提供一个`Array`作为输入，或者直接创建一个空`Set`：
```
var s1 = new Set(); // 空Set
var s2 = new Set([1, 2, 3]); // 含1, 2, 3
```
重复元素在`Set`中自动被过滤：
```
var s = new Set([1, 2, 3, 3, '3']);
s; // Set {1, 2, 3, "3"}
```
注意数字`3`和字符串` '3' `是不同的元素。  
通过`add(key)`方法可以添加元素到`Set`中，可以重复添加，但不会有效果：
```
s.add(4);
s; // Set {1, 2, 3, 4}
s.add(4);
s; // 仍然是 Set {1, 2, 3, 4}
```
通过`delete(key)`方法可以删除元素：
```
var s = new Set([1, 2, 3]);
s; // Set {1, 2, 3}
s.delete(3);
s; // Set {1, 2}
```
### iterable
遍历`Array`可以采用下标循环，遍历`Map`和`Set`就无法使用下标。为了统一集合类型，ES6标准引入了新的`iterable`类型，`Array`、`Map`和`Set`都属于`iterable`类型。  
具有`iterable`类型的集合可以通过新的`for ... of`循环来遍历。  
`for...of` 是 ES6 新引入的循环，用于替代 `for..in` 和 `forEach()` ，并且支持新的迭代协议。它可用于迭代常规的数据类型，如 `Array` 、 `String` 、 `Map` 和 `Set` 等等。  
用`for ... of`循环遍历集合，用法如下：
```
var a = ['A', 'B', 'C'];
var s = new Set(['A', 'B', 'C']);
var m = new Map([[1, 'x'], [2, 'y'], [3, 'z']]);
for (var x of a) { // 遍历Array
    console.log(x); // A \n B \n C
}
for (var x of s) { // 遍历Set
    console.log(x); //A \n B \n C
}
for (var x of m) { // 遍历Map
    console.log(x[0] + '=' + x[1]);
}// 1=x  \n 2=y  \n 3=z
```
`for ... of`循环和`for ... in`循环有何区别？  
`for ... in`循环由于历史遗留问题，它遍历的实际上是对象的属性名称。一个`Array`数组实际上也是一个对象，它的**每个元素的索引被视为一个属性**。  
当我们手动给`Array`对象添加了额外的属性后，`for ... in`循环将带来意想不到的意外效果：
```
var a = ['A', 'B', 'C'];
a.name = 'Hello';
for (var x in a) {
    console.log(x); // '0',\n '1',\n '2', 'name'
}
```
`for ... in`循环将把`name`包括在内，但`Array`的`length`属性却不包括在内。  
`for ... of`循环则完全修复了这些问题，它只循环集合本身的元素：
```
var a = ['A', 'B', 'C'];
a.name = 'Hello';
for (var x of a) {
    console.log(x); // 'A',\n 'B',\n 'C'
}
```
然而，更好的方式是直接使用`iterable`内置的`forEach`方法，它接收一个函数，每次迭代就自动回调该函数。以`Array`为例：
```
'use strict';
var a = ['A', 'B', 'C'];
a.forEach(function (element, index, array) {
    // element: 指向当前元素的值
    // index: 指向当前索引
    // array: 指向Array对象本身
    console.log(element + ', index = ' + index);
});
//A, index = 0
//B, index = 1
//C, index = 2
```
`Set`与`Array`类似，但`Set`没有索引，因此回调函数的前两个参数都是元素本身：
```
var s = new Set(['A', 'B', 'C']);
s.forEach(function (element, sameElement, set) {
    console.log(element);
});
```
`Map`的回调函数参数依次为value、key和map本身：
```
var m = new Map([[1, 'x'], [2, 'y'], [3, 'z']]);
m.forEach(function (value, key, map) {
    console.log(value);
});
```
如果对某些参数不感兴趣，由于JavaScript的函数调用不要求参数必须一致，因此可以忽略它们。例如，只需要获得`Array`的`element`：
```
var a = ['A', 'B', 'C'];
a.forEach(function (element) {
    console.log(element);
});
```
## 参考
[ES6教程](https://www.runoob.com/w3cnote/es6-tutorial.html)  
基本数据类型和引用数据类型的区别：https://www.cnblogs.com/c2016c/articles/9328725.html