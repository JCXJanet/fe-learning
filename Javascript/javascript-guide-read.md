---
title: Javascript权威指南阅读笔记
date: 2021-02-16 01:08:18
tags: JavaScript
---

## 词法结构

JavaScript区分大小写

html不区分大小写

JavaScript标志符必须以字母、下划线（_）或美元符（$）开始。

## 类型、值和变量

JavaScript的数据类型分为两类，原始类型和对象类型。

JavaScript中的数据类型为，数字、字符串、布尔值、null、undefined、symbol

对象：每个属性都由“名/值对”构成，无序集合。

数组：表示带编号的值的有序集合。

如果函数用于初始化（使用new运算符）一个新建的对象，称之为构造函数。

每个构造函数定义了一类对象-----由构造函数初始化的对象组成的集合。类可以看作是对象类型的子类型。

JavaScript定义的三种有用类，Date、RegExp、Error

JavaScript类型可以分为可变类型、不可变类型。数组、对象为可变类型，其余为不可变类型。

### 二进制浮点数和四舍五入错误

```javascript
var x =.3 -.2;
var y =.2 -.1;
x == y; // =>false:两值不相等
x == .1 // =>false: .3-.2 不等于 .1
y == .1 // =>true: .2-.1 等于 .1
```

### 日期和时间

```javascript
var then = new Date(2011, 0, 1); //2011年1月1日
var later = new Date(2011, 0, 1, 17, 10, 30); //同一天，当地时间5:10:30pm
var now = new Date(); //当前日期和时间
var elapsed = now -then; //日期减法，计算时间间隔的毫秒数
later.getFullYear(); // =>2011
later.getMonth(); // => 0: 从0开始计数的月份
later.getDate(); // => 1: 从1开始计数的天数
later.getDay(); // => 5: 得到星期几， 0代表星期日， 5代表星期一
later.getHours(); // => 当地时间17:5pm
later.getUTCHours(); // 使用UTC表示小时的时间，基于时区
```

### 字符串

```javascript
var s = "hello, world"
s.length
s.charAt(0); // h
s.charAt(s.length-1); // d
s.substring(1,4); // ell
s.slice(1,4); // ell
s.slice(-3) // rld
s.indexOf("1") // 2
s.lastIndexOf("1") // 10
s.indexOf("l",3) // 3 ,在位置3及之后首次出现字符l的位置
s.split(",")
s.replace("h","H")
s.toUpperCase();
```

### 模式匹配

```javascript
var text = "testing: 1, 2, 3";
var pattern = /\d+/g; //匹配所有包含一个或多个数字的实例
pattern.test(text); //true,匹配成功
text.search(pattern); // 9，首次匹配成功的位置
text.match(pattern); //["1", "2", "3"]: 所有匹配组成的数组
text.replace(pattern, "#"); // => "testing: #, #, #"
text.split(/\D+/); // =>["","1","2","3"]:用非数字字符截取字符串
```

### 全局对象

全局属性：undefined、Infinity、NaN

全局函数； isNaN()、parseInt()、eval()

构造函数：Date()、RegExp()、String()、Object()、Array()

全局对象：Math、JSON

### 显示类型转换

```javascript
Number("3") //3
String(false) //"false"
Boolean([]) // true
Object(3) // new Number(3)
var n = 123456.789
n.toFixed(0); //"1234567"
n.toFixed(2); //"123456.79"
n.toExponential(3); //"1.235e+5"
n.toPreCision(7); //"123456.8"
```

对象到布尔值，所有对象都会转换为true

```javascript
var now = new Date(); //创建一个日期对象
typeof (now +1); //string
typeof (now -1); //number
now = now.toString(); //true
now > (now -1) //true
```

==存在类型转换

### in运算符

```javascript
var point = {x:1,y:1}
"x" in point //true
"z" in point //false

var data = [7,8,9]
"0" in data //true 数组中包含元素"0"
1 in data //true
3 in data //false
```

### instanceof

instanceof用来判断一个对象是否是一个类的实例

### eval()

eval()只有一个参，如果传入的不是字符串，那么直接返回这个参数，如果参数是字符串，它会把字符串当作JavaScript代码进行编译。

