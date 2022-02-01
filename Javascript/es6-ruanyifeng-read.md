---
title: ES6阅读笔记
date: 2021-02-16 01:06:35
tags: ES6
---

## let、const

1.let、const所声明的变量只在命令所在的代码块内有效。

2.不存在变量提升。

3.暂时性死区。

4.不允许重复声明

const声明的常量不得改变值，实际上const并不是变量的值不得改动，而是变量指向的那个内存地址不得改变。

## 块级作用域

在ES5中块级作用域中不允许声明函数。

在ES6中块级作用域中，函数声明语句的行为类似于let，在块级作用域外不可引用。

```javascript
//ES6
function f() {console.log('I am outside!');}
(function() {
  if(false){
    function f() {console.log('I am inside！');}
  }
  f();
}());// I am outside!

//ES5
function f() {console.log('I am outside!');}
(function() {
  if(false){
    function f() {console.log('I am inside！');}
  }
  f();
}());//Uncaught TypeError: f is not a function
```

### 浏览器的规定

1.允许在块级作用域内声明函数

2.函数声明类似于var，即会提升到全局作用域或函数作用域的头部。

3.同时，函数声明还会提升到所在的块级作用域的头部。

**考虑到环境导致的行为差异太大，应该避免在块级作用域内声明函数**

如果确实需要则应该写成函数表达式的形式，而不是函数声明语句。

```javascript
//函数声明语句
{
	let a = 'secret';
  function f() {
    return a;
  };
}

//函数表达式
{
  let a = 'secret';
  let f = function() {
    return a;
  };
}
```

ES6的块级作用域允许声明函数的规则只在使用大括号的情况下成立，如果没有大括号，就会报错。

```javascript
//不报错
'use strict'
if (true) {
  function f() {}
}

//报错
'use strict'
if (true) 
  function f() {}
```

### 取得顶层对象

```javascript
//方法一
(typeof window !== 'undefined'? window
:(typeof process === 'object' &&
	typeof require === 'function' &&
	typeof global === 'object')
	? global
	: this);
//方法二
var getGlobal = function(){
	if(typeof self !== 'undefined') {return self;}
	if(typeof window !== 'undefined') {return window;}
	if(typeof global !== 'undefined') {return global;}
	throw new Error('unable to locate global object');
}
```

## 解构

```javascript
//例一
let {log, sin, cos} = Math;

//例二
const { log } = console;
log('hello') //hello
```

```javascript
//字符串len解构
let {length: len } = 'hello';
len //5
```

### 变量解构的用途

（1）交换变量的值

```javascript
let x = 1;
let y = 2;
[x,y] =[y,x];
```

（2）从函数返回多个值

``` javascript
function example(){
	return [1,2,3];
}
let [a,b,c] = example();

function example(){
	return {
		foo: 1,
		bar: 2
	};
}
let { foo , bar } = example();
```

（3）函数参数的定义

```javascript
//参数是一组有次序的值
function f([x,y,z]){...}
f([1,2,3]);

//参数是一组无次序的值
function f({x,y,z}){...}
f({z:3,y:2,x:1});
```

（4）JSON数据的提取

```javascript
let jsonData = {
 id: 42,
 status: "OK",
 data: [867,5309]
};
let {id,status,data:number} = jsonData;
```

（5）函数参数的默认值

```javascript
jQuery.ajax = function(url, {
	async = true,
	beforeSend = function() {},
	cache = true,
	complete = function() {},
	crossDomain = false,
	global = true,
}={}){

};
```

（6）遍历Map

```javascript
const map = new Map();
map.set('first', 'hello');
map.set('second', 'world');

for(let [key,value]  of map){
	console.log(key + " is " + value);
}

//获取键名
for(let [key] of map){
 //...
}
for(let [,value] of map){
 //...
}
```

（7）输入模块的指定方法

加载模块时，往往需要指定输入哪些方法。解构赋值使得输入语句非常清晰

```javascript
const {SourceMapConsumer, SourceNode} = required("source-map");
```

## 字符串

### 模版字符串-------模版编译

```javascript
let template = `
<ul>
  <% for(let i=0; i < data.supplies.length; i++) { %>
    <li><%= data.supplies[i] %></li>
  <% } %>
</ul>
`;

function compile(template){
  const evalExpr = /<%=(.+?)%>/g;
  const expr = /<%([\s\S]+?)%>/g;

  template = template
    .replace(evalExpr, '`); \n  echo( $1 ); \n  echo(`')
    .replace(expr, '`); \n $1 \n  echo(`');

  template = 'echo(`' + template + '`);';

  let script =
  `(function parse(data){
    let output = "";

    function echo(html){
      output += html;
    }

    ${ template }

    return output;
  })`;

  return script;
}

let parse = eval(compile(template));
div.innerHTML = parse({ supplies: [ "broom", "mop", "cleaner" ] });
```

### 过滤HTML字符串（防止用户输入恶意内容）

```javascript
let message =
  SaferHTML`<p>${sender} has sent you a message.</p>`;

function SaferHTML(templateData) {
  let s = templateData[0];
  for (let i = 1; i < arguments.length; i++) {
    let arg = String(arguments[i]);

    // Escape special characters in the substitution.
    s += arg.replace(/&/g, "&amp;")
            .replace(/</g, "&lt;")
            .replace(/>/g, "&gt;");

    // Don't escape special characters in the template.
    s += templateData[i];
  }
  return s;
}
```

### 多语言转换（国际化处理）

```javascript
i18n`Welcome to ${siteName}, you are visitor number ${visitorNumber}!`
// "欢迎访问xxx，您是第xxxx位访问者！"
```

## 字符串的新增方法

includes() ：返回布尔值，表示是否找到了参数字符串。

startsWith()：返回布尔值，表示参数字符串是否在原字符串的头部。

endsWith()：返回布尔值，表示参数字符串是否在原字符串的尾部。

repeat()；方法返回一个新的字符串，表示将原字符串重复n次。

padStart()：用于头部补全

padEnd()：用于尾部补全

trimStart()：消除字符串头部空格

trimEnd()：消除尾部空格

matchAll()：返回一个正则表达式在当前字符串的所有匹配。

## 正则表达式

```javascript
var re =/ab+c/;

var re = new RegExp("ab+c");
```

```
^开始
$结束
. 任意单个字符
* 0次或多次
+ 1次或多次
？0次或或者1次
\b 匹配一个单词边界
\B 匹配一个非单词边界
\d 匹配一个数字
\D 匹配一个非数字字符
\w 匹配一个单字字符
\W 匹配一个非单字字符
```

```
exec 一个在字符串中执行查找匹配的方法，返回一个数组
test 一个在字符串中测试是否匹配的方法，返回true or false
match 一个在字符串中执行查找匹配的String方法，它返回一个数组，在未匹配到的时候会返回null
matchAll 一个在字符串中执行查找所有匹配的String方法，它返回一个迭代器
search 一个在字符串中测试匹配的String方法，他返回匹配到的位置索引，或者在失败时返回-1
replace 一个在字符串中执行查找匹配的String方法，并且使用替换字符串替换掉匹配到的子字符串
split 一个使用正则表达式或者一个固定字符串分隔一个字符串，并将分隔后的子字符串存储到数组中的String方法。
```

正则实例对象新增`unicode`属性，表示是否设置了`u`修饰符。

### y修饰符

`y`修饰符的作用与`g`修饰符类似，也是全局匹配，后一次匹配都从上一次匹配成功的下一个位置开始。不同之处在于，`g`修饰符只要剩余位置中存在匹配就可，而`y`修饰符确保匹配必须从剩余的第一个位置开始，这也就是“粘连”的涵义。

```javascript
var s = 'aaa_aa_a';
var r1 = /a+/g;
var r2 = /a+/y;

r1.exec(s) // ["aaa"]
r2.exec(s) // ["aaa"]

r1.exec(s) // ["aa"]
r2.exec(s) // null
```

与`y`修饰符相匹配，ES6 的正则实例对象多了`sticky`属性，表示是否设置了`y`修饰符。

## 数值的扩充

Number.isFinite()

Number.isNaN()

Number.parseInt()

Number.parseFloat()

Number.isInteger()

Number.EPSILON 代表一个极小的常量。

Number.isSafeInteger() 用来判断一个整数是否落在这个范围之内。

Math.trunc() 用于去除一个数的小数部分，返回整数部分

```javascript
Math.trunc = Math.trunc || function(x) {
  return x < 0 ? Math.ceil(x) : Math.floor(x);
};
```

Math.sign() 方法用来判断一个数到底是整数、负数、还是零。

```javascript
Math.sign = Math.sign || function(x) {
  x = +x; // convert to a number
  if (x === 0 || isNaN(x)) {
    return x;
  }
  return x > 0 ? 1 : -1;
};
```

Math.cbrt() 用来计算一个数的立方根

```javascript
Math.cbrt = Math.cbrt || function(x) {
  var y = Math.pow(Math.abs(x), 1/3);
  return x < 0 ? -y : y;
};
```

Math.clz32() 将参数转为32位无符号整数。

Math.imul() 返回两个数以32位带符号整数形式相乘的结果。

Math.fround 返回一个数的32位单精度浮点数形式

```javascript
Math.fround = Math.fround || function (x) {
  return new Float32Array([x])[0];
};
```

Math.hypot 方法返回所有参数的平方和的平方根。

### 对数方法

`Math.expm1(x)`返回 ex - 1，即`Math.exp(x) - 1`。

`Math.log1p(x)`方法返回`1 + x`的自然对数，即`Math.log(1 + x)`。

`Math.log10(x)`返回以 10 为底的`x`的对数。

`Math.log2(x)`返回以 2 为底的`x`的对数。

### 双曲函数方法

- `Math.sinh(x)` 返回`x`的双曲正弦（hyperbolic sine）
- `Math.cosh(x)` 返回`x`的双曲余弦（hyperbolic cosine）
- `Math.tanh(x)` 返回`x`的双曲正切（hyperbolic tangent）
- `Math.asinh(x)` 返回`x`的反双曲正弦（inverse hyperbolic sine）
- `Math.acosh(x)` 返回`x`的反双曲余弦（inverse hyperbolic cosine）
- `Math.atanh(x)` 返回`x`的反双曲正切（inverse hyperbolic tangent）

### 指数运算

```
2 ** 2 // 4
2 ** 3 // 8
```

## 函数扩展

##### ES6允许函数有默认值

```javascript
// 写法一
function m1({x = 0, y = 0} = {}) {
  return [x, y];
}

// 写法二
function m2({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}

//区别是写法一函数参数的默认值是空对象，但是设置了对象解构赋值的默认值；写法二函数参数的默认值是一个有具体属性的对象，但是没有设置对象解构赋值的默认值。
```

##### 函数length属性

```javascript
(function (a) {}).length // 1
(function (a = 5) {}).length // 0
(function (a, b, c = 5) {}).length // 2
```

##### 作用域问题

```javascript
var x = 1;
function f(x, y = x) {
  console.log(y);
}
f(2) // 2

let x = 1;
function f(y = x) {
  let x = 2;
  console.log(y);
}
f() // 1

function f(y = x) {
  let x = 2;
  console.log(y);
}
f() // ReferenceError: x is not defined

var x = 1;
function foo(x = x) {
  // ...
}
foo() // ReferenceError: x is not defined

let foo = 'outer';
function bar(func = () => foo) {
  let foo = 'inner';
  console.log(func());
}
bar(); // outer

function bar(func = () => foo) {
  let foo = 'inner';
  console.log(func());
}
bar() // ReferenceError: foo is not defined

var x = 1;
function foo(x, y = function() { x = 2; }) {
  var x = 3;
  y();
  console.log(x);
}
foo() // 3
x // 1

var x = 1;
function foo(x, y = function() { x = 2; }) {
  x = 3;
  y();
  console.log(x);
}
foo() // 2
x // 1
```

利用参数默认值可以指定某一个参数不得省略，如果省略就抛出一个错误。

```javascript
function throwIfMissing() {
	throw new Error('Missing parameter');
}
function foo(mustBeProvided = throwIfMissing()) {
	return mustBeProvided;
}
foo()
// Error: Missing parameter
```

### rest参数

用于获取多余参数。

```javascript
//arguments变量写法
function sortNumbers(){
	return Array.prototype.slice.call(arguments).sort();
}
const sortNumbers = (...numbers) => numbers.sort();
```

### 严格模式

从ES5开始，函数内部可以设定为严格模式。

从ES6开始，规定只要函数参数使用了默认值、解构赋值或者扩展运算符，那么函数内部就不能显式设定为严格模式。

### name属性

name属性返回该函数的函数名。

### 箭头函数

```javascript
var f = () => 5;

var f = function () {return 5;}

var sum = (num1, num2) => num1 +num2;

var sum = function(num1,num2){
  return num1 + num2;
}

//箭头函数结合解构
const full = ({first ,last}) => first + '' + last;

function full(person) {
  return person.first + '' + person.last;
}

//rest参数与箭头函数结合
const numbers = (...nums) => nums;

numbers(1,2,3,4,5);
// [1,2,3,4,5]

const headAndTail = (head, ...tail) => [head, tail];

headAndTail(1,2,3,4,5)
//[1,[2,3,4,5]]

```

### 注意事项

1.函数体内的this对象就是定义时所在的对象，而不是使用时所在的对象。**由于箭头函数没有自己的this，也就不能用call()、apply()、bind()。**

2.不可以当作构造函数，不可以使用new命令，否则会抛出一个错误。

3.不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以使用rest参数代替。

4.不可以使用yield命令，因此箭头函数不能用做generator。



### 绑定this(ES7)

```javascript
foo::bar;
//等同于
bar.bind(foo);

foo::bar(...arguments);
//等同于
bar.apply(foo,arguments);

const hasOwnProperty = Object.prototype.hasOwnProperty;
function hasOwn(obj, key){
  return obj::hasOwnProperty(key);
}

var method = obj::obj.foo;
//等同于
var method = ::obj.foo;

let log = ::console.log;
//等同于
var log = console.log.bind(console);

//例一
import { map, takeWhile, forEach} from "iterlib";

getPlayers()
::map(x => x.character())
::takeWhile(x => x.strength > 100)
::forEach(x => console.log(x));
//例二
let { find, html } = jake;
document.quertSelectorAll("div.myClass")
::find("p")
::html("hahaha");
```

### 尾调用优化

尾调用：函数的最后一步是调用另一个函数。

```javascript
function f(x) {
  return g(x);
}

function f(x) {
  let y = g(x);
  return y;
}

function f(x) {
  return g(x) + 1;
}

function f(x) {
  g(x);
}
//等同于
function f(x) {
  g(x);
  return undefined;
}
```

尾调用不一定出现在函数尾部，只要是最后一步操作就好。

**注意：只有不再用到外层函数的内部变量，内层函数的调用的帧才会取代外层函数的调用帧，否则就无法进行"尾调用优化"。**

例如

```javascript
function addOne(a) {
  var one = 1;
  function inner(b) {
    return b + one;
  }
  return inner(a);
}
```

**在ES6中，只要使用尾递归，就不会发生栈溢出，相对节省内存。**

#### 蹦床函数

```javascript
function trampoline(f) {
	while(f & f instanceof Function) {
    f = f();
  }
  return f;
}
//蹦床函数
function sum(x, y) {
  if( y>0 ){
    return sum.bind(null, x+1, y-1);
  } else {
    return x;
  }
}

trampoline(sum(1,100000));

```

尾递归优化

```javascript
function tco(f) {
	var value;
  var active = false;
  var accumulated = [];
  
  return function accumulator() {
    	accumulated.push(arguments);
    	if(!active) {
        active = true;
        while (accumulated.length) {
          value = f.apply(this,accumulated.shift());
        }
        active = false;
        return value;
      }
  };
}

var sum =  tco(function(x,y) {
  if ( y > 0 ){
    return sum(x+1,y-1);
  }else{
    return x;
  }
});

sum(1,100000);
//100001
```

### 代替数组的apply方法

```javascript
//ES5
new (Date.bind.apply(Date, [null, 2015, 1, 1]))
//ES6
new Date(...[2015, 1, 1]);
```

## 数组扩展

### 扩展运算符

...

### Array.from()

```javascript
let arrayLike = {
  '0' : 'a',
  '1' : 'b',
  '2' : 'c',
  length: 3
}

var arr1 = [].slice.call(arrayLike); // ['a', 'b', 'c']
let arr2 = Array.from(arrayLike); // ['a','b','c']
```

**任何有length属性的对象，都可以通过Array.from方法转换为数组，而这种情况扩展运算符无法转换。**

### Array.of()

```javascript
Array.of(3, 11, 8); // [3, 11, 8]
Array.of(3) //[3]
Array.of(3).length //1
Array.of(undefined) //[undefined]
```

### copyWith()

数组实例的`copyWithin()`方法，在当前数组内部，将指定位置的成员复制到其他位置（会覆盖原有成员），然后返回当前数组。也就是说，使用这个方法，会修改当前数组。

```javascript
Array.prototype.copyWithin(target, start = 0, end = this.length)

//target（必需）：从该位置开始替换数据。如果为负值，表示倒数。
//start（可选）：从该位置开始读取数据，默认为 0。如果为负值，表示从末尾开始计算。
//end（可选）：到该位置前停止读取数据，默认等于数组长度。如果为负值，表示从末尾开始计算。

// 将3号位复制到0号位
[1, 2, 3, 4, 5].copyWithin(0, 3, 4)
// [4, 2, 3, 4, 5]

// -2相当于3号位，-1相当于4号位
[1, 2, 3, 4, 5].copyWithin(0, -2, -1)
// [4, 2, 3, 4, 5]

// 将3号位复制到0号位
[].copyWithin.call({length: 5, 3: 1}, 0, 3)
// {0: 1, 3: 1, length: 5}

// 将2号位到数组结束，复制到0号位
let i32a = new Int32Array([1, 2, 3, 4, 5]);
i32a.copyWithin(0, 2);
// Int32Array [3, 4, 5, 4, 5]

// 对于没有部署 TypedArray 的 copyWithin 方法的平台
// 需要采用下面的写法
[].copyWithin.call(new Int32Array([1, 2, 3, 4, 5]), 0, 3, 4);
// Int32Array [4, 2, 3, 4, 5]
```

### 数组实例的 find() 和 findIndex()

```javascript
[1, 4, -5, 10].find((n) => n < 0)
// -5

[1, 5, 10, 15].find(function(value, index, arr) {
  return value > 9;
}) // 10

[1, 5, 10, 15].findIndex(function(value, index, arr) {
  return value > 9;
}) // 2

function f(v){
  return v > this.age;
}
let person = {name: 'John', age: 20};
[10, 12, 26, 15].find(f, person);    // 26

[NaN].indexOf(NaN)
// -1

[NaN].findIndex(y => Object.is(NaN, y))
// 0
```

这两个方法都可以发现`NaN`，弥补了数组的`indexOf`方法的不足。

### 数组实例的 fill()

```javascript
['a', 'b', 'c'].fill(7)
// [7, 7, 7]

new Array(3).fill(7)
// [7, 7, 7]
```

### 数组实例的 entries()，keys() 和 values()

```javascript
for (let index of ['a', 'b'].keys()) {
  console.log(index);
}
// 0
// 1

for (let elem of ['a', 'b'].values()) {
  console.log(elem);
}
// 'a'
// 'b'

for (let [index, elem] of ['a', 'b'].entries()) {
  console.log(index, elem);
}
// 0 "a"
// 1 "b"

let letter = ['a', 'b', 'c'];
let entries = letter.entries();
console.log(entries.next().value); // [0, 'a']
console.log(entries.next().value); // [1, 'b']
console.log(entries.next().value); // [2, 'c']
```

### 数组实例的 includes()

```javascript
[1, 2, 3].includes(2)     // true
[1, 2, 3].includes(4)     // false
[1, 2, NaN].includes(NaN) // true

[1, 2, 3].includes(3, 3);  // false
[1, 2, 3].includes(3, -1); // true
//第二个参数为第一个要查找参数的位置。

```

#### 检查是否支持includes方法

```javascript
const contains = (() =>
  Array.prototype.includes
    ? (arr, value) => arr.includes(value)
    : (arr, value) => arr.some(el => el === value)
)();
contains(['foo', 'bar'], 'baz'); // => false
```

- Map 结构的`has`方法，是用来查找键名的，比如`Map.prototype.has(key)`、`WeakMap.prototype.has(key)`、`Reflect.has(target, propertyKey)`。
- Set 结构的`has`方法，是用来查找值的，比如`Set.prototype.has(value)`、`WeakSet.prototype.has(value)`。

### 数组实例的 flat()，flatMap()

```javascript
[1, 2, [3, 4]].flat()
// [1, 2, 3, 4]
[1, [2, [3]]].flat(Infinity)
// [1, 2, 3]
```

```javascript
// 相当于 [[2, 4], [3, 6], [4, 8]].flat()
[2, 3, 4].flatMap((x) => [x, x * 2])
// [2, 4, 3, 6, 4, 8]
```

```javascript
// 相当于 [[[2]], [[4]], [[6]], [[8]]].flat()
[1, 2, 3, 4].flatMap(x => [[x * 2]])
// [[2], [4], [6], [8]]
```

```javascript
arr.flatMap(function callback(currentValue[, index[, array]]) {
  // ...
}[, thisArg])
```

### 数组空位

- `forEach()`, `filter()`, `reduce()`, `every()` 和`some()`都会跳过空位。
- `map()`会跳过空位，但会保留这个值
- `join()`和`toString()`会将空位视为`undefined`，而`undefined`和`null`会被处理成空字符串。

```javascript
// forEach方法
[,'a'].forEach((x,i) => console.log(i)); // 1

// filter方法
['a',,'b'].filter(x => true) // ['a','b']

// every方法
[,'a'].every(x => x==='a') // true

// reduce方法
[1,,2].reduce((x,y) => x+y) // 3

// some方法
[,'a'].some(x => x !== 'a') // false

// map方法
[,'a'].map(x => 1) // [,1]

// join方法
[,'a',undefined,null].join('#') // "#a##"

// toString方法
[,'a',undefined,null].toString() // ",a,,"
```

1.扩展运算符（`...`）也会将空位转为`undefined`。

2.`copyWithin()`会连空位一起拷贝。

3.`fill()`会将空位视为正常的数组位置。

4.`for...of`循环也会遍历空位。

5.`entries()`、`keys()`、`values()`、`find()`和`findIndex()`会将空位处理成`undefined`。

### Array.prototype.sort() 的排序稳定性

[ES2019](https://github.com/tc39/ecma262/pull/1340) 明确规定，`Array.prototype.sort()`的默认排序算法必须稳定。这个规定已经做到了，现在 JavaScript 各个主要实现的默认排序算法都是稳定的。

## 对象的扩展

```javascript
function f(x, y) {
  return {x, y};
}

// 等同于

function f(x, y) {
  return {x: x, y: y};
}

f(1, 2) // Object {x: 1, y: 2}
```

```javascript
// 方法一
obj.foo = true;

// 方法二
obj['a' + 'bc'] = 123;

let propKey = 'foo';

let obj = {
  [propKey]: true,
  ['a' + 'bc']: 123
};


let lastWord = 'last word';

const a = {
  'first word': 'hello',
  [lastWord]: 'world'
};

a['first word'] // "hello"
a[lastWord] // "world"
a['last word'] // "world"

let obj = {
  ['h' + 'ello']() {
    return 'hi';
  }
};

obj.hello() // hi
```

属性名表达式与简洁表示法，不能同时使用，会报错。

### 可枚举性

```javascript
let obj = { foo: 123 };
Object.getOwnPropertyDescriptor(obj, 'foo')
//  {
//    value: 123,
//    writable: true,
//    enumerable: true,
//    configurable: true
//  }
```

有四个操作会忽略`enumerable`为`false`的属性。

- `for...in`循环：只遍历对象自身的和继承的可枚举的属性。
- `Object.keys()`：返回**对象自身的所有**可枚举的属性的键名。
- `JSON.stringify()`：只串行化对象自身的可枚举的属性。
- `Object.assign()`： 忽略`enumerable`为`false`的属性，只拷贝对象自身的可枚举的属性。

### 属性的遍历

**（1）for...in**

`for...in`循环遍历对象自身的和继承的可枚举属性（不含 Symbol 属性）。

**（2）Object.keys(obj)**

`Object.keys`返回一个数组，包括对象自身的（不含继承的）所有可枚举属性（不含 Symbol 属性）的键名。

**（3）Object.getOwnPropertyNames(obj)**

`Object.getOwnPropertyNames`返回一个数组，包含对象自身的所有属性（不含 Symbol 属性，但是包括不可枚举属性）的键名。

**（4）Object.getOwnPropertySymbols(obj)**

`Object.getOwnPropertySymbols`返回一个数组，包含对象自身的所有 Symbol 属性的键名。

**（5）Reflect.ownKeys(obj)**

`Reflect.ownKeys`返回一个数组，包含对象自身的所有键名，不管键名是 Symbol 或字符串，也不管是否可枚举。

以上的 5 种方法遍历对象的键名，都遵守同样的属性遍历的次序规则。

- 首先遍历所有数值键，按照数值升序排列。
- 其次遍历所有字符串键，按照加入时间升序排列。
- 最后遍历所有 Symbol 键，按照加入时间升序排列。

```javascript
Reflect.ownKeys({ [Symbol()]:0, b:0, 10:0, 2:0, a:0 })
// ['2', '10', 'b', 'a', Symbol()]
```

### `super`，指向当前对象的原型对象。

JavaScript 引擎内部，`super.foo`等同于`Object.getPrototypeOf(this).foo`（属性）或`Object.getPrototypeOf(this).foo.call(this)`（方法）。

### 完整克隆一个对象

```javascript
// 写法一
const clone1 = {
  __proto__: Object.getPrototypeOf(obj),
  ...obj
};

// 写法二
const clone2 = Object.assign(
  Object.create(Object.getPrototypeOf(obj)),
  obj
);

// 写法三
const clone3 = Object.create(
  Object.getPrototypeOf(obj),
  Object.getOwnPropertyDescriptors(obj)
)
```

上面代码中，写法一的`__proto__`属性在非浏览器的环境不一定部署，因此推荐使用写法二和写法三。

```javascript
//合并对象
let ab = { ...a, ...b };
// 等同于
let ab = Object.assign({}, a, b);
```

[ES2020](https://github.com/tc39/proposal-optional-chaining) 引入了“链判断运算符”（optional chaining operator）`?.`

```javascript
const firstName = message?.body?.user?.firstName || 'default';
const fooValue = myForm.querySelector('input[name=foo]')?.value
```

上面代码使用了`?.`运算符，直接在链式调用的时候判断，左侧的对象是否为`null`或`undefined`。如果是的，就不再往下运算，而是返回`undefined`。

链判断运算符有三种用法。

- `obj?.prop` // 对象属性
- `obj?.[expr]` // 同上
- `func?.(...args)` // 函数或对象方法的调用

下面是判断对象方法是否存在，如果存在就立即执行的例子。

```javascript
iterator.return?.()
```

```javascript
a?.b
// 等同于
a == null ? undefined : a.b

a?.[x]
// 等同于
a == null ? undefined : a[x]

a?.b()
// 等同于
a == null ? undefined : a.b()

a?.()
// 等同于
a == null ? undefined : a()
```

（1）短路机制

```javascript
a?.[++x]
// 等同于
a == null ? undefined : a[++x]
```

上面代码中，如果`a`是`undefined`或`null`，那么`x`不会进行递增运算。也就是说，链判断运算符一旦为真，右侧的表达式就不再求值。

（2）delete 运算符

```javascript
delete a?.b
// 等同于
a == null ? undefined : delete a.b
```

上面代码中，如果`a`是`undefined`或`null`，会直接返回`undefined`，而不会进行`delete`运算。

（3）括号的影响

如果属性链有圆括号，链判断运算符对圆括号外部没有影响，只对圆括号内部有影响。

```javascript
(a?.b).c
// 等价于
(a == null ? undefined : a.b).c
```

上面代码中，`?.`对圆括号外部没有影响，不管`a`对象是否存在，圆括号后面的`.c`总是会执行。

一般来说，使用`?.`运算符的场合，不应该使用圆括号。

### Null 判断运算符 ？？

## 对象的新增方法

### Object.is()

```javascript
Object.is('foo', 'foo')
// true
Object.is({}, {})
// false
```

```javascript
+0 === -0 //true
NaN === NaN // false

Object.is(+0, -0) // false
Object.is(NaN, NaN) // true
```

#### ES部署is方法

```javascript
Object.defineProperty(Object, 'is', {
  value: function(x, y) {
    if (x === y) {
      // 针对+0 不等于 -0的情况
      return x !== 0 || 1 / x === 1 / y;
    }
    // 针对NaN的情况
    return x !== x && y !== y;
  },
  configurable: true,
  enumerable: false,
  writable: true
});
```

### Object.assign()

```javascript
const target = { a: 1 };

const source1 = { b: 2 };
const source2 = { c: 3 };

Object.assign(target, source1, source2);
target // {a:1, b:2, c:3}
```

其他类型的值（即数值、字符串和布尔值）不在首参数，也不会报错。但是，除了字符串会以数组形式，拷贝入目标对象，其他值都不会产生效果。

### assign注意

**（1）浅拷贝**

```javascript
const obj1 = {a: {b: 1}};
const obj2 = Object.assign({}, obj1);

obj1.a.b = 2;
obj2.a.b // 2
```

**（2）同名属性的替换**

```javascript
const target = { a: { b: 'c', d: 'e' } }
const source = { a: { b: 'hello' } }
Object.assign(target, source)
// { a: { b: 'hello' } }
```

**（3）数组的处理**

```javascript
Object.assign([1, 2, 3], [4, 5])
// [4, 5, 3]
```

**（4）取值函数的处理**

```javascript
const source = {
  get foo() { return 1 }
};
const target = {};

Object.assign(target, source)
// { foo: 1 }
```

### assign应用

**（1）为对象添加属性**

```javascript
class Point {
  constructor(x, y) {
    Object.assign(this, {x, y});
  }
}
```

**（2）为对象添加方法**

```javascript
Object.assign(SomeClass.prototype, {
  someMethod(arg1, arg2) {
    ···
  },
  anotherMethod() {
    ···
  }
});

// 等同于下面的写法
SomeClass.prototype.someMethod = function (arg1, arg2) {
  ···
};
SomeClass.prototype.anotherMethod = function () {
  ···
};
```

**（3）克隆对象**

```javascript
function clone(origin) {
  return Object.assign({}, origin);
}
```

```javascript
function clone(origin) {
  let originProto = Object.getPrototypeOf(origin);
  return Object.assign(Object.create(originProto), origin);
}
```

**（4）合并多个对象**

```javascript
const merge =
  (target, ...sources) => Object.assign(target, ...sources);
```

```javascript
const merge =
  (...sources) => Object.assign({}, ...sources);
```

**（5）为属性指定默认值**

```javascript
const DEFAULTS = {
  logLevel: 0,
  outputFormat: 'html'
};

function processContent(options) {
  options = Object.assign({}, DEFAULTS, options);
  console.log(options);
  // ...
}
```

### Object.getOwnPropertyDescriptors()

```javascript
const obj = {
  foo: 123,
  get bar() { return 'abc' }
};

Object.getOwnPropertyDescriptors(obj)
// { foo:
//    { value: 123,
//      writable: true,
//      enumerable: true,
//      configurable: true },
//   bar:
//    { get: [Function: get bar],
//      set: undefined,
//      enumerable: true,
//      configurable: true } }
```

```javascript
function getOwnPropertyDescriptors(obj) {
  const result = {};
  for (let key of Reflect.ownKeys(obj)) {
    result[key] = Object.getOwnPropertyDescriptor(obj, key);
  }
  return result;
}
```

该方法的引入目的，主要是为了解决`Object.assign()`无法正确拷贝`get`属性和`set`属性的问题。

```javascript
const source = {
  set foo(value) {
    console.log(value);
  }
};

const target2 = {};
Object.defineProperties(target2, Object.getOwnPropertyDescriptors(source));
Object.getOwnPropertyDescriptor(target2, 'foo')
// { get: undefined,
//   set: [Function: set foo],
//   enumerable: true,
//   configurable: true }
```

上面代码中，两个对象合并的逻辑可以写成一个函数。

```javascript
const shallowMerge = (target, source) => Object.defineProperties(
  target,
  Object.getOwnPropertyDescriptors(source)
);
const clone = Object.create(Object.getPrototypeOf(obj),

Object.getOwnPropertyDescriptors(obj));

// 或者

const shallowClone = (obj) => Object.create(
  Object.getPrototypeOf(obj),
  Object.getOwnPropertyDescriptors(obj)
);
```

### `__proto__`属性，Object.setPrototypeOf()，Object.getPrototypeOf() 

```javascript
// es5 的写法
const obj = {
  method: function() { ... }
};
obj.__proto__ = someOtherObj;

// es6 的写法
var obj = Object.create(someOtherObj);
obj.method = function() { ... };
```



```javascript
// 格式
Object.setPrototypeOf(object, prototype)

// 用法
const o = Object.setPrototypeOf({}, null);

function setPrototypeOf(obj, proto) {
  obj.__proto__ = proto;
  return obj;
}

let proto = {};
let obj = { x: 10 };
Object.setPrototypeOf(obj, proto);

proto.y = 20;
proto.z = 40;

obj.x // 10
obj.y // 20
obj.z // 40
```





```javascript
function Rectangle() {
  // ...
}

const rec = new Rectangle();

Object.getPrototypeOf(rec) === Rectangle.prototype
// true

Object.setPrototypeOf(rec, Object.prototype);
Object.getPrototypeOf(rec) === Rectangle.prototype
// false
```

如果参数不是对象，会被自动转为对象。

### Object.keys()，Object.values()，Object.entries()，Object.fromEntries() 

`Object.fromEntries()`方法是`Object.entries()`的逆操作，用于将一个键值对数组转为对象。

```javascript
Object.fromEntries([
  ['foo', 'bar'],
  ['baz', 42]
])
// { foo: "bar", baz: 42 }
```



## Symbol

```javascript
let s = Symbol();

typeof s
// "symbol"

let s1 = Symbol('foo');
let s2 = Symbol('bar');

s1 // Symbol(foo)
s2 // Symbol(bar)

s1.toString() // "Symbol(foo)"
s2.toString() // "Symbol(bar)"

const obj = {
  toString() {
    return 'abc';
  }
};
const sym = Symbol(obj);
sym  //Symbol(abc),会调用toString()方法将对象转换为字符串

let s1 = Symbol();
let s2 = Symbol();

s1 === s2; //false

let s1 = Symbol('foo');
let s2 = Symbol('foo');

s1 === s2; //false
//Symbol不会相等

let sym = Symbol('My symbol');
"your symbol is " + sym;
//TypeError: can't convert symbol to string
`your symbol is $(sym)`
//TypeError: can't convert symbol to string
//Symbol值不能和其他类型进行运算

String(sym) //'Symbol(My symbol)'
sym.toString(sym)  // 'Symbol(My symbol)'
//Symbol值可以显式转换为字符串
//另外Symbol值可以转换为布尔值，但是不能转换为数值。

const sym = Symbol('foo')

sym.description // "foo"

let mySymbol =  Symbol();

//第一种写法
let a = {};
a[mySymbol] = 'Hello!';

//第二种写法
let a = {
  [mySymbol]: 'Hello!';
}

//第三种写法
let a = {}
Object.defineProperty(a,mySymbol, {value: 'Hello!'});

const mySymbol = Symbol();
const a = {};
a.mySymbol = 'Hello!' ;
a[mySymbol] //undefined
a['mySymbol'] // "Hello!"


//在对象的内部，使用Symbol值定义属性时，Symbol值必须放在方括号之中
let s = Symbol();

let obj = {
  [s]: function (arg) { ... }
};
obj[s](123)
//等价于
let obj = {
  [s](arg){ ... }
};
```

### 消除魔术数字

```javascript
//魔术字符串指的是，在代码中多次出现，与代码形成强耦合的某一个具体的字符串或数值。
function getArea(shape, options) {
  let area = 0;
  
  switch (shape) {
    case 'Triangle':
      area = .5 * options.width * options.height;
      break;
  }
  return area;
}
getArea('Triangle', {width:100, height:100}); //魔术字符串

//消除魔术字符串
const shapeType = {
  triangle: 'Triangle'
};
function getArea(shape,options){
  let area = 0;
  switch (shape) {
    case shapeType.triangle:
      area =.5*options.width*options.height;
      break;
  }
  return area;
}
getArea(shapeType.triangle, {width: 100, height: 100});


```

### 属性名的遍历

Symbol作为属性名，遍历对象的时候，该属性不会出现在for...in、for...of循环中，也不会被Object.keys()、Object.getOwnpropertyNames()、JSON.stringify()返回。

```javascript
const obj = {};
let a = Symbol('a');
let b = Symbol('b');

obj[a] = 'Hello';
obj[b] = 'World';

const objectSymbols = Object.getOwnPropertySymbols(obj);
//获取指定对象的所有Symbol属性名
objectSymbols
//[Symbol(a),Symbol(b)];

const obj = {};
const foo = Symbol('foo');
obj[foo] = 'bar';

for(let i in obj){
  console.log(i);
}

Object.getOwnPropertyNames(obj); // []
Object.getOwnPropertySymbols(obj); // [Symbol(foo)]

let obj = {
  [Symbol('my_key')]:1,
  enum: 2,
  nonEnum: 3
};
Reflect.ownKeys(obj);


```

### 需要使用同一个Symbol值的时候

```javascript
let s1 = Symbol.for('foo');
let s2 = Symbol.for('foo');

s1 === s2; //true;
```

Symbol.for()方法会被登记在全局环境中提供搜索，Symbol()方法不会。Symbol.for()方法不会每次调用就返回一个新的Symbol类型的值，而是会先检查给定的key是否已经存在，如果不存在才会创建一个新值。

```javascript
//Symbol.keyFor()方法返回一个已经登记的Symbol类型值的key
let s1 = Symbol.for("foo");
Symbol.keyFor(s1) //"foo"

let s2 = Symbol.for("foo");
Symbol.keyFor(s2) //undefined
```

### 单例

```javascript
const FOO_KEY = Symbol.for('foo');

function A(){
	this.foo = 'hello';
}
if(!global[FOO_KEY]){
	global[FOO_KEY] = new A{};
}

model.exports = global[FOO_KEY];
```

### Symbol.hasInstance

```javascript
class MyClass {
	[Symbol.hasInstance](foo){
		return foo instanceof Array;
	}
}
[1,2,3] instanceof new MyClass() //true

class Even {
  static [Symbol.hasInstance](obj) {
    return Number(obj) % 2 === 0;
  }
}

// 等同于
const Even = {
  [Symbol.hasInstance](obj) {
    return Number(obj) % 2 === 0;
  }
};

1 instanceof Even // false
2 instanceof Even // true
12345 instanceof Even // false
```

### Symbol.isConcatSpreadable

```javascript
let arr1 = ['c', 'd'];
['a', 'b'].concat(arr1, 'e') // ['a', 'b', 'c', 'd', 'e']
arr1[Symbol.isConcatSpreadable] // undefined

let arr2 = ['c', 'd'];
arr2[Symbol.isConcatSpreadable] = false;
['a', 'b'].concat(arr2, 'e') // ['a', 'b', ['c','d'], 'e']
```

### Symbol.species

```javascript
class T1 extends Promise {
}

class T2 extends Promise {
  static get [Symbol.species]() {
    return Promise;
  }
}

new T1(r => r()).then(v => v) instanceof T1 // true
new T2(r => r()).then(v => v) instanceof T2 // false
```

### Symbol.match

```javascript
String.prototype.match(regexp)
// 等同于
regexp[Symbol.match](this)

class MyMatcher {
  [Symbol.match](string) {
    return 'hello world'.indexOf(string);
  }
}

'e'.match(new MyMatcher()) // 1
```

### Symbol.replace

```javascript
const x = {};
x[Symbol.replace] = (...s) => console.log(s);

'Hello'.replace(x, 'World') // ["Hello", "World"]
```

`Symbol.replace`方法会收到两个参数，第一个参数是`replace`方法正在作用的对象，上面例子是`Hello`，第二个参数是替换后的值，上面例子是`World`。

### Symbol.search

### Symbol.split

### Symbol.iterator

### Symbol.toPrimitive

### Symbol.toStringTag

### Symbol.unscopables

## Set 和 Map 数据结构

### Set

```javascript
const s = new Set();

[2, 3, 5, 4, 5, 2, 2].forEach(x => s.add(x));

for (let i of s) {
  console.log(i);
}
// 2 3 5 4
// 例一
const set = new Set([1, 2, 3, 4, 4]);
[...set]
// [1, 2, 3, 4]

// 例二
const items = new Set([1, 2, 3, 4, 5, 5, 5, 5]);
items.size // 5

// 例三
const set = new Set(document.querySelectorAll('div'));
set.size // 56

// 类似于
const set = new Set();
document
 .querySelectorAll('div')
 .forEach(div => set.add(div));
set.size // 56

// 去除数组的重复成员
[...new Set(array)]

[...new Set('ababbc')].join('')
// "abc"

let set = new Set();
let a = NaN;
let b = NaN;
set.add(a);
set.add(b);
set // Set {NaN}
//在set内部NAN相等

let set = new Set();

set.add({});
set.size // 1

set.add({});
set.size // 2
//在set里面{}永不相等

```

### Set 实例的属性和方法

Set 结构的实例有以下属性。

- `Set.prototype.constructor`：构造函数，默认就是`Set`函数。
- `Set.prototype.size`：返回`Set`实例的成员总数。

Set 实例的方法分为两大类：操作方法（用于操作数据）和遍历方法（用于遍历成员）。下面先介绍四个操作方法。

- `Set.prototype.add(value)`：添加某个值，返回 Set 结构本身。
- `Set.prototype.delete(value)`：删除某个值，返回一个布尔值，表示删除是否成功。
- `Set.prototype.has(value)`：返回一个布尔值，表示该值是否为`Set`的成员。
- `Set.prototype.clear()`：清除所有成员，没有返回值。

Set 结构的实例有四个遍历方法，可以用于遍历成员。

- `Set.prototype.keys()`：返回键名的遍历器
- `Set.prototype.values()`：返回键值的遍历器
- `Set.prototype.entries()`：返回键值对的遍历器
- `Set.prototype.forEach()`：使用回调函数遍历每个成员

需要特别指出的是，`Set`的遍历顺序就是插入顺序。这个特性有时非常有用，比如使用 Set 保存一个回调函数列表，调用时就能保证按照添加顺序调用。

### WeakSet

WeakSet 结构与 Set 类似，也是不重复的值的集合。但是，它与 Set 有两个区别。

首先，WeakSet 的成员**只能是对象**，而不能是其他类型的值。

```javascript
const ws = new WeakSet();
ws.add(1)
// TypeError: Invalid value used in weak set
ws.add(Symbol())
// TypeError: invalid value used in weak set
```

其次，WeakSet 中的对象都是弱引用，即垃圾回收机制不考虑 WeakSet 对该对象的引用，也就是说，如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，**不考虑该对象还存在于 WeakSet 之中**。

API和Set一样

### Map

JavaScript 的对象（**Object**），本质上是键值对的集合（Hash 结构），但是传统上只能用**字符串当作键**。

Map 数据结构“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。

```javascript
const m = new Map();
const o = {p: 'Hello World'};

m.set(o, 'content')
m.get(o) // "content"

m.has(o) // true
m.delete(o) // true
m.has(o) // false
```

## Proxy

```javascript
var obj = new Proxy({}, {
  get: function (target, propKey, receiver) {
    console.log(`getting ${propKey}!`);
    return Reflect.get(target, propKey, receiver);
  },
  set: function (target, propKey, value, receiver) {
    console.log(`setting ${propKey}!`);
    return Reflect.set(target, propKey, value, receiver);
  }
});
```

```javascript
var handler = {
  get: function(target, name) {
    if (name === 'prototype') {
      return Object.prototype;
    }
    return 'Hello, ' + name;
  },

  apply: function(target, thisBinding, args) {
    return args[0];
  },

  construct: function(target, args) {
    return {value: args[1]};
  }
};

var fproxy = new Proxy(function(x, y) {
  return x + y;
}, handler);

fproxy(1, 2) // 1
new fproxy(1, 2) // {value: 2}
fproxy.prototype === Object.prototype // true
fproxy.foo === "Hello, foo" // true
```

下面是 Proxy 支持的拦截操作一览，一共 13 种。

- **get(target, propKey, receiver)**：拦截对象属性的读取，比如`proxy.foo`和`proxy['foo']`。
- **set(target, propKey, value, receiver)**：拦截对象属性的设置，比如`proxy.foo = v`或`proxy['foo'] = v`，返回一个布尔值。
- **has(target, propKey)**：拦截`propKey in proxy`的操作，返回一个布尔值。
- **deleteProperty(target, propKey)**：拦截`delete proxy[propKey]`的操作，返回一个布尔值。
- **ownKeys(target)**：拦截`Object.getOwnPropertyNames(proxy)`、`Object.getOwnPropertySymbols(proxy)`、`Object.keys(proxy)`、`for...in`循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而`Object.keys()`的返回结果仅包括目标对象自身的可遍历属性。
- **getOwnPropertyDescriptor(target, propKey)**：拦截`Object.getOwnPropertyDescriptor(proxy, propKey)`，返回属性的描述对象。
- **defineProperty(target, propKey, propDesc)**：拦截`Object.defineProperty(proxy, propKey, propDesc）`、`Object.defineProperties(proxy, propDescs)`，返回一个布尔值。
- **preventExtensions(target)**：拦截`Object.preventExtensions(proxy)`，返回一个布尔值。
- **getPrototypeOf(target)**：拦截`Object.getPrototypeOf(proxy)`，返回一个对象。
- **isExtensible(target)**：拦截`Object.isExtensible(proxy)`，返回一个布尔值。
- **setPrototypeOf(target, proto)**：拦截`Object.setPrototypeOf(proxy, proto)`，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。
- **apply(target, object, args)**：拦截 Proxy 实例作为函数调用的操作，比如`proxy(...args)`、`proxy.call(object, ...args)`、`proxy.apply(...)`。
- **construct(target, args)**：拦截 Proxy 实例作为构造函数调用的操作，比如`new proxy(...args)`。



#### 下面的例子则是利用`get`拦截，实现一个生成各种 DOM 节点的通用函数`dom`

```javascript
const dom = new Proxy({}, {
  get(target, property) {
    return function(attrs = {}, ...children) {
      const el = document.createElement(property);
      for (let prop of Object.keys(attrs)) {
        el.setAttribute(prop, attrs[prop]);
      }
      for (let child of children) {
        if (typeof child === 'string') {
          child = document.createTextNode(child);
        }
        el.appendChild(child);
      }
      return el;
    }
  }
});

const el = dom.div({},
  'Hello, my name is ',
  dom.a({href: '//example.com'}, 'Mark'),
  '. I like:',
  dom.ul({},
    dom.li({}, 'The web'),
    dom.li({}, 'Food'),
    dom.li({}, '…actually that\'s it')
  )
);

document.body.appendChild(el);
```

## Reflect

（1） 将`Object`对象的一些明显属于语言内部的方法（比如`Object.defineProperty`），放到`Reflect`对象上。现阶段，某些方法同时在`Object`和`Reflect`对象上部署，未来的新方法将只部署在`Reflect`对象上。也就是说，从`Reflect`对象上可以拿到语言内部的方法。

（2） 修改某些`Object`方法的返回结果，让其变得更合理。比如，`Object.defineProperty(obj, name, desc)`在无法定义属性时，会抛出一个错误，而`Reflect.defineProperty(obj, name, desc)`则会返回`false`。

```javascript
// 老写法
try {
  Object.defineProperty(target, property, attributes);
  // success
} catch (e) {
  // failure
}

// 新写法
if (Reflect.defineProperty(target, property, attributes)) {
  // success
} else {
  // failure
}
```

（3） 让`Object`操作都变成函数行为。某些`Object`操作是命令式，比如`name in obj`和`delete obj[name]`，而`Reflect.has(obj, name)`和`Reflect.deleteProperty(obj, name)`让它们变成了函数行为。

```javascript
// 老写法
'assign' in Object // true

// 新写法
Reflect.has(Object, 'assign') // true
```

（4）`Reflect`对象的方法与`Proxy`对象的方法一一对应，只要是`Proxy`对象的方法，就能在`Reflect`对象上找到对应的方法。这就让`Proxy`对象可以方便地调用对应的`Reflect`方法，完成默认行为，作为修改行为的基础。也就是说，不管`Proxy`怎么修改默认行为，你总可以在`Reflect`上获取默认行为。

`Reflect`对象一共有 13 个静态方法。

- Reflect.apply(target, thisArg, args)
- Reflect.construct(target, args)
- Reflect.get(target, name, receiver)
- Reflect.set(target, name, value, receiver)
- Reflect.defineProperty(target, name, desc)
- Reflect.deleteProperty(target, name)
- Reflect.has(target, name)
- Reflect.ownKeys(target)
- Reflect.isExtensible(target)
- Reflect.preventExtensions(target)
- Reflect.getOwnPropertyDescriptor(target, name)
- Reflect.getPrototypeOf(target)
- Reflect.setPrototypeOf(target, prototype)

## Promise 对象

`Promise`对象有以下两个特点。

（1）对象的状态不受外界影响。`Promise`对象代表一个异步操作，有三种状态：`pending`（进行中）、`fulfilled`（已成功）和`rejected`（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。这也是`Promise`这个名字的由来，它的英语意思就是“承诺”，表示其他手段无法改变。

（2）一旦状态改变，就不会再变，任何时候都可以得到这个结果。`Promise`对象的状态改变，只有两种可能：从`pending`变为`fulfilled`和从`pending`变为`rejected`。只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果，这时就称为 resolved（已定型）。如果改变已经发生了，你再对`Promise`对象添加回调函数，也会立即得到这个结果。这与事件（Event）完全不同，事件的特点是，如果你错过了它，再去监听，是得不到结果的。

`Promise`也有一些缺点。

首先，无法取消`Promise`，一旦新建它就会立即执行，无法中途取消。

其次，如果不设置回调函数，`Promise`内部抛出的错误，不会反应到外部。

第三，当处于`pending`状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

```javascript
const promise = new Promise(function(resolve, reject) {
  // ... some code

  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});
//resolve函数的作用是，将Promise对象的状态从“未完成”变为“成功”（即从 pending 变为 resolved），在异步操作成功时调用，并将异步操作的结果，作为参数传递出去；reject函数的作用是，将Promise对象的状态从“未完成”变为“失败”（即从 pending 变为 rejected），在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。
```

`Promise`实例生成以后，可以用`then`方法分别指定`resolved`状态和`rejected`状态的回调函数。

```javascript
promise.then(function(value) {
  // success
}, function(error) {
  // failure
});
```

Promise 新建后就会立即执行。

#### 异步加载图片

```javascript
function loadImageAsync(url) {
  return new Promise(function(resolve, reject) {
    const image = new Image();

    image.onload = function() {
      resolve(image);
    };

    image.onerror = function() {
      reject(new Error('Could not load image at ' + url));
    };

    image.src = url;
  });
}
```

#### `Promise`对象实现的 Ajax 

```javascript
const getJSON = function(url) {
  const promise = new Promise(function(resolve, reject){
    const handler = function() {
      if (this.readyState !== 4) {
        return;
      }
      if (this.status === 200) {
        resolve(this.response);
      } else {
        reject(new Error(this.statusText));
      }
    };
    const client = new XMLHttpRequest();
    client.open("GET", url);
    client.onreadystatechange = handler;
    client.responseType = "json";
    client.setRequestHeader("Accept", "application/json");
    client.send();

  });

  return promise;
};

getJSON("/posts.json").then(function(json) {
  console.log('Contents: ' + json);
}, function(error) {
  console.error('出错了', error);
});
```

### Promise.prototype.then()

`then`方法返回的是一个新的`Promise`实例（注意，不是原来那个`Promise`实例）。因此可以采用链式写法，即`then`方法后面再调用另一个`then`方法。

### Promise.prototype.catch()

```javascript
// bad
promise
  .then(function(data) {
    // success
  }, function(err) {
    // error
  });

// good
promise
  .then(function(data) { //cb
    // success
  })
  .catch(function(err) {
    // error
  });
//第二种写法要好于第一种写法，理由是第二种写法可以捕获前面then方法执行中的错误，也更接近同步的写法（try/catch）。因此，建议总是使用catch方法，而不使用then方法的第二个参数。
```

### Promise.prototype.finally() 

`finally`方法用于指定不管 Promise 对象最后状态如何，都会执行的操作。

### Promise.all() 

`Promise.all()`方法接受一个数组作为参数，`p1`、`p2`、`p3`都是 Promise 实例，如果不是，就会先调用下面讲到的`Promise.resolve`方法，将参数转为 Promise 实例，再进一步处理。另外，`Promise.all()`方法的参数可以不是数组，但必须具有 Iterator 接口，且返回的每个成员都是 Promise 实例。

`p`的状态由`p1`、`p2`、`p3`决定，分成两种情况。

（1）只有`p1`、`p2`、`p3`的状态都变成`fulfilled`，`p`的状态才会变成`fulfilled`，此时`p1`、`p2`、`p3`的返回值组成一个数组，传递给`p`的回调函数。

（2）只要`p1`、`p2`、`p3`之中有一个被`rejected`，`p`的状态就变成`rejected`，此时第一个被`reject`的实例的返回值，会传递给`p`的回调函数。

```javascript
// 生成一个Promise对象的数组
const promises = [2, 3, 5, 7, 11, 13].map(function (id) {
  return getJSON('/post/' + id + ".json");
});

Promise.all(promises).then(function (posts) {
  // ...
}).catch(function(reason){
  // ...
});
```

如果作为参数的 Promise 实例，自己定义了`catch`方法，那么它一旦被`rejected`，并不会触发`Promise.all()`的`catch`方法。

```javascript
Promise.all = arr => {
    let aResult = [];    //用于存放每次执行后返回结果
    return new _Promise(function (resolve, reject) {
      let i = 0;
      next();    //开始逐次执行数组中的函数
      function next() {
        arr[i].then(function (res) {
          aResult.push(res);    //执行后返回的结果放入数组中
          i++;
          if (i == arr.length) {    //如果函数数组中的函数都执行完，便把结果数组传给then
            resolve(aResult);
          } else {
            next();
          }
        })
      }
    })
  };
```

### Promise.race()

```javascript
const p = Promise.race([p1, p2, p3]);
```

只要`p1`、`p2`、`p3`之中有一个实例率先改变状态，`p`的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给`p`的回调函数。

### Promise.allSettled() 

`Promise.allSettled()`方法接受一组 Promise 实例作为参数，包装成一个新的 Promise 实例。只有等到所有这些参数实例都返回结果，不管是`fulfilled`还是`rejected`，包装实例才会结束。

```javascript
const promises = [
  fetch('/api-1'),
  fetch('/api-2'),
  fetch('/api-3'),
];

await Promise.allSettled(promises);
removeLoadingIndicator();
```

### Promise.any() 

`Promise.any()`方法接受一组 Promise 实例作为参数，包装成一个新的 Promise 实例。只要参数实例有一个变成`fulfilled`状态，包装实例就会变成`fulfilled`状态；如果所有参数实例都变成`rejected`状态，包装实例就会变成`rejected`状态。

```javascript
const promises = [
  fetch('/endpoint-a').then(() => 'a'),
  fetch('/endpoint-b').then(() => 'b'),
  fetch('/endpoint-c').then(() => 'c'),
];
try {
  const first = await Promise.any(promises);
  console.log(first);
} catch (error) {
  console.log(error);
}
```

### Promise应用

#### 加载图片

```javascript
const preloadImage = function (path) {
  return new Promise(function (resolve, reject) {
    const image = new Image();
    image.onload  = resolve;
    image.onerror = reject;
    image.src = path;
  });
};
```

### Generator 函数与 Promise 的结合

```javascript
function getFoo () {
  return new Promise(function (resolve, reject){
    resolve('foo');
  });
}

const g = function* () {
  try {
    const foo = yield getFoo();
    console.log(foo);
  } catch (e) {
    console.log(e);
  }
};

function run (generator) {
  const it = generator();

  function go(result) {
    if (result.done) return result.value;

    return result.value.then(function (value) {
      return go(it.next(value));
    }, function (error) {
      return go(it.throw(error));
    });
  }

  go(it.next());
}

run(g);
```

### Iterator 和 for...of 循环

Iterator 的作用有三个：一是为各种数据结构，提供一个统一的、简便的访问接口；二是使得数据结构的成员能够按某种次序排列；三是 ES6 创造了一种新的遍历命令`for...of`循环，Iterator 接口主要供`for...of`消费。

```javascript
var it = makeIterator(['a', 'b']);

it.next() // { value: "a", done: false }
it.next() // { value: "b", done: false }
it.next() // { value: undefined, done: true }

function makeIterator(array) {
  var nextIndex = 0;
  return {
    next: function() {
      return nextIndex < array.length ?
        {value: array[nextIndex++], done: false} :
        {value: undefined, done: true};
    }
  };
}
```

原生具备 Iterator 接口的数据结构如下。

- Array
- Map
- Set
- String
- TypedArray
- 函数的 arguments 对象
- NodeList 对象

#### 与其他遍历语法的比较

以数组为例，JavaScript 提供多种遍历语法。最原始的写法就是`for`循环。

```javascript
for (var index = 0; index < myArray.length; index++) {
  console.log(myArray[index]);
}
```

这种写法比较麻烦，因此数组提供内置的`forEach`方法。

```javascript
myArray.forEach(function (value) {
  console.log(value);
});
```

这种写法的问题在于，无法中途跳出`forEach`循环，`break`命令或`return`命令都不能奏效。

`for...in`循环可以遍历数组的键名。

```javascript
for (var index in myArray) {
  console.log(myArray[index]);
}
```

`for...in`循环有几个缺点。

- 数组的键名是数字，但是`for...in`循环是以字符串作为键名“0”、“1”、“2”等等。
- `for...in`循环不仅遍历数字键名，还会遍历手动添加的其他键，甚至包括原型链上的键。
- 某些情况下，`for...in`循环会以任意顺序遍历键名。

总之，`for...in`循环主要是为遍历对象而设计的，不适用于遍历数组。

`for...of`循环相比上面几种做法，有一些显著的优点。

```javascript
for (let value of myArray) {
  console.log(value);
}
```

- 有着同`for...in`一样的简洁语法，但是没有`for...in`那些缺点。
- 不同于`forEach`方法，它可以与`break`、`continue`和`return`配合使用。
- 提供了遍历所有数据结构的统一操作接口。

### 异步

所谓"异步"，简单说就是一个任务不是连续完成的，可以理解成该任务被人为分成两段，先执行第一段，然后转而执行其他任务，等做好了准备，再回过头执行第二段。

比如，有一个任务是读取文件进行处理，任务的第一段是向操作系统发出请求，要求读取文件。然后，程序执行其他任务，等到操作系统返回文件，再接着执行任务的第二段（处理文件）。这种不连续的执行，就叫做异步。

相应地，连续的执行就叫做同步。由于是连续执行，不能插入其他任务，所以操作系统从硬盘读取文件的这段时间，程序只能干等着。

### 协程

传统的编程语言，早有异步编程的解决方案（其实是多任务的解决方案）。其中有一种叫做"协程"（coroutine），意思是多个线程互相协作，完成异步任务。

协程有点像函数，又有点像线程。它的运行流程大致如下。

- 第一步，协程`A`开始执行。
- 第二步，协程`A`执行到一半，进入暂停，执行权转移到协程`B`。
- 第三步，（一段时间后）协程`B`交还执行权。
- 第四步，协程`A`恢复执行。

### 异步任务的封装

```javascript
var fetch = require('node-fetch');

function* gen(){
  var url = 'https://api.github.com/users/github';
  var result = yield fetch(url);
  console.log(result.bio);
}

var g = gen();
var result = g.next();

result.value.then(function(data){
  return data.json();
}).then(function(data){
  g.next(data);
});
```

### Thunk 函数

编译器的“传名调用”实现，往往是将参数放到一个临时函数之中，再将这个临时函数传入函数体。这个临时函数就叫做 Thunk 函数。

任何函数，只要参数有回调函数，就能写成 Thunk 函数的形式。下面是一个简单的 Thunk 函数转换器。

```javascript
// ES5版本
var Thunk = function(fn){
  return function (){
    var args = Array.prototype.slice.call(arguments);
    return function (callback){
      args.push(callback);
      return fn.apply(this, args);
    }
  };
};

// ES6版本
const Thunk = function(fn) {
  return function (...args) {
    return function (callback) {
      return fn.call(this, ...args, callback);
    }
  };
};

var readFileThunk = Thunk(fs.readFile);
readFileThunk(fileA)(callback);
```

### Thunkify 模块

```bash
$ npm install thunkify
```

```javascript
var thunkify = require('thunkify');
var fs = require('fs');

var read = thunkify(fs.readFile);
read('package.json')(function(err, str){
  // ...
});
```

### Generator 函数自动执行

```javascript
function* gen() {
  // ...
}

var g = gen();
var res = g.next();

while(!res.done){
  console.log(res.value);
  res = g.next();
}
```

### Thunk 函数的自动流程管理

```javascript
function run(fn) {
  var gen = fn();

  function next(err, data) {
    var result = gen.next(data);
    if (result.done) return;
    result.value(next);
  }

  next();
}

function* g() {
  // ...
}

run(g);
```

### co 模块

```javascript
var co = require('co');
co(gen);
```

Generator 就是一个异步操作的容器。它的自动执行需要一种机制，当异步操作有了结果，能够自动交回执行权。

两种方法可以做到这一点。

（1）回调函数。将异步操作包装成 Thunk 函数，在回调函数里面交回执行权。

（2）Promise 对象。将异步操作包装成 Promise 对象，用`then`方法交回执行权。



第一行，检查当前是否为 Generator 函数的最后一步，如果是就返回。

第二行，确保每一步的返回值，是 Promise 对象。

第三行，使用`then`方法，为返回值加上回调函数，然后通过`onFulfilled`函数再次调用`next`函数。

第四行，在参数不符合要求的情况下（参数非 Thunk 函数和 Promise 对象），将 Promise 对象的状态改为`rejected`，从而终止执行。

### 基于 Promise 对象的自动执行

```javascript
var fs = require('fs');

var readFile = function (fileName){
  return new Promise(function (resolve, reject){
    fs.readFile(fileName, function(error, data){
      if (error) return reject(error);
      resolve(data);
    });
  });
};

var gen = function* (){
  var f1 = yield readFile('/etc/fstab');
  var f2 = yield readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};

var g = gen();

g.next().value.then(function(data){
  g.next(data).value.then(function(data){
    g.next(data);
  });
});

function run(gen){
  var g = gen();

  function next(data){
    var result = g.next(data);
    if (result.done) return result.value;
    result.value.then(function(data){
      next(data);
    });
  }

  next();
}

run(gen);
```

### 处理 Stream

```javascript
const co = require('co');
const fs = require('fs');

const stream = fs.createReadStream('./les_miserables.txt');
let valjeanCount = 0;

co(function*() {
  while(true) {
    const res = yield Promise.race([
      new Promise(resolve => stream.once('data', resolve)),
      new Promise(resolve => stream.once('end', resolve)),
      new Promise((resolve, reject) => stream.once('error', reject))
    ]);
    if (!res) {
      break;
    }
    stream.removeAllListeners('data');
    stream.removeAllListeners('end');
    stream.removeAllListeners('error');
    valjeanCount += (res.toString().match(/valjean/ig) || []).length;
  }
  console.log('count:', valjeanCount); // count: 1120
});
```

## async 函数

async 函数是什么？一句话，它就是 Generator 函数的语法糖。

```javascript
function timeout(ms) {
  return new Promise((resolve) => {
    setTimeout(resolve, ms);
  });
}

async function asyncPrint(value, ms) {
  await timeout(ms);
  console.log(value);
}

asyncPrint('hello world', 50);
```

### 使用注意点

第一点，前面已经说过，`await`命令后面的`Promise`对象，运行结果可能是`rejected`，所以最好把`await`命令放在`try...catch`代码块中。

```javascript
async function myFunction() {
  try {
    await somethingThatReturnsAPromise();
  } catch (err) {
    console.log(err);
  }
}

// 另一种写法

async function myFunction() {
  await somethingThatReturnsAPromise()
  .catch(function (err) {
    console.log(err);
  });
}
```

第二点，多个`await`命令后面的异步操作，如果不存在继发关系，最好让它们同时触发。

```javascript
let foo = await getFoo();
let bar = await getBar();

// 写法一
let [foo, bar] = await Promise.all([getFoo(), getBar()]);

// 写法二
let fooPromise = getFoo();
let barPromise = getBar();
let foo = await fooPromise;
let bar = await barPromise;
```

第三点，`await`命令只能用在`async`函数之中，如果用在普通函数，就会报错。

第四点，async 函数可以保留运行堆栈。

### async 函数的实现原理 

async 函数的实现原理，就是将 Generator 函数和自动执行器，包装在一个函数里。

```javascript
async function fn(args) {
  // ...
}

// 等同于

function fn(args) {
  return spawn(function* () {
    // ...
  });
}
```

#### 按顺序完成异步操作

Promise 的写法如下。

```javascript
function logInOrder(urls) {
  // 远程读取所有URL
  const textPromises = urls.map(url => {
    return fetch(url).then(response => response.text());
  });

  // 按次序输出
  textPromises.reduce((chain, textPromise) => {
    return chain.then(() => textPromise)
      .then(text => console.log(text));
  }, Promise.resolve());
}
```

下面是 async 函数实现。

```javascript
async function logInOrder(urls) {
  for (const url of urls) {
    const response = await fetch(url);
    console.log(await response.text());
  }
}
async function logInOrder(urls) {
  // 并发读取远程URL
  const textPromises = urls.map(async url => {
    const response = await fetch(url);
    return response.text();
  });

  // 按次序输出
  for (const textPromise of textPromises) {
    console.log(await textPromise);
  }
}
```

## Class 的基本语法

```javascript
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}
```

类的内部所有定义的方法，都是不可枚举的（non-enumerable）。

`toString`方法是`Point`类内部定义的方法，它是不可枚举的。这一点与 ES5 的行为不一致。

`constructor`方法是类的默认方法，通过`new`命令生成对象实例时，自动调用该方法。一个类必须有`constructor`方法，如果没有显式定义，一个空的`constructor`方法会被默认添加。

类必须使用`new`调用，否则会报错。这是它跟普通构造函数的一个主要区别，后者不用`new`也可以执行。

### 取值函数（getter）和存值函数（setter）

```javascript
let person = new class {
  constructor(name) {
    this.name = name;
  }

  sayName() {
    console.log(this.name);
  }
}('张三');

person.sayName(); // "张三"
//`person`是一个立即执行的类的实例。
```

### 注意点

**（1）严格模式**

类和模块的内部，默认就是严格模式，所以不需要使用`use strict`指定运行模式。只要你的代码写在类或模块之中，就只有严格模式可用。考虑到未来所有的代码，其实都是运行在模块之中，所以 ES6 实际上把整个语言升级到了严格模式。

**（2）不存在提升**

类不存在变量提升（hoist），这一点与 ES5 完全不同。

**（3）name 属性**

由于本质上，ES6 的类只是 ES5 的构造函数的一层包装，所以函数的许多特性都被`Class`继承，包括`name`属性。

**（4）Generator 方法**

如果某个方法之前加上星号（`*`），就表示该方法是一个 Generator 函数。

**（5）this 的指向**

类的方法内部如果含有`this`，它默认指向类的实例。

### 私有属性的提案

方法是在属性名之前，使用`#`表示

```javascript
class IncreasingCounter {
  #count = 0;
  get value() {
    console.log('Getting the current value!');
    return this.#count;
  }
  increment() {
    this.#count++;
  }
}

const counter = new IncreasingCounter();
counter.#count // 报错
counter.#count = 42 // 报错
```

## Class 的继承

```javascript
class ColorPoint extends Point {
  constructor(x, y, color) {
    super(x, y); // 调用父类的constructor(x, y)
    this.color = color;
  }

  toString() {
    return this.color + ' ' + super.toString(); // 调用父类的toString()
  }
}
```

子类必须在`constructor`方法中调用`super`方法，否则新建实例时会报错。

### Object.getPrototypeOf()

```javascript
Object.getPrototypeOf(ColorPoint) === Point
// true
```

## Module 的语法

```javascript
// CommonJS模块
let { stat, exists, readFile } = require('fs');

// 等同于
let _fs = require('fs');
let stat = _fs.stat;
let exists = _fs.exists;
let readfile = _fs.readfile;
```

种加载称为“运行时加载”，因为只有运行时才能得到这个对象，导致完全没办法在编译时做“静态优化”。

```javascript
// ES6模块
import { stat, exists, readFile } from 'fs';
```

严格模式主要有以下限制。

- 变量必须声明后再使用
- 函数的参数不能有同名属性，否则报错
- 不能使用`with`语句
- 不能对只读属性赋值，否则报错
- 不能使用前缀 0 表示八进制数，否则报错
- 不能删除不可删除的属性，否则报错
- 不能删除变量`delete prop`，会报错，只能删除属性`delete global[prop]`
- `eval`不会在它的外层作用域引入变量
- `eval`和`arguments`不能被重新赋值
- `arguments`不会自动反映函数参数的变化
- 不能使用`arguments.callee`
- 不能使用`arguments.caller`
- 禁止`this`指向全局对象
- 不能使用`fn.caller`和`fn.arguments`获取函数调用的堆栈
- 增加了保留字（比如`protected`、`static`和`interface`）

### export 命令

```javascript
// profile.js
export var firstName = 'Michael';
export var lastName = 'Jackson';
export var year = 1958;
```

```javascript
// profile.js
var firstName = 'Michael';
var lastName = 'Jackson';
var year = 1958;

export { firstName, lastName, year };
```

```javascript
// 报错
function f() {}
export f;

// 正确
export function f() {};

// 正确
function f() {}
export {f};
```

```javascript
// main.js
import { firstName, lastName, year } from './profile.js';

function setName(element) {
  element.textContent = firstName + ' ' + lastName;
}
```

### export default 命令

```javascript
// export-default.js
export default function () {
  console.log('foo');
}
```

```javascript
export { foo, bar } from 'my_module';

// 可以简单理解为
import { foo, bar } from 'my_module';
export { foo, bar };
```

### 适用场合

（1）按需加载。

```javascript
button.addEventListener('click', event => {
  import('./dialogBox.js')
  .then(dialogBox => {
    dialogBox.open();
  })
  .catch(error => {
    /* Error handling */
  })
});
```

（2）条件加载

```javascript
if (condition) {
  import('moduleA').then(...);
} else {
  import('moduleB').then(...);
}
```

（3）动态的模块路径

```javascript
import(f())
.then(...);
```

### 注意点

`import()`加载模块成功以后，这个模块会作为一个对象，当作`then`方法的参数。因此，可以使用对象解构赋值的语法，获取输出接口。

```javascript
import('./myModule.js')
.then(({export1, export2}) => {
  // ...·
});
```

```javascript
async function main() {
  const myModule = await import('./myModule.js');
  const {export1, export2} = await import('./myModule.js');
  const [module1, module2, module3] =
    await Promise.all([
      import('./module1.js'),
      import('./module2.js'),
      import('./module3.js'),
    ]);
}
main();
  
```