eval()最重要的是，它使用了调用它的变量作用域环境。

## 语句

### for in

javaScript会依次枚举对象的属性来执行循环。

### 严格模式主要有以下限制：

1.变量必须声明后再使用
2.函数的参数不能有同名属性，否则报错
3.不能使用with语句
4.不能对只读属性赋值，否则报错
5.不能使用前缀0表示八进制数，否则报错
6.不能删除不可删除的属性，否则报错
7.不能删除变量delete prop，会报错，只能删除属性delete global[prop]
8.eval不会在它的外层作用域引入变量
9.eval和arguments不能被重新赋值
10.arguments不会自动反映函数参数的变化
11.不能使用arguments.callee
12.不能使用arguments.caller
13.禁止this指向全局对象
14.不能使用fn.caller和fn.arguments获取函数调用的堆栈
15.增加了保留字（比如protected、static和interface）

### 设立"严格模式"的目的，主要有以下几个：

1.消除Javascript语法的一些不合理、不严谨之处，减少一些怪异行为;
2.消除代码运行的一些不安全之处，保证代码运行的安全；
3.提高编译器效率，增加运行速度；
4.为未来新版本的Javascript做好铺垫。
注：经过测试IE6,7,8,9均不支持严格模式。

## 对象

value、writable、enumerable、configurable、get、set

```javascript
var o = new Object();
var a = new Array();
var d = new Date();
var r = new RegExp("js");
```

### Object.create()

```javascript
var o1 = Object.create({x:1, y:2}); //o1继承了属性x和y
var o2 = Object.create(null); //o2不继承任何属性和方法
var o3 = Object.create(Object.prototype); //o3和{}和new Object()一样
```

### 检测属性

```javascript
var o = { x:1 }
"x" in o; //true
"y" in o; //false
"toString" in o; //true
```

```javascript
var o =  {x:1}
o.hasOwnProperty("x"); //true
o.hasOwnProperty("y"); //false
o.hasOwnProperty("toString"); //false
```

```javascript
var o = inherit({y:2});
o.x = 1;
o.propertyIsEnumerable("x");  //true
o.propertyIsEnumerable("y");  //false
Object.prototype.propertyIsEnumerable("toString"); //true
```

```javascript
var o = {x:1}
o.x !== undefined;  //true
o.y !== undefined;  //false
o.toString !== undefined;  //true
```

```javascript
var o = {x:undefined}
o.x !== undefined;  //false
o.y !== undefined;  //false
"x" in o;  //true
"y" in o;  //false
delete o.x;
"x" in o; //false
```

```javascript
Object.getOwnPropertyDescriptor() //得到自有属性的描述符
Object.getPrototypeOf() //获得继承属性的特性，需遍历原型链。
Object.defineProperty() //新建属性
Object.defineProperties() //新建属性
```

### 检测原型

```javascript
var p =  {x:1}  //定义一个原型对象
var o =  Object.create(p); //使用这个原型创建一个对象
p.isPrototypeOf(o); //true，o继承自p
Object.prototype.isPrototypeOf(o); //p继承自Object.prototype
```

### 类型判断函数

```javascript
function classof(o) {
	if (o === null) return "Null";
	if (o === undefined) return "Undefined";
	return Object.prototype.toString.call(o).slice(8,-1);
}
```

```javascript
Object.esExtensible();  //判断对象是否是可扩展的
Object.preventExtensions(); //将对象转换为不可扩展的
Object.seal() //封闭对象
Object.isSealed() //检测对象是否已经封闭
Object.freeze() //冻结对象
Object.isFrozen() //判断对象是否冻结
```

### 序列化对象

```javascript
o = {x:1, y:{z:[false,null, ""]}};
s = JSON.stringify(o);
p = JSON.parse(s);
```

### toLocaleString()

Object中的toLocaleString()方法并不做任何本地化操作，等同于toString()。Date和Number类toLocaleString()方法做了定制，可以用它对数字、日期和时间做本地化的转换。Array类，每个数组元素都会调用toLocaleString()方法转换为字符串。

## 数组

pop()、push()、unshift()、shift()、splice()、join()、reverse()、sort()、concat()、slice()、toString()、toLocaleString()

indexOf()、lastIndexOf()、

**sort()排序以字母表顺序排序**

##### forEach()方法

从头至尾遍历数组，为每个元素调用指定的函数，

```javascript
data.forEach(function(v, i, a){ a[i] = v + 1;})
//v为value、i为元素索引、a为数组本身。
```

##### map()方法

将调用的数组的每个元素传递给指定的函数

```javascript
var a = [1,2,3];
var b = a.map(function(x){return x*x}); //b是[1,4,9]
```

##### filter()方法

返回的数组是调用的数组的一个子集。传递的函数 使用来逻辑判定的:该函数返回true或false。

```javascript
var a = [5,4,3,2,1];
var smallvalues = a.filter(function(x) {return x < 3});  // [2,1]
var everyother = a.filter(function(x,i) {return i % 2 ==0 }); //[5,3,1]
```

##### every()方法和some()方法

```javascript
var a = [1,2,3,4,5];
a.every(function(x) {return x < 10;}) //true
a.some(function(x) {return x % 2 === 0；}) //false
```

##### reduce()和reduceRight()注入和折叠

```javascript
var a = [1,2,3,4,5];
var sum = a.reduce(function(x,y){ return x+y}, 0); //求和
var product = a.reduce(function(x, y) { return x*y}, 1); //求积
var max = a.reduce(function(x,y) {return (x,y)?x:y}) //求最大值
```

```javascript
var a = [2,3,4];
// 计算2^(3^4)乘方操作的有限顺序是从右到左
var big = reduceRight(function (a , v){
	return Math.pow(v,a); 
})
```

```javascript
var objects = [{x:1},{y:2},{z:3}];
var merged = objects.reduce(union); // {x:1,y:2,z:3}
```

```javascript
var objects = [{x:1,a:1},{y:2,a:2},{z:3,a:3}];
var leftunion = objects.reduce(union); // {x:1,y:2,z:3,a:1}
var rightunion = objects.reduceRight(union); // {x:1,y:2,z:3,a:3}
```

##### isArray()

函数检测

```javascript
Array.isArray([]) //true
Array.isArray({}) //false
```

```javascript
[] instanceof Array //true
var isArray = Function.isArray || function(o) {
  	return typeof o === "object" &&
    Object.prototype.toString.call(o) === "[object Array]";
}
```

### 作为数组的字符串

```javascript
var s = test;
s.charAt(0); // "t"
s[1]; // "e"
```

## 函数

```javascript
var o = {                      			//对象o
	m: function(){               			//对象中的方法m()
    	var self = this;         			//将this对象保存到一个变量中
    	console.log(this === o);			//输出true，this就是这个对象o
    	f();													//调用辅助函数f()
    
    	function f(){									//定义一个嵌套函数f()
        	console.log(this === o);	//"false": this的值是全局对象或undefined
        	console.log(self === o);	//"true": self指外部函数的this值
      }
  }
}
```

#### arguments对象

```javascript
//检查传入实参的个数是否正确
function f(x,y,z){
	if(arguments.length !=3){
		throw new Error("function f called with" + arguments.length + "arguments,but it expects 3 arguments.");
	}
}
```

#### callee和caller属性

```javascript
//递归调用,其中callee实际上是函数本身
var factorial = function(x) {
	if(x<=1) return 1;
	return x*arguments.callee(x-1);
}
```

### 自定义函数

```javascript
//初始化函数对象的计数器属性
//由于函数声明背提前了，因此这里是可以在函数声明
//之前给它的成员赋值的
uniqueInteger.counter = 0;

//每次调用这个函数都会返回一个不同的整数
//它使用一个属性来记住下一次将要返回的值
function uniqueInteger() {
	return uniqueInteger.counter++;
}

//计算阶乘，并将结果缓存至函数的属性中
function factorial(n){
  if(isFinite(n) && n>0 && n==Math.round(n)){
    if(!(n in factorial))
      factorial[n] = n *factorial(n-1);
    return factorial[n];
  }
  else return NaN; //如果输入有误
}
factorial[1] = 1; //初始化缓存以保存这种基本情况
```

### 闭包

闭包可以捕捉到局部变量（和参数），并一直保存下来，看起来像这些变量绑定到了在其中定义他们的外部函数。

```javascript
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f() {return scope;}
    return f;
}
checkscope()()
```

```javascript
function counter(){
  var n = 0;
  return {
    count: function() {return n++;},
    reset: function() {n=0;}
  };
}

var c = counter(), d = counter();
c.count(); //0
d.count(); //0
c.reset(); //重置
c.count(); //0
d.count(); //1
```

利用闭包实现的私有属性存取器方法

```javascript
function addPrivateProperty(o,name,predicate){
  var value;
  o["get" + name] = function(){return value;};
  
  o["set" + name] = function(v){
    if(predicate && !predicate(v)){
      throw Error("set" + name + ": invalid value" +v);
    }else{
      value = v;
    }
  };
}

var o = {};

addPrivateProperty(o, "Name", function(){return typeof x == "string";});

o.setName("Frank");
console.log(o.getName());
o.setNmae(o);
```

```javascript
function constfunc(v){return function() {return v;}}
var funcs =[];
for(var i=0;i<10;i++){
    funcs[i] = constfunc(i);
}
//不共享变量

var self = this;
var outerArguments = arguments;
```

```javascript
//检查参数个数是否符合标准
function check(args){
  var actual = args.length; //实际参数个数
  var expected = args.callee.length; //期望的实参个数
  if(actual !== expected)
    throw Error("Expected" + expected + "args;got" + actual);
}
```

### 手写bind()

```javascript
if(!Function.prototype.bind){
	Function.prototype.bind = function(o /*, args */){
    var self = this,boundArgs =arguments;
    return function(){
      var args = [],i;
      for(i = 1; i<boundArgs.length; i++) args.push(boundArgs[i]);
      for(i = 0; i<arguments.length; i++) args.push(arguments[i]);
      return self.apply(o,args);
    };
  };
}
```

### 记忆

```javascript
function memorize(f){
	var cache = {};
	return function(){
		var key = arguments.length + Array.prototype.join.call(arguments,",");
		if(key in cache) return cache[key];
		else return cache[key] = f.apply(this,arguments);
	}
}

//使用
function gcd(a,b){
  var t;
  if(a < b) t=b, b=a, a=t;
  while(b != 0) t=b, b=a%b, a=t;
  return a;
}
var gcdmemo = memorize(gcd);
gcdmemo(85,187);
var factorial = memorize(function(n){
  return (n<=1) ? 1:n*factorial(n-1);
});
factorial(5); //120
```

### 鸭式辩型

```javascript
function quacks(o){
  for(var i=1; i<arguments.length; i++){
    var arg = arguments[i];
    switch(typeof arg){
      case 'string':
        if(typeof o[arg] !== "function") return false;
      case 'function':
        arg = arg.prototype;
      case 'object':
        for(var m in arg){
           if(typeof arg[m] !== "function") continue;
           if(typeof o[m] !== "function") return false;
        }
    }
  }
  return true;
}
```

### 组合优于继承

```javascript
//使用组合代替继承的集合的实现
var FilteredSet = Set.extend(
	function FilteredSet(set, filter){
		this.set = set;
		this.filter = filter;
	},
	{
	add: function(){
				if(this.filter){
					for(var i=0; i<arguments.length;i++){
						var v = arguments[i];
						if(!this.filter(v))
							throw new Error("FilteredSet: value" + v + "rejected by filter");
					}
        }
				this.set.add.apply(this.set, arguments);
				return this;
  },
  remove: function() {
				this.set.remove.apply(this.set,arguments);
  			return this;
	},
  contains: function(v) { return this.set.contains(v);},
  size: function() {return this.set.size();},
  foreach: function(f,c) {this.set.foreach(f,c);}
  });
```

### 生成器

```javascript
//一个生成器，每次产生一行字符串
//这里没有使用s.split(),因为这样会每次都处理整个字串，并分配一个数组
function eachline() {
	let p;
	while((p = s.indexOf('\n')) != -1){
		yield s.substring(o,p);
		s = s.substring(p+1);
	}
	if (s.length > 0 ) yield s;
}
//一个生成器函数，对于每个可迭代的i的每个元素x，都会产生一个f(x)
function map(i,f){
   for(let x in i) yield f(x);
}
//一个生成器函数，针对每个结果为true的f(x)，为i生成一个元素。
function select(i,f){
   for(let x in i){
     if(f(x)) yield x;
   }
}
let text = " #comment \n \n hello \nworld\n quit \n unreached \n"
let lines = eachline(text);
console.log(lines);
let trimmed = map(lines, function(line) { return line.trim(); });
let nonblank = select(trimmed, function(line){
 		return line.length > 0 && line[0] != "#"; 
});
for(let line in nonblank){
  	if(line === "quit") break;
  	console.log(line);
}
```

### defer与async的区别

 defer属性，相当于告诉浏览器立即下载，但延迟执行。

HTML5规范要求脚本按照它们出现的先后顺序执行，因此第一个延迟脚本会先于第二个延迟脚本执行，而这两个脚本会先于`DOMContentLoaded`事件执行。**在现实当中**，延迟脚本并不一定会按照顺序执行，也不一定会在`DOMContentLoad`时间触发前执行，因此最好只包含一个延迟脚本。

`async`只适用于**外部脚本文件**，并告诉浏览器立即下载文件。但与`defer`不同的是，标记为`async`的脚本并不保证按照它们的先后顺序执行。谁先下载好谁先执行。

第二个脚本文件可能会在第一个脚本文件之前执行。因此确保两者之间互不依赖非常重要。指定`async`属性的目的是不让页面等待两个脚本下载和执行，从而异步加载页面其他内容。

### 同源策略

同源策略 (Same-Origin Policy) 最早由 Netscape 公司提出, 所谓同源就是要求, 域名, 协议, 端口相同. 非同源的脚本不能访问或者操作其他域的页面对象(如DOM等). 作为著名的安全策略, 虽然它只是一个规范, 并不强制要求,  但现在所有支持 javaScript 的浏览器都会使用这个策略. 以至于该策略成为浏览器最核心最基本的安全功能, 如果缺少了同源策略, web的安全将无从谈起.

### 同源策略的限制

同源策略下的web世界, 域的壁垒高筑, 从而保证各个网页相互独立, 互相之间不能直接访问, iframe, ajax 均受其限制, 而script标签不受此限制.

#### iframe限制

- 可以访问同域资源, 可读写;
- 访问跨域页面时, 只读.

#### Ajax限制

Ajax 的限制比 iframe 限制更严.

- 同域资源可读写;
- 跨域请求会直接被浏览器拦截.(chrome下跨域请求不会发起, 其他浏览器一般是可发送跨域请求, 但响应被浏览器拦截)

#### Script限制

script并无跨域限制, 这是因为script标签引入的文件不能够被客户端的 js 获取到, 不会影响到原页面的安全, 因此script标签引入的文件没必要遵循浏览器的同源策略. 相反, ajax 加载的文件内容可被客户端 js 获取到, 引入的文件内容可能会泄漏或者影响原页面安全, 故, ajax必须遵循同源策略.

#### 注意

同源策略要求三同, 即: **同域**, **同协议**, **同端口**.

- 同域即host相同, 顶级域名, 一级域名, 二级域名, 三级域名等必须相同, 且域名不能与 ip 对应;
- 同协议要求, http与https协议必须保持一致;
- 同端口要求, 端口号必须相同.

IE有些例外, 它仅仅只是验证主机名以及访问协议，而忽略了端口号.

这里需要澄清一个概念, 所谓的域, 跟 js 等资源的存放服务器没有关系, 比如你到 baidu.com 使用 script 标签请求了 google.com 下的js, 那么该 js 所在域是 baidu.com, 而不是 google.com. 换言之, 它能操作baidu.com的页面对象, 却不能操作google.com的页面对象.

### 浏览器定位和导航

##### 提取URL的搜索字符串中的参数

```javascript
function urlArgs() {
  var args = {};
  var query = location.search.substring(1); //查找到查询串，并去掉'?'
  var pairs = query.split('&'); //根据"&"符号讲查询字符串分隔开
  for(var i = 0; i< pairs.length; i++){
    var pos = pairs[i].indexOf('='); //查找"name = value"
    		if(pos == -1) continue; //没找到的话跳过
    		var name  = pairs[i].substring(0,pos); //提取name
    		var value = pairs[i].substring(pos+1); //提取value
   			value = decodeURIComponent(value); //对value进行解码
    		args[name] = value;//存储为属性
  }
  return args; //返回解析后的属性
}
```

```
Location对象的assign()方法可以使窗口载入并显示你指定的URL中的文档。
replace()方法类似，但它在载入新文档之前会从浏览历史中把当前的文档删除。
reload()重新加载。
```

##### 浏览历史history对象

### 浏览器和屏幕信息

##### navigator对象

appName、appVersion、userAgent、platform、online

##### geoloaction对象

##### screen对象

### 对话框

alert()

confirm()

prompt() 显示一条信息，等待用户输入字符串，并返回那个字符串。

### 脚本化文档

```
document.getElementById()
document.getElementByTagName("span");
document.getElementByClassName("");
document.querySelectorAll()
```

```
parentNode
childNodes
firstChild、lastChild
nextSibling、previoursSibling
nodeType
nodeValue
nodeName
```

```
firstElementChild、lastElementChild
nextElementSibling、previousSilbling
childElementCount
```

```
var image = document.images[0]
var width = parseInt(image.getAttribute("WIDTH"))
image.setAttribute("class", "thumbnail");
```

其他方法：hasAttribute()、removeAttribute()

```
document.createElement("script")//创建<script>
appendChild();//插入
document.createTextNode("text node content");
removeChild();
replaceChild();
clondeNode();
createDocumentFragment();//临时容器
```

##### 查询窗口的视口尺寸

```javascript
//作为一个对象的w、h属性返回视口的尺寸
function getViewportSize(w){
  w = w || window;
  //除了IE 8及更早的版本以外，其他浏览器都使用
  if(w.innerWidth != null)  return {w: w.innerWidth, h:w.innerHeight};
  //对表准模式下的IE(或任何浏览器)
  var d = w.document;
  if(document.compatMode == "CSS1Compat")
    return {
      w: d.documentElement.clientWidth,
      h: d.documentElement.clientHeight
    };
  //对怪异模式下的浏览器
  return {w:d.body.clientWidth,h:d.body.clientWidth}
}
```

```
getBoundingClientRect() //在视口中判定元素的位置
scrollTop() //让浏览器滚动
```

坐标 offsetWidth、offsetHeight、offsetLeft、offsetTop、offsetParent

视口大小 clientWidth、clientHeight、clientLeft、clientTop

内容区大小 scrollWidth、scrollHeight、scrollLeft、scrollTop

### document属性

cookie、domain、lastModified、location、referred、title、URL

document.write()方法

contenteditable属性

### 事件类型

mousedown、mousemove、mouseup、keydown、keydown、keypress、keyup、

click、

change、focus、

load、online、offline、readystatechange、loadstart、progress、loadend

dragstart、dragenter、dragover、drop、

视频 waiting、playing、seeking、volumechange

addEventListener()、attachEvent()、

事件取消preventDefault()

### localStorage和sessionStorage

```
localStorage.setItem("x",1);
localStorage.getItem("x");
localStorage.removeItem("x");
localStorage.clear();
```

### 保存cookie

```javascript
function setcookie(name,value,daysToLive){
	var cookie = name + "=" + encodeURIComponent(value);
	if(typeof daysTolive === "number")
		cookie += ";max-age=" + (daysToLive*60*60*24);
	document.cookie = cookie;
}
```

### 读取cookie

```javascript
function getcookie() {
	var cookie = {};
  var all = document.cookie;
  if(all === ""){
    return cookie;
  }
  var list = all.split(";");
  for(var i=0;i<list.length;i++){
    var cookie =list[i];
    var p = cookie.indexOf("=");
    var name = coolie.substring(o,p);
    var value = cookie.substring(p+1);
    value = decodeURIComponent(value);
    cookie[name] =value;
  }
}
```

