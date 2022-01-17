JavaScript的应用环境，主要由宿主环境与运行期环境构成。其中，宿主环境是指外壳程序（shell）和Web浏览器等，而运行期环境则是由JavaScript引擎内建的。

脚本引擎必须运行在一个宿主之中，并由该宿主创建和维护脚本引擎实例的“运行期环境（runtime）”。

JavaScript是指一种通用的、跨平台的和跨环境的语言，并不特指某种特定的宿主环境或者运行环境。也就是说，它是指ECMAScript-262所描述的语言规范。

### Javascript的语法

1.语法综述

语言中的标识符一般可以分为两类，一类用于命名语法、符号等抽象概念，另一类用于命名数据（的存储位置）。前者被称为“语法关键字”，后者则被称为“变量”和“常量”。

一些模块的实现方案与逻辑（例如流程控制）相关，例如Node.js。但一些实现方案则是逻辑无关的，例如ECMAScript的静态模块机制就是如此，它只描述模块之间的依赖关系。

ES6 模块跟 CommonJS 模块的不同，主要有以下两个方面：

1. ES6 模块输出的是值的引用，输出接口动态绑定，而 CommonJS 输出的是值的拷贝
2. ES6 模块编译时执行，而 CommonJS 模块总是在运行时加载

在 CommonJS 模块中，如果你 require 了一个模块，那就相当于你执行了该文件的代码并最终获取到模块输出的 module.exports 对象的一份拷贝。

1. CommonJS 模块中 require 引入模块的位置不同会对输出结果产生影响，并且会生成值的拷贝
2. CommonJS 模块重复引入的模块并不会重复执行，再次获取模块只会获得之前获取到的模块的拷贝

**ES6 静态编译，CommonJS 运行时加载**

关于第二点，ES6 模块编译时执行会导致有以下两个特点：

1. import 命令会被 JavaScript 引擎静态分析，优先于模块内的其他内容执行。
2. export 命令会有变量声明提前的效果。

相同点：

**模块不会重复执行**

这个很好理解，无论是 ES6 模块还是 CommonJS 模块，当你重复引入某个相同的模块时，模块只会执行一次。

```javascript
// a.js
console.log('a starting');
exports.done = false;
const b = require('./b');
console.log('in a, b.done =', b.done);
exports.done = true;
console.log('a done');

// b.js
console.log('b starting');
exports.done = false;
const a = require('./a');
console.log('in b, a.done =', a.done);
exports.done = true;
console.log('b done');

// node a.js
// 执行结果：
// a starting
// b starting
// in b, a.done = false
// b done
// in a, b.done = true
// a done
```

从上面的执行过程中，我们可以看到，在 CommonJS 规范中，当遇到 require() 语句时，会执行 require 模块中的代码，并缓存执行的结果，当下次再次加载时不会重复执行，而是直接取缓存的结果。正因为此，出现循环依赖时才不会出现无限循环调用的情况。虽然这种模块加载机制可以避免出现循环依赖时报错的情况，但稍不注意就很可能使得代码并不是像我们想象的那样去执行。因此在写代码时还是需要仔细的规划，以保证循环模块的依赖能正确工作。

2.声明

JavaScript是弱类型语言。但所谓弱类型语言，只表明该语言在表达式运算中不强制校验操作数的数据类型，而并不表明该语言是否具有类型系统。

JavaScript有6种声明标识符的方法，包括变量（var）、常量（const）、块作用域变量（let）、函数（function）、类（class）和模块（import），它们都可以声明出在语法分析阶段就被识别的标识符—其中函数声明包括具名的函数名和形式参数名。

在ES6之后，字符串被映射为可迭代对象，因此也可以用for...of来列举单个字符，但这种情况下得到的字符是迭代成员（而不是属性描述符）。

typeof new RegExp() // 'object'



ECMAScript的类型系统

ECMAScript语言类型并不是“典型的”JavaScript语言类型

这些数据类型与JavaScript对数据类型的约定有如下三点不同：

1.ECMAScript语言类型用首字符大写的单词作为类型名，例如Undefined；而JavaScript语言类型使用字符串作为类型名，且首字符小写，例如"undefined"，并且叙述中通常在不混淆的情况下也可以省掉单/双引号来直接作为类型名，例如undefined。

2.ECMAScript语言类型中的Null是一个类型，并且有一个唯一值null；而在JavaScript语言类型中没有Null类型，null值是对象类型的一个特殊实例。

3.ECMAScript语言类型中没有函数类型，函数是对象类型的一个变体（Exotic Object）[插图]，即对象类型的一种实现；而JavaScript语言类型中函数是第一类类型（First class type），即能用typeof关键字检查的、与string、object等同级别的基本类型。

ECMAScript约定它的类型也可以归纳成两类：对象（Object）和基础类型（即除开Object之外的其他6种类型）。这两种类型是可以转换的，这些内部过程称为抽象操作（Abstract Operations），包括ToPrimitive()和ToObject()等。



变量声明

JavaScript中的变量声明有两种方法：显示声明、隐式声明。

解释器总是将显式声明理解为“变量声明”，而对隐式声明则不一定。

 如果变量未被声明，则先声明该变量并立即给它赋值。

如果变量已经声明过，则该语句是赋值语句。



除了以下三点不同，let的语法以及使用场景都与var一致：

■ var声明的变量，其作用域为当前函数、模块或全局；let声明的变量，其作用域总是在当前的代码块，例如语句块。

■ 在同一个代码块中，可以用var来多次声明变量名，这在语法分析中与声明一次没有区别；而用let却只能声明一次，覆盖一个已经声明的let变量（或者用let去覆盖一个已声明过的标识符）会导致语法错误。

■ 用户代码可以在声明语句之前使用所声明的var变量，这时该变量的值是undefined；而let声明的变量必须先声明后使用，声明语句之前的代码引用了let变量会触发异常，这也会导致typeof成为一个不安全的运算。



let声明与其他一些较新的语法元素遵从“块级作用域”规则，因此即使出现在全局代码块中，它们也只是声明为“全局作用域”中的标识符，而不作为对象global上的属性。



空字符串与其他字符串一样也可以用作对象成员名。



```javascript
obj = {
  '': 100
}

console.log(obj['']) // 100
```



模板字面量本质上来说是一个字面量的引用—该字面量在JavaScript内部表达为一个对象（array-like object）或数组

```javascript
foo = tpl => console.log(typeof tpl, tpl instanceof Array, ref=tpl)
foo `xyz`
object true ['xyz', raw: Array(1)]
```

这个.raw属性指向该“表达该模板字面量的内部数组”的一个原始的、未经转义的格式。

字符e或E时，该字面量总被识别为浮点数（注意，某些引擎会优化一些字面量的内部存储形式）。

常量声明与变量声明都是用来将一个标识符（变量名/常量名）与其对应的数据存储绑定起来



符号没有字面量声明形式。由于符号是值而非对象（我称之为“近似对象的”一种值类型数据），所以也不能使用new运算符来创建它。

```javascript
var aSymbol = Symbol();
const aSymbolConst = Symbol();
```



或者也可以不声明形式参数，这时也可以在函数体内使用一个名为arguments的内部对象来存取函数调用时的传入值。

在ES5以后的规范中，明确规定了在表达式中出现的具名函数名只影响该函数内的代码，而不会影响该表达式所在的作用域

在声明函数的形式参数时，可以为参数指定默认值“（default parameters）”：

```javascript
function foo(x, y=100) { console.log(x,y)}
```

如果默认值并没有放在参数列表的尾部，那么可以使用undefined，在传参时表明该参数使用默认值：

```javascript
function foo(x=100, y) { console.log(x,y)}
foo(undefined, 'abc');
```

可以在参数列表尾部使用“剩余参数（rest parameter）”，它用一个带...前缀的参数名（形参）来捕获所有未被声明的实参。在这种情况下，该形参总是一个包含所有剩余参数的数组，

```javascript
function foo(x, y, ...z){ console.log(z)}
foo('a','b', 'c', 'd')
```



3.表达式运算符

括号“()”既是语法分隔符也是运算符。

JavaScript的表达式总有结果值—一个值类型或引用类型的数据，或者undefined

单值表达式的结果是值本身，其他表达式的结果是运算的结果值（也因此必然有运算符）。

JavaScript表达式的运算结果[插图]，要么产生一个“基本类型的值”，要么产生“对一个对象实例的引用”。

||与&&  既不改变操作数的数据类型，也不强制运算结果的数据类型。除此之外，还有以下两条特性：

■ 运算符会将操作数隐式转换为布尔值，以进行布尔运算。

■ 运算过程（与普通布尔运算一样）是支持布尔短路的。



字符串连接运算总是产生一个新的字符串，它在运算效果（结果值）上完全等同于调用字符串对象的concat()方法。

尽管在ES5中引入了一个新的概念：将字符串作为类似数组的对象（to treat the string as an array-like object），从而引入了第二个可以用于字符串的运算符，即下标存取（[]）。

从ES6开始，字符串又被添加了Symbol.iterator属性，它可以作为可迭代对象处理，例如，接受数组展开以及yield*等运算（以及for...of语句）：



在位运算操作中，JavaScript强制运算目标为一个有符号的32位整型数[插图]：如果目标是非数值，那么会被强制转换为数值；如果目标是浮点数，那么会被取整；否则，将目标识别为有符号整型数。

当对非数值使用一般表达式运算符时，非数值会被先转换成一个称为NaN的数值来参与运算，而结果将仍然是NaN。



在使用“严格相等”时会先比较数据的类型，因此这种等值检测过程中不会发生类型转换。并且按照约定，JavaScript中的“-0===0”，以及“NaN！==NaN”，于是ES6之后的版本设计了Object.is()方法[插图]，处理类似“Object.is（-0，+0）===false”“Object.is（NaN，NaN）===true”这样的运算逻辑。



在三种值类型（数值、布尔值和字符串）中，如果两个被比较的值类型不同，那么：

■ 有任何一个是数字时，会将另一个转换为数字进行比较；

■ 有任何一个是布尔值时，它将被转换为数字进行比较（并且由于上一规则的存在，所以另一个数据也将被转换为数字）；

■ 有任何一个是对象（或函数）时，将调用该对象的valueOf()方法来将其转换为值数据进行比较，且在多数情况下该值数据作为数字值处理

■ 按照特定规则返回比较结果，例如，undefined与null值总是相等的。

```javascript
[Boolean(Symbol()), !Symbol(), Symbol() == true, Symbol() === true]
//[true, false, false, false]
```



所谓字符串的序列检测，在具体实现上是有限制的：

■ 当两个操作数都是字符串时，表2-13中所列的4个运算符才表示字符串序列检测；否则，

■ 当任意一个操作数是非字符串时，会将字符串转换为数值来参与运算。



赋值的效果是修改存储单元中的值



赋值运算对值类型来说是复制数据，而对于引用类型来说，则只是复制一个地址。



其一，值类型的字符串是一个大的、不确定长度的连续数据块，这导致复制数据的开销很大，所以在JavaScript中将字符串的赋值也变成了复制地址—该字符串（连续数据块起始处）的地址引用。由此引入了三条字符串处理的限制：

■ 不能直接修改字符串中的字符。

■ 字符串连接运算必然导致写复制，这将产生新的字符串。

■ 不能改变字符串的长短，例如，修改length属性是无意义的。

其二，在ES6之后的解构赋值中，赋值运算符的语义并不只有“复制地址/复制引用”，还包括“解析数组或对象的成员”这一行为。在具体到向赋值模板复制这些成员的值（或引用）时，仍然是“赋值的效果是修改存储单元中的值”。



隐式调用函数的情况包括：

■ 将函数用作属性读取器时，属性存取操作将隐式调用该函数。

■ 使用.bind方法将源函数绑定为目标函数时，调用目标函数则隐式调用源函数。

■ 当使用Proxy()创建源函数的代理对象时，调用代理对象则隐式调用源函数。

■ 可以使用new运算变相地调用函数。

■ 可以将函数赋给对象的符号属性（例如，Symbol.hasInstance、Symbol.Iterator等），并在对象的相应行为触发时调用该函数。



用class声明的类也将是一个函数（构造器），但它只能被new运算调用。

```javascript
new functionReference();
```

functionReference后面的括号并不是函数调用运算符，而只是new运算符语法的参数传入表—这是因为，在这一语法中决定（或启动）函数调用的是new运算符而非这对括号。



typeof运算符是尝试获取值的类型信息

甚至它也可以直接对标识符进行运算，而无视该标识符是否存在、是否声明过，或者是否绑定值。



“...”称为展开（spread syntax/operator）将它置于一个可迭代对象（例如数组）之前时，表明将操作数展开为一组值；将它置于一个普通对象之前时，则表明将该对象展开为一组属性。如果展开发生在函数调用界面上，则展开结果作为参数；如果展开在一个数组或对象中，则将结果作为它们的成员。



在使用[立即执行的函数表达式](https://developer.mozilla.org/zh-CN/docs/Glossary/IIFE)时，可以利用 `void` 运算符让 JavaScript 引擎把一个`function`关键字识别成函数表达式而不是函数声明（语句）。

```javascript
void function iife() {
    var bar = function () {};
    var baz = function () {};
    var foo = function () {
        bar();
        baz();
     };
    var biz = function () {};

    foo();
    biz();
}();
```



当用户点击一个以 `javascript:` 开头的URI 时，它会执行URI中的代码，然后用返回的值替换页面内容，除非返回的值是[`undefined`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/undefined)。`void`运算符可用于返回[`undefined`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/undefined)。例如：

```
<a href="javascript:void(0);">
  这个链接点击之后不会做任何事情，如果去掉 void()，
  点击之后整个页面会被替换成一个字符 0。
</a>
<p> chrome中即使<a href="javascript:0;">也没变化，firefox中会变成一个字符串0 </p>
<a href="javascript:void(document.body.style.backgroundColor='green');">
  点击这个链接会让页面背景变成绿色。
</a>
```

##### [在箭头函数中避免泄漏](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/void#在箭头函数中避免泄漏)

箭头函数标准中，允许在函数体不使用括号来直接返回值。 如果右侧调用了一个原本没有返回值的函数，其返回值改变后，则会导致非预期的副作用。 安全起见，当函数返回值是一个不会被使用到的时候，应该使用 `void` 运算符，来确保返回 [`undefined`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/undefined)（如下方示例），这样，当 API 改变时，并不会影响箭头函数的行为。

```javascript
button.onclick = () => void doSomething();
```



await会有两种流程控制效果：

■ 运算符await总是会挂起函数，直到它所等待的数据就绪；并且，

■ 如果它等待的数据被rejected，那么它会触发一个throw效果，以便将rejected的原因—任何可能的值或对象—作为异常抛出。

yield也具有两种流程控制效果：

■ 运算符yield总是会挂起函数（并将生成的值作为外部的迭代对象的.next()方法的结果值返回）；

并且，■ 运算符总是在外部迭代对象调用下一个.next（x）方法时恢复函数（并将传入的值x作为yield运算的结果值）。



JavaScript中运算符的优先级

19:  ()

18: . 对象成员存取 [] 数组下标 new() 带参数的new

17: ()  函数调用 new不带参数的new

16: ++ -- 后置递增/递减

15: + - 前置加减号 ++ -- 前置递增/递减 ~ ! Delete typeof void

14: * / %

13:+ - +(字符串连接)

12: << >>  >>>

11: < <= > >= in instanceof

10: == != === !==

9: &

8: ^

7: |

6: &&

5: ||

4: ? :

3:= oP=运算赋值

2: yield yield*

1: ...

0: ,



JavaScript中并没有“强制优先级运算符”这个概念，这对圆括号运算符正式的称谓是“成组运算/分组运算（Grouping Operator）”。只是由于它在所有运算符中优先级是最高的，因此在该运算符括号内的表达式总会最先被执行，从而得到了“强制括号内表达式的优先级”的效果。



4.语句

```javascript
var remoteMetaData = ajax.Get(your_url); // "2;"
var remoteData = eval(remoteMetaData);

switch(typeof remoteData) {
    // ...
}
```

```javascript
str2 = 'this is a ' + (str = 'test')
// 'this is a test'
```

赋值表达式（以及赋值语句）具有隐式声明变量的作用：一个变量（标识符）在赋值前未被声明，则脚本会首先隐式地声明该变量，然后完成赋值运算。在这种情况下，隐式声明的变量总是全局变量，因此它也被视为局部变量“泄露”到了全局。为了避免这种潜在的命名污染，在严格模式中向未声明的变量赋值是被禁止的。



JavaScript中的函数本身是一个变量/值，因此函数调用其实是一个表达式

非引用匿名函数的调用方法

```javascript
// “函数调用运算符()”作用于匿名函数本身
(function() {
  // ...
}())

// 作用于一个运算的结果值。
(function() {
  // ...
})();


// 用于调用函数并忽略其返回值。运算符void用于使其后的函数作为表达式执行。
void function() {
  // ...
}()
```



声明时立即执行函数的另外写法

```javascript
void function foo() {
  // ..
}(1,2);

x = 1 + (y => y + 2)(5) + 10
// 18
```



```
var str = 'test string.';
```

“var str”作为变量声明在语法解析阶段就被处理，使执行环境中有了名为str的变量；“str='test string.'”在执行阶段处理，通过赋值操作向变量名str绑定具体的值。严格来说，JavaScript中所有显式的数据声明都是按这种处理方法来实现的，包括var/let/const声明、函数声明等。



let const 是因为暂时性死区

当程序的控制流程在新的作用域（`module` `function` 或 `block` 作用域）进行实例化时，在此作用域中用let/const声明的变量会先在作用域中被创建出来，但因此时还未进行词法绑定，所以是不能被访问的，如果访问就会抛出错误。因此，在这运行流程进入作用域创建变量，到变量可以被访问之间的这一段时间，就称之为暂时死区。

*ES6规定，`let/const` 命令会使区块形成封闭的作用域。若在声明之前使用变量，就会报错。*
*总之，在代码块内，使用 `let` 命令声明变量之前，该变量都是不可用的。*
*这在语法上，称为 **“暂时性死区”**（ temporal dead zone，简称 **TDZ**）。*



break只能用于for、while等循环语句以及switch分支语句和标签化语句的内部，而return只能用于函数内部。



JavaScript中的标签就是一个标识符。标签可以与变量重名而互不影响，因为它是另一种独立的语法元素（既不是变量，也不是类型），其作用是指示“标签化语句（labeled statement）”。而它的声明也很简单：一个标识符后面跟一个冒号“：”。可以在多数语句前面加上这样的标签以使该语句被“标签化（labeled）”，



标签在语义上用于表示一个“语句/语句块”的范围



在JavaScript中，标签只能被break语句与continue语句所引用。前者表明停止语句执行，并跳转到break所指示标签的范围之外；后者表明停止当前循环，并跳转到continue所指示标签的范围起点。



continue

它默认表明停止当前循环并跳转到下一次循环迭代开始处运行。



continue后面也可以带一个标签，这时它表明从循环体内部中止，并继续到标签指示处开始执行。

continue后面的标签只能对单个循环语句有意义，因此它甚至不能作用于一个包括循环的复合语句。



return子句只能用在一个函数之内，且同一个函数之内允许存在多个return子句。当return子句没有指定返回值时，该函数返回undefined。

最后，当执行函数的逻辑过程中没有遇到return子句时，函数将会执行到最后一条语句（函数声明末尾的大括号处），并返回undefined值。



异常处理机制被分为三个部分，包括：

■ 触发异常，使用throw语句可以在任意位置触发异常，或由引擎内部在执行过程中触发异常。

■ 捕获异常，使用try...catch语句可以（在形式上表明）捕获一个代码块中可能发生的异常，并使用变量exception来指向该异常的一个引用。

■ 结束处理，使用try...finally语句可以无视指定代码块中发生的异常，确保finally语句块中的代码总是被执行。



catch(){...}块和finally{...}块都是可选的，但必须至少存在一个



finally{...}语句块的一个重要之处在于它总是在try/catch块退出之前被执行。

```javascript
// 在(函数内部的)try块中使用return时，finally块中的代码仍是在return子句前执行的
try {
  // ...
  return;
}
finally {
  ...
}
  
//在（标签化语句的）try块中使用break时，finally块中的代码仍是在break子句前执行的
//注意，continue子句与此类似
aLabel: 
  try{
    // ...
    break aLabel;
  }
  finally {
    ...
  }
```

throw语句后“应当是”一个错误对象：Error()构造器的实例，或通过“catch（exception）”子句中捕获到的异常exception。之所以说“应当是”，是因为throw语句其实可以将任何对象/值作为异常抛出。

如果throw语句位于一个finally{...}语句块中，那么在它之后的语句也不能被执行—这意味着finally{...}语句块中的代码“不一定”能被完整地执行。同样的道理，即使不是使用throw语句显式地触发异常，在finally{...}块中出现的任何执行期异常也会中止其后的代码执行。因此，对于开发者来说，应尽可能保证finally{...}语句块中的代码都能安全、无异常地执行。



5.模块

CommonJS与AMD分别是服务端和浏览器端模块化方案的主流选择，前者用require()载入，用exports对象导出，后者使用define()来声明模块的依赖关系，而装载过程却通常利用浏览器或应用框架的自身机制。



装载模块意味着模块中的顶层代码会被执行一次，由于引擎的模块装载系统会静态扫描全部模块并确定装载的次序，所以事实上模块名在"import"语句中出现和被依赖的次序也就成了那些顶层代码得以执行的次序。显然，由于后续的模块是基于缓存的，所以它们的顶层代码不会被反复执行。



所有模块的顶层代码都是顺序地、串行地执行的。

```javascript
export default 1 + 2;
```

在处理这个语句时，JavaScript在语法分析阶段只会在导出表中建立名字“default”，而这个名字与值的绑定却要等到执行阶段才会处理。这与JavaScript处理var声明的方式是类似的。



聚合

```javascript
// 聚合（并导出）资源块module-name中的名字x或v
export { x } from 'module-name';
export { v as x } from 'module-name';

// 聚合（并导出）资源块module-name中的全部名字
export * from 'module-name';
```

最后一个，那些被聚合的名字不会重复出现在当前模块的导出表中。只是在需要引用某个名字时（例如x2），当前模块会先查找自己的导出名字中是否有x2，如果没有，则会通过一个内部登记项（称为RequestedModules）来索引那些源模块，以实现深度遍历



所有的导出语句事实上都会在运行期环境初始化之前添加到导出表中，这个导出表也用于构建该模块的名字空间—二者在应对不同需求的抽象时是同一个概念。接下来，根据执行过程的需求（即其他模块的import），引擎将会初始化以及装载这个模块，在这个过程中该模块的顶层代码将会被执行。



在ECMAScript中，名字空间看起来像是一个普通对象，并且也可以通过对象成员来存取那些被导出的名字。不过由于名字空间对象的原型是null，所以除了那些导出名字之外它没有任何多余的成员名。

```javascript
// 被导入模块
export var x = 'good'

// 导入名字空间
import * as myNames from "module-name";

console.log(myNames.x); // 'good'
console.log('x' in MyNames); // true
console.log('toString' in MyNames); // false
console.log(Object.getPrototypeOf(myNames)); // null
```

具体的JavaScript引擎在装载主模块并开始执行第一行用户代码之前，通过语法分析就可以得出所有模块之间的导出、导入关系。

在所有模块依赖关系的深度遍历结束后，JavaScript就会开始向当前模块（主模块）的执行环境添加那些import项所声明的名字，并让导出名（源模块的）与本地名字（当前模块）绑定在一起。

在所有其他方式声明的（例如，使用var声明或函数声明等）名字创建之前，那些通过import导入的名字就已经被创建并绑定了值。然而自此之后，模块依赖的维护工作就结束了。也就是说，主模块根本没有自己的名字空间。



不同于导入名，名字空间中的名字其实是属性名，可以像对象属性一样操作。源于名字空间本身是一个特殊对象，所以它的属性（即名字空间中的名字）也有一些特殊性。

```javascript
// 被导入模块（module-name）
export var x = 'good';

import * as myNames from 'module-name';

// { Values: 'good', Writable: true, Enumerable: true, Configurable: false }
console.log(Object.getOwnPropertyDescriptor(myNames, 'x'));

// 属性描述符显示它是可写的，且名字空间（作为对象）未被冻结
console.log(Object.getOwnPropertyDescriptor(myNames, 'x').writable); // true
console.log(Object.isFrozen(myNames)); // false
```

但是无法修改

```javascript
// x是有值的
console.log(x); // isn't undefined, the <x> value exported
import x from 'module-name';
```

类似写法

```javascript
import * as myNames from 'module-name';

//类似于
import { x, y } from 'module-name';
const { x, y } = myNames;
```

```javascript
// 例如：被导入模块（module-name）
export var x = 'good';

// 1.导入名字空间（使用 myNames.x）
import * as myNames from "module-name";

// 2.导入名字（使用导入名x）
import { x } from "module-name";

// 3."名字空间+本地变量声明"（使用本地名字x2）
var {x: x2} = myNames;

// 测试
console.log(x, x2, myNames.x); // good good good
```

对于方法1，参见上一小节：myNames.x是一个对象属性，但与一般对象属性的操作不同。而在方法2中，x是当前模块中的一个本地名字，它被创建为所谓的“非可变间接绑定（immutable indirect binding）”，并关联到目标模块module-name中的对应导出项。这决定了该本地名字跟常量是类似的、是不可写的。例如，当读取x时，实际发生的操作是：

■ 当前模块查找到一个引用名x，并，

■ 发现它绑定到了源模块（M）的导出名x。因此，

■ 将会调用模块M的内部操作来返回M["x"]。

因此这将得到一个来自源模块的引用，进而得到该引用的值（例如上例中的'good'）。但是如果是写x，则不会发生这么深的访问操作，因为在第一步得到当前模块中的引用名x时，就会发现这个x是一个“非可变的（immutable）”绑定，不可修改。

而在使用方法3来访问x2时，由于x2是在本地声明为变量的，因此它可以写。并且，当它读的时候也只是访问本地环境中的值，而不是对源模块的引用。因为从引用中取值的操作已经在之前模板赋值时就发生过了。

使用名字空间还有一个潜在的好处：如果使用import导入一个名字，而在源模块中不存在该名字，那么将导致一个异常，而访问名字空间时是不会有这种异常的。

```javascript
// 如果使用imort，则将在模块初始化阶段抛出异常
// import { xyz } from "module-name";

// 变量xyz的初值将被置为undefined
var { xyz } = myNames;
```

由于模块总是运行在严格模式中，所以用户代码没有办法动态地在模块顶层的名字空间中创建一个新名字或标识符。也就是说，你既没有办法增删myNames的属性表，也不能在"module-name"模块中使用eval来动态地创建一个名字。



另外，由于catch子句以及具名函数都会隐式地声明变量名，因此在它们的语法中也不允许用eval和arguments作为标识符。

arguments或eval也不能使用delete去删除

用delete操作其他一些不能被删除的对象属性、标识符时将导致执行期异常。



除非在创建和启动JavaScript引擎时将它置为严格模式，或者通过模块来加载整个系统，否则默认情况下用户代码只能指定一个有限范围的严格模式。此外，JavaScript保留了一个用于动态执行的非严格模式的全局环境，用户代码可以随时进入该模式。



在任何处于严格模式的代码中，JavaScript引擎都允许用户代码通过如下两种方式将代码执行在一个非严格模式的全局环境中：

■ 使用间接调用的eval()函数，详情可参见“6.6.3.2 例外：obj.eval()的特殊性”。

■ 使用new Function方式创建的函数，详情可参见“6.6.7其他的动态执行逻辑”。



6.运算符的二义性

加号“+”带来的主要问题与另一条规则有关。这条规则是“如果表达式中存在字符串，则优先按字符串连接进行运算”。

由于加号“+”进行的是值运算，因此当对象（例如x）参与运算时将调用方法x.valueOf()来确定操作数的类型（T），并在类型T不符合要求时调用x.toString()来再次尝试。

赋值的语句即是：

■ 将右侧的结果值赋给左侧的引用来存储；但，

■ 如果左侧的被引用对象没有存储能力，则抛出异常。



因为“对象赋值模板”是作为语法分析期的分析逻辑阶段处理的逻辑（这与字面量风格的对象声明是一致的），所以JavaScript允许“对象赋值模块（和字面量风格的对象声明）”在“name：value”语法的声明项中，name可以是标识符、字符串、数字和使用“[...]”风格的计算属性名。其中“数字”是按字面量解析的

每个左侧标识符名的解析会在执行期再发生一次，在语法分析阶段解析它们的语法有效性。

### JavaScript的面向对象语言特性

当Array()的参数只有一个并且是数值类型时（typeof的值为'number'），使用new运算创建出来的会是一个用该数值指定元素个数的数组—其每个元素都是undefined值。

所有数组都是可迭代对象，但类数组对象却不一定可迭代；反过来说，可迭代对象也不一定都是数组。



```javascript
class MyObjectEx extends MyObject {
  ...
}
  
// 与如下效果类似
MyObjectEx.prototype = new MyObject();
MyObjectEx.prototype.constructor = MyObjectEx;
```

由class关键字引导的整个类声明（包括类表达式）代码块总是工作在严格模式中。这意味着extends声明也同样处在严格模式下。用extends声明的parentClass是一个表达式（的结果值），因此这事实上是说，该表达式将运行在严格模式中。

```javascript
class MyObjectEx extends MyObject {
  constructor() {
    super(x, y)
  }
}

// 与如下效果相似
function MyObjectEx() {
  // super(x, y) 将实现为类似代码
  MyObject.apply(this, [x, y]);
}
MyObjectEx.prototype = new MyObject();
MyObjectEx.prototype.constructor = MyObjectEx;
```

注意在使用this之前，总是需要先显式地调用super()以便在当前构造方法中获得this实例。

不过如果没有extends，那么不调用super也是可以的。

**调用父类方法**

```javascript
// 声明基类上的foo()方法
class MyObject {
  foo(x) {
    ...
  }
}
  
// 使用继承
class MyObjectEx extends MyObject {
  foo(x, y) {
    super.foo(x); // <- 调用父类同名方法
  }
  
  bar(x, y) {
    super.foo(x); // <- 调用父类方法
  }
}

var obj = new MyObjectEx();
obj.foo(100, 200);
```

在使用super.XXX调用父类方法时也会隐式地传入当前的this引用，这与在构造器中调用super()时是一致的。

**类成员（类静态成员）**

在使用super.XXX调用父类方法时也会隐式地传入当前的this引用，这与在构造器中调用super()时是一致的。

```javascript
// 声明类的静态方法和属性
class MyObject {
  static get aName() {
    return 10
  }
  
  static foo() {
    console.log(super.toString());
  }
}

class MyObjectEx extends MyObject {}
```

访问类静态成员时并不需要创建对象实例，因为它是类自有的成员。

只是在其中调用super()或super.XXX()时，this会绑定到类（构造器函数）本身—因为这种情况下并没有创建对象实例。

对象成员有三种性质，称为属性的可读写（writable）、可列举（enumerable）和可重置（configurable）性质。

对象成员可以是自有的（own properties），也可以是继承的（inheritedproperties）。所谓继承的，是指对象的父类或祖先类原型（即该对象的原型链上）具有该成员；子类对象可以用相同名字重新声明该成员，这称为覆盖（override）或重写（overwrite）。

JavaScript对象的某些特定成员被设置为隐藏的，因而不能被列举

在这种情况下，可以用“in”运算检测到该成员，但不能用“for...in”语句来列举它。

propertyIsEnumerable()该方法是不检测对象的原型链的，即对象继承来的成员不能被列举。

| 键名     | 成员         | 语法                           | 含义                         | 备注 |
| -------- | ------------ | ------------------------------ | ---------------------------- | ---- |
| 一般键名 | 仅显式成员   | for...in                       | 可列举的成员名(含原型链)     |      |
| 一般键名 | 仅显式成员   | Object.keys()                  | 可列举的、非符号的自有属性名 |      |
| 一般键名 | 仅显式成员   | Object.values()                | 可列举的、非符号的自有属性名 |      |
| 一般键名 | 仅显式成员   | Object.entries()               | 可列举的、非符号的自有属性名 |      |
| 一般键名 | 包含隐式成员 | Object.getOwnPropertyNames()   | 全部的、非符号的自有属性名   |      |
| 符号键名 | 包含隐式成员 | Object.getOwnPropertySymbols() | 全部的、符号键名的自有属性名 |      |

```javascript
// Object.values(obj)
Object.keys(obj).map(key => obj[key])

// Object.entries(obj)
Object.keys(obj).map(key => [key, obj[key]])
```

在Web浏览器中，DOM的约定是“如果一个属性没有初值，则应该将其置为null”。

“.”和“[]”都是对象成员存取运算符，所不同的是：前者右边的操作数必须是一个标识符，后者在方括号中的操作数可以是变量、标识符、符号、字面量或表达式。

无论是声明成员，还是取它的值，都可以在“[]”运算符中使用可计算的成员名，即使用表达式来作为成员名

```javascript
var nameLeft = 'abcd', nameRight= 'def'

console.log(obj[nameLeft + '.' + nameRight])
console.log(obj[[nameLeft, nameRight].join('.')])
```

成员的删除

```javascript
function MyObject() {
  // ...
}

// 在原型中声明属性
MyObject.prototype.value = 100;

// 创建实例
var obj1 = new MyObject();
var obj2 = new MyObject();

// 下面的代码并不会使obj1.value被删除
delete obj1.value;
console.log(obj1.value);

// 下面的代码可以删除obj1.value，但是，由于是对原型进行操作会使obj2.value也被删除
delete obj1.constructor.prototype.value;
console.log(obj1.value);
console.log(obj2.value);
```

不过该运算符不能用于删除：

■ 用var/let/const声明的变量与常量。

■ 直接继承自原型的成员。

其中，关于delete不能删除“继承自原型的成员”有一点例外：如果修改了这个成员的值，仍然可以删除它（并使它恢复到原型的值）。

JavaScript的一些官方文档中提及，delete仅在删除一个不能删除的成员时，才会返回false；在其他情况下（例如删除不存在的成员，或者删除继承自父类/原型的成员），即使删除不成功也会返回true。



使用for...in语句是不能列举符号属性的，并且Object.keys()也不能用来列举符号属性。唯一能有效列举符号属性的方法是Object.getOwnPropertySymbols()。

尽管符号属性也有可读写、可列举和可重置等性质，但是因为for...in语句并不列举符号属性，所以—事实上—也没有必要去隐藏它们，例如设置enumerable为false。并且无论如何，Object.getOwnPropertySymbols()总是可以取得一个对象全部的、自有的符号属性列表。

| 符号               | 影响的语法元素或对象行为    | 类型     |
| ------------------ | --------------------------- | -------- |
| Symbol.hasInstance | object instanced Class      | function |
| Symbol.iterator    | for...of                    | function |
| Symbol.unscopables | with(object) {...}          | object   |
| Symbol.toPrimitive | Object.prototype.valueOf()  | function |
| Symbol.toStringTag | Object.prototype.toString() | string   |

```javascript
var str = new String('hi');
console.log(str);

str[Symbol.toPrimitive] = () => 1;
console.log(100 + str); // 101

class MyString extends String {
  static [Symbol.hasInstance]() {return false}
}
console.log(new MyString instanceof MyString); // false

Object.defineProperty(String, Symbol.hasInstance, {value: () => false});
console.log(new String instanceof String); // false
```

然而使用符号作为属性名时，就必须导出标识符symbolPropName以访问对应的属性。

JavaScript确保即使用用户代码在多个地方调用了Symbol.for()，也有且仅有第一次调用时会创建（并返回）符号，而此后的调用都将直接返回该符号。

#### JavaScript的原型继承

一个对象系统的继承特性有三种实现方案，包括基于类（class-based）、基于原型（prototype-based）和基于元类（metaclass-based）。

在JavaScript中，“空（null）”是作为一个保留字存在的，代表一个“属于对象类型的空值”。因为它属于对象类型，所以也可以用for...in去列举它；又因为它是空值，所以没有任何方法和属性，因而列举不到内容。另一方面，多数对象相关的方法调用都将null作为特殊值处理，例如它不能使用Object.keys()来列举键值。

```javascript
// 显示类型object， null是一个俗语对象类型的对象
console.log(typeof null);

// null 可被列举属性
var num = 0;
for (var propertyName in null) {
  num++;
  console.log(propertyName);
}

// 显示为0
console.log(num);
```

null也可以参与运算，例如“+（加法和字符串连接）”或“-（减法）”运算。由于它并不创建自Object()构造器或其子类，因此instanceof运算会返回false。

null不是“空白对象（empty object）”。空白对象（也称为祼对象），是一个标准的、通过Object()构造的对象实例。通过instanceof运算也会返回true。在默认情况下，它只有原生对象的一些内置成员。而for...in语句并不列举它们，所以空白对象在for...in中也并不产生任何效果。

原型的含义是指：如果构造器（Object）有一个原型对象（Object.prototype），则由该构造器创建的实例（obj）都必然复制自该原型对象。换言之，所谓“原型（Prototype）”，就是构造器用于生成实例的模板。而这样的“复制”就存在多种可能性，由此引申出动态绑定和静态绑定等问题。



Javascript仅当写某个实例的成员时，才将成员的信息复制到实例映像中。

例如

```
obj2.value = 10
```

obj2仍然是一个指向原型的引用，在操作过程中也没有与原型相同大小的对象实例创建出来。但obj2（以及所有的对象实例）都需要维护一张成员列表。这张成员列表指向在obj2中发生了修改的成员名、值与类型，称为对象的自有属性表（own properties）。

这张表是否与原型一致并不重要，只需要遵循以下两条规则。

■ 规则1：保证优先读取对象的自有属性表。

■ 规则2：如果在上述自有属性表中没有指定属性，则尝试遍历对象的整个原型链，直到原型为空（null）或找到该属性。

所以JavaScript提供了以下方法：

■ Object.getOwnPropertyDescriptor()

■ Object.getOwnPropertyNames()来访问该表。而且在为某个属性置值时，本质上就是在这个自有属性表中创建一项，以覆盖原型中同名的属性。

```javascript
var obj1 = new Object;
var obj2 = new Object;

// obj1与obj2都访问Object.prototype.value
Object.prototype.value = 'abc'
console.log(obj1.value); // 'abc'
console.log(obj2.value); // 'abc'
console.log(Object.getOwnPropertyNames(obj2)); // 空数组

// obj1的成员不变，仍然访问Object.prototype.value
// obj2的自有属性表中创建了一个名为value的项
obj2.value = '10';
console.log(obj1.value); // 'abc';
console.log(obj2.value) // 10
console.log(Object.getOwnPropertyNames(obj2)); // ['value']
```

存取实例中的属性，比存取原型中的属性的效率要高。

所谓“空白对象（empty object）”，是指在它的原型链上的所有自有属性表都为空的对象。而所谓“原型链（prototype chain）”，就是对象所有的父类和祖先类的原型所形成的、可上溯访问的链表。

构造过程：**从函数到构造器**

其实函数首先只是函数，尽管它有一个prototype成员。在默认情况下，所有函数的这个成员总是一个指向标准的Object()构造器的实例—空白对象，不过该实例创建后，constructor属性总是会先被赋值为当前函数。

```javascript
// 简单版本的asConstructor()
function asConstructor(f) {
	return Object.assign(f, {
		prototype: { 'constructor': f};
	});
}
aClass = asConstructor(new Function);
```

例子

```javascript
function MyObject() {}

// 1.显示true，表明原型的构造器总是指向函数自身
console.log(MyObject.prototype.constructor === MyObject);

// 2.删除该成员
delete MyObject.prototype.constructor;

// 3.上述删除操作使该成员指向原型中的值（显示true）
console.log(MyObject.prototype.constructor === Object);
```

函数与构造器并没有明显的界限，唯一的区别只在于原型prototype属性是不是一个有意义的值。例如，一个普通的JavaScript函数，如果不将它视作构造器，那么这个prototype属性就显得很多余了。

对象原型所具有的基本成员

| 成员名               | 类型     | 分类            |
| -------------------- | -------- | --------------- |
| toString             | function | 动态语言        |
| toLocaleString       | function | 动态语言        |
| valueOf              | function | 动态语言        |
| constructor          | function | 对象系统：构造  |
| propertyIsEnumerable | function | 对象系统：属性  |
| hasOwnProperty       | function | 对象系统： 属性 |
| isPrototypeOf        | function | 对象系统：原型  |

构造器（函数）所具有的特殊成员

| 成员名    | 类型     | 分类                                 |
| --------- | -------- | ------------------------------------ |
| call      | function | 动态语言（继承自Function.prototype） |
| apply     | function | 动态语言（继承自Function.prototype） |
| bind      | function | 动态语言（继承自Function.prototype） |
| name      | string   | 函数式语言（总是重写为自有的成员）   |
| arguments | object   | 函数式语言（总是重写为自有的成员）   |
| length    | name     | 函数式语言（总是重写为自有的成员）   |
| caller    | function | 函数式语言（总是重写为自有的成员）   |
| prototype | object   | 对象系统：原型                       |

Object的类方法

| Object.xxx成员名            | 分类         |
| --------------------------- | ------------ |
| create()                    | 原型         |
| getPrototypeOf()            | 原型         |
| setPrototypeOf()            | 原型         |
| assign()                    | 属性表项增改 |
| defineProperty()            | 属性表项增改 |
| defineProperties()          | 属性表项增改 |
| getOwnPropertyDescriptor()  | 属性表列举   |
| getOwnPropertyDescriptors() | 属性表列举   |
| getOwnPropertyNames()       | 属性表列举   |
| getOwnPropertySymbols()     | 属性表列举   |
| keys()                      | 属性表列举   |
| values()                    | 属性表列举   |
| entries()                   | 属性表列举   |
| seal()                      | 属性表状态   |
| freeze()                    | 属性表状态   |
| preventExtensions()         | 属性表状态   |
| isSealed()                  | 属性表状态   |
| isFrozen()                  | 属性表状态   |
| isExtensible()              | 属性表状态   |
| is()                        | 其他         |

原型为null是原型继承中的特例，它有两种情况：其一，Constructor.prototype值为null；其二，Object.getPrototypeOf（obj）值为null。

当一个函数作为构造器使用，且它的prototype属性为null值时（或prototype属性为任何非对象值），这个函数也是能创建出实例来的。但实质上这个实例是直接通过new Object()创建的：

但有趣的是，尽管新实例不是由MyObject创建的，new运算却仍然会用它作为this来调用一次MyObject()。

这样的对象实例是一个只有一级（没有原型链）的属性包—只有一个自有属性表。比起空白对象（empty object），它“更加空白”—连Object类的内置属性也没有继承。不过它仍然具有对象的一切性质，包括作为其他对象的原型，或者使用Object.defineProperty()等方法来操作它。

与直接向Constructor.prototype属性置值不同，Object.setPrototypeOf()方法不接受对象和null之外的其他值。



JavaScript原型继承的实质便是对原型修改“效果的传递”。它基于以下两个事实。

■ 原型：原型是一个对象。

■ 原型链：在访问属性时，如果子类对象没有该属性，则将访问其原型的属性表。



从ES6开始，这一行为有了明确规范：重写操作被约定为针对自有属性表进行。因此重写的结果决定于写该属性时的性质设置，而不再继承自父类。

一个在父类中不能被删除、列举且只读的成员被重写后，就变成了可删除、可列举和可写的。



### JavaScript的类继承

super的出现是为了填补原型继承的一项众所周知的不足：无法有效调用父类方法。

类似

```
MyObjectEx.prototype.aMethod = function() {
	var thisClass = this.constructor;
	var parentClass = thisClass.prototype.constructor;
	parentClass.prototype["aMethod"](); // 调用父类方法
}
```

super.xxx作为方法调用时，将会隐式地传入当前方法中的this对象。

new运算将回溯它的继承链并使用顶端的原生构造器来构造实例。

类继承中的this实例：

■ 实际上是使用Object()构造器创建的。并且（使用它作为this引用）

■ 调用构造器，该调用是顺序，从基类开始一直到MyObjectEx()。



在类的构造方法中，不能在调用super()之前使用this引用。

显然，必须让所有的构造方法都先调用super()以回溯整个原型链，才能确保基类最先创建实例。这也是没有在类中声明constructor()方法时，JavaScript会为它默认添加一个构造方法、并在其中调用super()的原因。更确切地说，如果子类是派生的（derived），那么它就必须在constructor()中调用super，而非派生类（即没有声明extends的类）就不能调用它。

```javascript
class MyObject {
  constructor() {
    console.log('->'. 'MyObject')
  }
}

class MyObjectEx extends MyObject {
  constructor() {
    super();
    console.log('->', 'MyObjectEx')
  }
}

x = new MyObjectEx;
// MyObject
// MYObjectEx
```

this对象是在进入该构造方法之前就由引擎创建好了的（因此不需要在构造方法中调用super）；并且，在构造方法中还可以返回一个对象来替换这个默认创建的this对象。

由于ParentClass是一个变量的当前值，因此它是否为null值，即super是否有效只能在执行过程中才确知。

这种派生自null且没有构造方法的类其实也是可以创建实例的



```
class AClass {
	foo () {
		//	声明为AClass.prototype.foo 属性
	}
	static get name() {
		//  声明为AClass.name的属性存取方法
	}
	get name() {
		//	声明为AClass.prototype.name的属性存取方法
	}
}
```



```
class B {
    get name() { console.log('get');return 2 };
    set name(value) { console.log('set');return 1 };
}
var b = new B()

b.name  // 2
b.name = 3 
b.name // 2

class C {
    static get name() { console.log('get');return 2 };
    static set name(value) { console.log('set');return 1 };
}
var c = new C()
c.name // undefined
c.name = 3 
c.name //3
```



可以通过Object.defineProperty()等方法来声明（或覆盖）原有的属性描述符，从而影响对象属性的性质。这些可用的方法有三个：Object.defineProperty()、Object.defineProperties()、Object.create()



```
var obj = { data : 'oldValue'}

Object.defineProperty(obj, 'data', function(oldValue) {
	return {
		get: function() { return obj.data }
		configurable: false
	}
}(obj.data))
```

与赋值操作类似，如果在子类中使用这些方法来定义父类的同名属性（覆盖继承来的属性），那么该属性也将变成子类对象中“自有的”属性，它的可见性等性质由新的描述符来决定。但不同的是，“重新定义属性”这个操作与原型中该属性是否“只读”或是否允许修改性质（configurable）是无关的。

这可能导致类似如下的情况：在父类中某个属性是只读的并且其描述符性质是不可修改的，但在子类中，同名的属性却可以读写并可以重新修改性质。

属性的性质不可继承。

for...in语句得到的总是该对象全部的可见属性名，而Object.keys()将得到其中的一个子集，即（不包括继承而来的）自有的可见属性名。

Object.getOwnPropertyXXX()得到的与上述两种情况都不相同，它列举全部自有的属性，无论是否可见。也就是说，它是Object.keys()所列举内容的超集—包括全部可见和不可见的、自有的属性。

对象有一个内部属性[[Extensible]]用来影响其自有属性表的相关行为。该属性的默认值是true，表明一个对象是可以被添加和删除属性的。

seal与freeze状态不是直接的状态值，而是基于[[Extensible]]和现有的自有属性（的性质）而计算出来的、动态的值。其中，

■ seal是指[[Extensible]]为false，且所有自有属性的configurable性质为false。

■ freeze是指[[Extensible]]为false，且所有自有属性的configurable和writable性质都为false。



```
isSealed = function(obj) {
	return !Object.isExtensible(obj) && Object.values(Object.getOwnPropertyDescriptors(obj)).every((desc) => !desc.configurable);
}

// 当 desc.writable 以undefined值参加运算时并不影响结果
isFrozen = function(obj) {
return !Object.isExtensible(obj) && Object.values(Object.getOwnPropertyDescriptors(obj)).every((desc) => !desc.configurable && !desc.writable);
}
```



其一，对一个自有属性表为空的对象，使用preventExtensions（obj）将导致它同时密封和冻结。

其二，存取属性并不受freeze状态的“置属性只读”的影响。

因为Object.freeze()只是置所有数据属性的writable性质，而存取描述符并没有这个性质。因此在置值操作中，赋值器（setter）总是会被调用，而与对象是否被冻结无关。

其三，当父类（原型）冻结或指定属性只读，赋值运算会失效，但能用重新声明属性的方法达到与赋值运算相同的效果

赋值运算检查的是writable性质，修改和删除属性描述符时检查的是configurable性质，这两个都是针对“属性表项”的。而在子类实例（以及任何实例）中创建属性描述符时检查的是对象的[[Extensible]]属性，是针对“属性表”而非“属性表项”的。因此在[[Extensible]]这个内部属性为true时，对象就总是可以创建新属性，也包括“重新声明（覆盖）”父类同名属性。

总结为：属性表的性质不可继承。

从本质上来说，delete运算用于删除对象自有的属性描述符。



属性就是一切，继承的本质就是对自有属性表的管理。

| Symbol.xxx属性           | 影响行为                                                     | 属性值的界面                  |
| ------------------------ | ------------------------------------------------------------ | ----------------------------- |
| species                  | (隐式创建对象)                                               | function                      |
| hasInstance              | obj instanceof target                                        | function (obj) {}             |
| unscopables              | with  (target) ...                                           | { keyName: booleanValue, ...} |
| toStringTag              | target.toString()                                            | string                        |
| toPrimitive              | (值运算)                                                     | function (hint) {}            |
| iterator                 | for (... of  target) ... / new  CollectionTypes(target) /Promise.allOrRace(target) / arr.from(target) /(解构赋值、展开运算符) | 迭代器对象                    |
| (以下与数组或字符串相关) |                                                              |                               |
| isConcatSpreadable       | Arr.concat(target)                                           | true / false                  |
| match                    | str.match(target) / str.endsWith(target, ...) / str.includes(target, ...) / str.startWith(target, ...) / new RegExp(target, ...) | function (str) {}             |
| replace                  | str.replace(target, newString)                               | function (str, newString) {}  |
| search                   | str.search(target)                                           | function(str) {}              |
| split                    | str.split(target, limit)                                     | function(str, limit) {}       |

Symbol.toStringTag

```javascript
var obj = {
	[Symbol.toStringTag]: 'YourObjectClassname'
}

console.log(obj.toString()); // '[object YourObjectClassname]';
```

Symbol.toPrimitive

```
var obj = {
	[Symbol.toPrimitive](hint) {
		if (hint == 'number') return NaN;
    return 'invalid';
	}
};

console.log('' + obj); // 'invalid'
console.log(+ obj); // NaN
```

Symbol.hasInstance

``` 
class MyObject {
	static [Symbol.hasInstance](obj) {
		return super[Symbol.hasInstance](obj) || (obj && obj.className == 'MyObject');
	}
}

var obj = { className: 'MyObject' };
var obj1 = new MyObject;
console.log(obj instanceof MyObject) // true
console.log(obj1 instanceof MyObject) // true

var fakedBase = new Object;
fakedBase[Symbol.hasInstance] = (obj) => 'className' in obj;
console.log(obj instanceof fakedBase); // true

var atom = Object.create(null);
console.log(atom instanceof Object); // false, 不是Object()的实例
atom.className = '[META]'; // anything
console.log(new Object instanceof fakedBase) // false, 排除一般对象
console.log(atom instanceof fakedBase) // true, 是fakedBase的实例
console.log({ className: ''} instanceof fakedBase); // true, 支持字面量
```

Symbol.unscopables

unscopables指向一个对象，该对象的每一个属性指明是否从with闭包中强制排除对应的target.xxx属性。这是因为with（target）语句在试图将属性名作为名字访问—需要通过[[get]]行为去访问target的指定属性—之前，会用查表的方式查询unscopables对象。

```
var f = new Function;
var constructor = null; // 在with语句之外的标识符

// 会访问 f.prototype.constructor
with (f.prototype) console.log(f === constructor); // true

// 排除
f.prototype[Symbol.unscopables] = { constructor: true };

// 会访问到with语句之外的标识符
with(f.prototype) console.log( f ===  constructor ); // false 
```

Symbol.isConcatSpreadable

isConcatSpreadable是一个布尔值，仅当target是一个数组或类数组的集合（Collection）对象，并且试图使用arr.concat来连接它时才有意义。当target[Symbol.isConcatSpreadable]为true时，arr.concat()内部会调用循环取出target集中的每一个元素并添加arr—这个过程类似for...of target语句；否则将target对象作为单个元素添加到arr。

```
var obj = [1,2,3];

obj[Symbol.isConcatSpreadable] = false;

var arr = [0].concat(obj);
console.log(arr.length); // 2
console.log(arr[1]); // [1,2,3]

var arr = [0].concat([1,2,3]);
console.log(arr.length); // 4
console.log(arr[1]); // 1
```

连结展开只作用于arr.concat()方法，在数组连接时用于展示目标对象（target），整个过程中是对目标对象的下标存取。而展开运算针对有迭代器的对象，包括字符串、Set/Map等，运算过程中也是迭代器在起作用，与下标存取没有关系。

Symbol.match、Symbol.replace、Symbol.search和Symbol.split

以str.split()为例，假设我们要用一个一般对象（作为目标对象）来分隔字符串，那么只需为它的Symbol.split为名字的属性设置一个分隔方法就可以了

最后需要特别指出的是species符号，它指向一个构造器函数，主要影响JavaScript内部隐式创建对象的行为。



Proxy这个侵入的路径看起来很好，但实际使用时却会有些问题，这有两个方面的原因。其一是因为部分JavaScript内部检测会绕过代理。例如，new运算会检测目标函数是否是构造器，这是通过检查其[[Construct]]内部方法槽是否有值来实现的，会绕过代理中的handler.construct。

其二，因为这些内部方法事实上也是相互调用的，进而带来了递归调用的可能。



反映在语言上，原子操作相关的语法或调用总是安全的。但是这也意味着开发人员要保证它们在覆盖时也不得调用其他非原子操作，否则会破坏系统的稳定性。

最后再补充一下关于[[Set]]内部方法的操作。在[[Set]]置值时，如果它需要创建或更新自有的属性描述符，会调用[[DefineOwnProperty]]来实现。而且在整个过程中[[GetOwnProperty]]会发生多次，包括检测当前对象（例如，proxy）是否有指定属性，以及检测目标对象（例如，receiver）、目标对象的原型等。



Object()是JavaScript对象系统的基类。然而（仅对于Object.prototype来说），不能重置它的原型。

这是ECMAScript规范对Object.prototype的一点限制，确保它总是由宿主或脚本引擎来进行初始化。且一旦初始化之后，就不可再改变（这称为非可变原型，immutable prototype）。其具体的做法就是让Object.prototype的自有内部方法[[SetPrototypeOf]]指向一个特定的逻辑：

■ 如果置值等于既有的值，则返回true；否则，

■ 置初值之外的任何值，均返回false，表明不成功。

JavaScript也将Object.prototype属性的性质置为不可写和不可重置

侵入方法：扩展Object.prototype上的属性（包括添加或更新），从而影响所有以它为祖先类的子类和子类实例。

```javascript
function intrudeOnPrototype(Class, handler) {
  var original = Object.getPrototypeOf(Class.prototype);
  var target = Object.create(original);
  var {proxy, revoke} = Proxy.revocable(target, handler);
  Object.setPrototypeOf(Class.prototype, proxy);
  return () => revoke(Object.setPrototypeOf(Class.prototype, original));
}

var str = new String('OldString');
var recovery = intrudeOnPrototype(String, {
  get: function(target, prop) {
    if (prop === 't') return 100;
    return Reflect.get(...arguments);
  }
})
var str2 = new String('NewString');

console.log(Object.getPrototypeOf(str) === Object.getPrototypeOf(str2)); // true
console.log(str.t) // 100
console.log(str2.t) // 100

console.log((new Object).t) // undefined

//重置
recovery();
console.log(str.t) // undefined
console.log(str.t2) // undefined
```

直接替换Object.prototype并不是严格意义上的运行期侵入，因为这会导致替换前/后有两种不同原型的对象实例。又例如，其实你也可以替换Object()构造器本身。但这些都不是好的运行期侵入手段—它们会被感知到，也通常是不推荐的。



所谓元类（meta class），是指能产生类的类。

在运算符instanceof的一项约定：当它的右操作数是一个函数时，则使用该函数的.prototype来检查原型链。因为在这个表达式中，函数指代的是类（构造器），它之于对象实例的原型是Constructor.prototype（而不是构造器自身）。



在MetaClassEx中声明的bar()方法又去哪儿了呢？bar()方法，准确地说是MetaClassEx.prototype.bar()方法，确实是被丢弃了。因为任何Meta所创建的类都是一个原子类（Atom），它的原型是null。这意味着“元”的原型不对它的实例（类）起任何作用。



■ 原型继承系统：可以没有构造器的、原生的JavaScript对象系统。

■ 类继承系统：使用class声明的ES6风格的面向对象系统。

■ 元对象继承系统：以Atom，即MetaObject为基类。

■ 类类型继承系统：以MetaClass为基类。

■ 以及元编程系统：以Meta为元的可编程系统。



可以思考下元编程。



### JavaScript语言的结构化

语言的四大类可分为命令式语言、函数式语言、逻辑式语言和面向对象程序设计语言

从程序本质，可分为命令式语言和说明式语言。

#### 命令式语言

命令式语言的核心就在于“通过运算去改变内存（中的数据）”。

在结构化程序设计语言中，对结构的解释包括三个部分：程序的控制结构、组织结构和数据结构。所谓控制结构，即顺序、分支和循环这三种基本程序逻辑；所谓组织结构，即指表达式、语句行、语句块、过程、单元、包等；所谓数据结构，包括基本数据结构和复合数据结构，且复合数据结构必然由基本数据结构按复合规则构成。

抽象层次过低带来的问题至少包括三个方面。其一，结构体与实体直接相关，并且将这种相关性直接呈现在使用者的面前，因此开发人员必须面临数据的具体含义与关系。

其二，结构体的抽象更面向于数据存储形式的表达和算法实现的方式，脱离了具体使用环境和算法的结构缺乏通用性。

其三，僵化的类型与僵化的逻辑并存，影响了业务逻辑的表达。

#### 面向对象语言

封装、多态、继承

“面向对象”所解决的主要问题：

■ 开发人员必须面对数据的具体含义与关系。

■ 脱离了具体使用环境与算法的结构缺乏通用性。

■ 类型与逻辑僵化从而影响了业务逻辑的表达。

“结构化”的抽象是实体到结构体的直接映射，而“面向对象”的抽象则是实体到类、衍生关系到“继承性”的映射。

#### 更高层次的抽象：接口，面向接口的编程方法

接口（Interface）提出的观点就是：只暴露数据体的逻辑行为能力，而不暴露这种能力的实现方法和基于的数据特性。这里用“数据体”而不是“对象”，是因为Interface并不关注“接口系统实现者”的数据结构特性，例如，是使用“对象/类类型”来实现接口，还是用“结构类型”来实现接口（尽管在具体语言中，这是与确定的数据类型相关的）。

算法+数据结构=程序

命令式语言关注于数据结构，其本质是基于结构的运算。

亦即是说，对于“算法+数据结构”，说明式语言侧重于描述“基于算法的实现”，而命令式语言侧重于实现“基于结构的运算”。

说明式语言的几种子分类的区别主要在于“说明”所陈述的主体的不同。例如，函数式主要陈述运算规则，数据流式主要陈述数值计算，逻辑式则主要陈述推理过程等。

JavaScript也是语言不同分类间相互作用的产物，它既同时是说明式和命令式语言，又兼具串行和并行语言的特征。有着这样交叉分类特性的语言，通常被称为“多范型语言（Multi-paradigm Programming Language）”。

所谓结构化包括：程序的控制结构、组织结构和数据结构。

箭头函数体在表面或语法上看来是表达式，但语义上却是语句的组织结构。

所有JavaScript的语句也可以分为声明语句和非声明语句两类，所有的声明语句都是静态词法分析的，而非声明语句则是动态执行的（即用来叙述计算逻辑的）。

模块只有两个语义，其一是它包含了一个功能集合，并构建和对外宣称了这个集合的一个列表；其二是它用于从外部得到上述列表，并按当前（外部）环境的语义理解和使用该列表。模块在语义上的成功之处，在于它是平衡“对功能的展示和对细节的隐蔽”两种需求所能带来的最小可能结果。



#### 组织的原则

原则一：抑制数据的可变性

JavaScript应用环境中推荐使用let/const来替代var，本质上是在控制数据可变性的范围（作用域），以便达到在该范围中减少状态总数的目的。

原则二：最小逻辑和最大复用

JavaScript是一门既支持时序逻辑的语言，也是一门支持非时序逻辑的语言。前者是所谓顺序编程（串行）；后者，是所谓并行编程。

原则三：语法在形式上的清晰与语义一致性





在JavaScript中，除const和namespace之外的所有语句声明的名字都是使用MutableBinding来创建的

除了计算顶层符号之外，在多数情况下，所谓语句的“词法声明与变量声明”是用在语法分析阶段来检测作用域中是否存在相同的标识符名的。另外，在初始化一个环境（Block/Global/Function/Eval/Module）时，也将利用这个（在语句分析期得到的）标识符列表来创建和初始化绑定。



对于全部4种for语句来说，一旦使用带let/const声明的语法，那么：

■ 将总是存在一个自有的形式分块（loopEnv）用于处理循环表达式；并且，

■ 还将有一个形式分块（iterationEnv）来处理由迭代次数决定的循环体实例。

​	◆ 如果是for语句，在每次循环中将创建一个新的实例并将它的父级指向loopEnv。

​	◆ 如果是for...of、for...in，以及for await...of，则每次循环所创建实例的父级直接指向for语句“所在的块”。



在每次创建iterationEnv的新实例时，从上一个实例中复制所有let/const声明的变量值（而不是串接新旧环境）。



由于parameter实质上是名字声明—标识符与异常对象的绑定，因此它也可以使用赋值模板。

```javascript
try {
  throw { message: 'A Eorror', code: 1234}
} catch ({message, code}) {
  console.log(`${code}: ${message}`); // 1234: A Error
}
```

因此在所有的单语句(if 、 while、 do...while... 、for...)中都不能使用let/const来进行数据声明。

另外，var语句在作用域上的特殊性决定了它不会受到这一限制。

由于解析“声明”是在语法分析期发生的而非在执行期，因此事实上所有的声明语句都会导致JavaScript在语法分析期构建对应的名字表

函数所声明的形式分块包括它的函数名和参数名，并且它的函数名将会在“所在的块（的varNames）”中进行一次登记。而模块所声明的形式分块则另具特殊性。因为模块的使用机制约定—同一模块的多次import之间会共享同一个块（以及其环境），因此事实上它的形式分块不是由import语句来创建的，而是由JavaScript引擎在装配所有模块的过程中创建的。

函数表达式与函数声明语句的主要不同，在于后者将会在“所在的块”中登记函数名。这也是不存在“声明匿名函数的语句”这样的语法的原因。



形式分块与“块级别的”作用域对应—在代码文本中有一个物理上可见的形式分块，也就意味着JavaScript会为该分块创建作用域，称为词法作用域。

| 序号 | 作用域 |          | 示例                                                         | 说明                                                         |
| ---- | ------ | -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 1    | 表达式 |          | 不存在                                                       |                                                              |
| 2    | 语句   | 数据声明 | var / let / const                                            | 语法关键字表示特定的行为动作。被省略部分是动作的目标，而不能是复合语句。 |
| 2    | 语句   | 一般语句 | if()...else... / for ().../ do...while()/while().../with()... | 被省略的部分既可以是单行语句，也可以是{}标识的块（指语句本身的作用域含义） |
| 2    | 语句   | 块       | {...}; /switch() {...}/try {...} catch() {...}finally{...}/  | 这些语句关键字本身也是语句，并且它们用{} 来限制一个新的形式分块，并作为它的子级词法作用域。（有形式分块的） |
| 3    | 函数   |          | function x() { ...};                                         | 一函数所声明的形式分块为作用域。                             |
| 4    | 模块   |          | export ...;                                                  |                                                              |
| 5    | 全局   |          | 依赖于host的实现                                             |                                                              |

当引擎将一个文件识别为一个模块时，它会为该模块创建一个与形式分块一致的作用域，这个作用域包括全部export的名字。引擎会使用一个特定的结构（namespace）来记录所有的导出名字，并允许其他模块使用import来引用它，并进而找到这个作用域（用来组合这些模块间的作用域层次）。

#### 词法作用域

代码分块带来的语法效果是信息隐蔽。一般来说，所谓信息隐蔽，指的是变量或成员的可见性问题。而这个可见性的区间（即“域”），被称为作用域（Scope）。当这个域是通过静态词法分析而得出的时候，它就被称为词法作用域（Lexical Scope）。

词法环境（Lexical Environment）是词法作用域这一概念的运行期实现：代码在物理行上、显式地进行形式分块，并在引擎执行期映射成词法环境记录（Environment Record），以便将“块级别的”词法作用域实例化

##### 不存在“级别1：表达式”

在JavaScript中并没有所谓的“表达式级别的”词法作用域，因为表达式没有自己的词法环境。但这存在着一个“有分歧”的例外，即eval()其实是一个“（函数调用）表达式”，且又确实拥有一个独立的Eval词法环境，因此这种动态语言特性成了“唯一”在JavaScript中拥有表达式级别的词法作用域的特例。

##### 级别2：语句

这是由于早期的JavaScript只支持所谓的“（函数级别的）变量作用域”，而“块级（词法）作用域”是直到ECMAScript 2015之后才提出的。

■ case分支使switch内的代码形成了局部分块，但这不是语法效果。亦即是说，这不是JavaScript语法概念上的某种作用域。

■ break语句的含义是跳出switch语句，而非跳出某个case分支。

而case语句只是在这个范围内做了一些标识，以提示一个“起始位置”，并没有词法作用域所必需的“结束位置”的含义。

##### 级别3：函数

当使用函数调用运算“()”时，JavaScript会为函数创建一个词法环境，以便将该函数的形式分块实例化为一个函数级的词法作用域。

在ES6之后，上述语句有了明确的解释：对于语句内的函数声明，其名字将提升至语句之外的函数或全局作用域（这让它的行为看起来与用var进行变量声明一样）。对此，需要特别补充或强调的是：之所以在函数内可以声明子级的函数，而在语句内却无法（在它的块级作用域中）声明函数，根本上是因为函数的名字是一个变量名（varNames），而不是一个词法名字（lexicalNames）。

箭头函数也是有自己的作用域的。

至于箭头函数没有arguments和函数名等标识符，并且所绑定的this引用来自“所在的词法环境”等特性，是箭头函数的语法语义所决定的，也与作用域无关。

##### 级别4：模块

模块词法作用域所对应的形式分块是由模块的export声明决定的，执行环境将预先扫描所有这些声明并进行词法分析，然后会在实例化时创建一个自有的词法环境来登记它们。但这个实例化过程却是在执行import声明来导入该模块时才会发生的，用以绑定全部的顶层声明：在多数情况下，你可以将这些声明（包括varDecls和functionDecls和其他顶层的块中的声明）理解为需要在顶层登记的名字。

这些名字是模块词法作用域中的，而非全局的。

模块中的代码只会执行一次，如果模块中的名字被导出，那么所有导入它的模块将共享同一个实例。这在引擎中并不难实现，只需在导入变量时将变量名绑定到目标实例的一个引用上就可以了，这个所谓的“引用”是ECMAScript内部的规范类型。并且，所有的导入都将使用所谓的“本地名字”在当前模块的词法作用域中登记，这些本地名字是只读和不可重新声明的，即使它们在源模块中是可以读写的变量。

在模块的词法作用域中访问this引用时，总是得到undefined值。

##### 级别5：全局

JavaScript的全局环境中一共存在三种用来登记名字的组件，包括词法作用域、变量作用域和全局对象（global）。所有变量声明（varDelcs和functionDecls）的名字都在变量作用域中登记，而用户代码通过“访问不存在的变量名”导致的名字创建是作为全局对象的属性登记的。最后，其他的let/const等声明导致的登记，才是发生在全局的词法作用域中的。

全局词法作用域位于最外层的环境，它包括其他所有顶层的代码分块，以及它们对应的作用域，又或者它们更子级的块。用户代码运行在这个词法作用域中，但并不能通过全局对象（global）来访问到它，例如下例中用户代码可以访问到变量x，却不能通过global来引用到“x”这个变量名

例如，浏览器中所谓全局对象是指window，而Node.js则指向一个引擎内建的对象。同理，由于浏览器将所有脚本文件加载到同一个全局环境，所以在这些文件的最外层代码就可以通过this引用直接访问到global；而在Node.js中将各个.js文件作为各个独立的模块加载进来，每个模块都有自己的环境，因此在主模块中访问的this并不是global。

因此，在Node.js中，要在模块（包含主模块）中向global添加变量名时，就只能利用eval的间接调用机制。

#### 执行流程及其变更

##### 级别1：可能的逃逸

由于在JavaScript中不能重载运算符，因此除了通过定制操作数的性质来影响运算符的行为，没有其他途径来实现这种“可能的逃逸”。

##### 级别2：“break<label>；”等语法

流程变更语句可以跨越相同等级的作用域。

##### 级别3：return子句

■ break <label>；不能跨越函数的词法作用域。

■ return子句可以跨越任何语句的作用域而退出函数。

它们事实上处在不同的词法作用域中

■ 流程变更语句可以“穿过”相同级别的任何词法作用域。

■ 高级别的流程变更语句，可以“穿过”嵌套在其内的任何低级别的词法作用域，反之则不成立。

##### 级别4：动态模块与Promise中的流程控制

静态加载的模块并没有执行流程的概念，而对于动态模块来说，它的作用域将会“被包含于”一个Promise对象的执行环境中，类似在“newPromise（func）”语句的func中。

##### 级别5：throw语句

“try...catch...finally”语法结构的词法作用域是“语句”，而throw则是“全局”

#### 词法作用域之间的相关性

结构化语言中的作用域是互不相交的，在这些作用域（及其对应的形式分块）之间只存在平行或嵌套两种相关性。

■ 相同级别的词法作用域可以相互嵌套。

■ 高级别的词法作用域能够包含低级别的词法作用域。

■ 低级别的词法作用域不能包含高级别的词法作用域。由于不存在包含关系，因此语言实现时，一般处理成语法上的违例或者强制解释为平行关系。

#### 执行流程变更的内涵

■ 词法作用域是互不相交的。正是作用域互不相交的特性构造了代码结构化的层次，并消除了一些错误隐患。

■ 词法作用域间可以存在平行或包含关系。高级别可以嵌套低级别的词法作用域，反之则不成立。

■ 高级别的流程变更子句（或语句）可以跨越低级别的作用域，反之则不成立。

但在JavaScript/Java中，标签化语句是有“语句”级的词法作用域的，而“break <label>；”的效果仅影响该作用域，因此它并不是那种“无所不能的GOTO”。“break<label>；”对循环与分支的影响，可以解释为同等级词法作用域下的GOTO效果。

这种形式使得代码清晰，并且能表达结构化分析阶段对系统自顶向下逐层精化时所展现的逻辑组织。

程序执行的流程变更，本质上已经转义为作用域（及其等级）的变更

高级别的流程控制语句，对低级别的语句的作用域会产生“穿透”，这正是流程控制的关键，也是结构化编程严谨而不失灵活性的关键。其中的要诀，在于让流程变更子句（或语句）的设计覆盖不同级别的作用域，以获得最大的灵活性；但并不必覆盖所有的语句或语法结构—那将导致浪费和纵容。

### 层次结构程序设计

封装、继承与多态是面向对象解决“结构性问题”的三种具体手段。

■ 封装与继承是对数据（即对象性质）的结构化，分别展示数据的内聚与外延。

■ 多态是对象性质在继承关系上的表现。

#### 属性的可见性

并且由于所有的属性都是公开的，所以任何时候、任何环境下只要能获得这个对象（的引用），就可以无差别地访问它的全部属性。

符号具有隐匿名字的特性，但并不能隐匿可见性。当一个对象使用某个符号作为属性名时，用户代码至多也只是不知道其符号名而已，仍然是可以通过类似如下代码来访问该符号的：

■ 私有的（privated），仅在实例或类作用域内部的方法可访问。

■ 静态私有的（static privated），在类或（和）实例的作用域内部可以访问。



■ 保护的（protected）。在子类实例中可访问的父类或父类实例属性。

■ 内部保护的（internal protected）。在同项目内子类实例中可访问的父类或父类实例属性。

#### 多态的逻辑

在作用域或可见性概念上，可以将super理解为是受保护的（protected）：在类/对象中私有，且用于访问父类

#### super是一个作用域相关的绑定

super关键字是上下文受限的，并且是词法作用域（静态）绑定的。这个关键字只能使用在类声明（含类表达式）和对象字面量风格的声明中，并且只能出现在方法或静态方法声明内部。

它既是受限的（即词法作用域绑定的），又是对多态逻辑绑定的。

### 历史遗产：变量作用域

全局的变量作用域是映射到全局对象的属性上的，并非一个独立的作用域/环境。

#### 变量作用域

| 序号 | 使用域 | 示例                                   | 变量作用域 |
| ---- | ------ | -------------------------------------- | ---------- |
| 1    | 表达式 |                                        | 不存在     |
| 2    | 语句   |                                        | 不存在     |
| 3    | 函数   | function x() ...  / function * x() ... | 支持       |
| 4    | 模块   | var x = 100;                           | 支持       |
| 5    | 全局   | var  x = 100; / y=100                  | 支持       |

对于ES6开始加入的模块来说，变量是声明在模块的变量作用域中的，如果它被export，那么还会有一个同名字的项被登记在名字空间中。

具名函数和var声明一样，所声明的名字都是变量名，登记在变量作用域中。并且与变量名一样也可以多次声明、相互覆盖，效果与重写或置值类似

##### 级别3：函数（局部变量）

由于JavaScript不存在语句级别的变量作用域，因此for循环中的变量y也就“逸出”到函数的变量作用域中，变成（函数的）局部变量。

而全局不能访问局部的、私有的信息，这是信息分层和信息屏蔽的重要原则。

##### 级别4：模块

由于模块的作用域总是严格模式的，因此在它其中的—包括子级函数中的代码，都无法使用eval()来动态地在外层变量环境中去“创建/声明”一个变量。

主模块也将有自己的模块级的变量作用域

所有这些模块是相互依赖而非嵌套的，因此模块之间除了使用export/import来相互引用之外，无法影响对方的变量作用域（以及模块环境）。

因此JavaScript以下两项特性是不利于大型项目的开发的：

■ 在全局范围内随意声明变量，包括在全局范围内使用if、for等语句声明变量。

■ 向一个未声明的变量名赋值时，会隐式地声明一个全局变量（全局属性）

#### 变量的特殊性与变量作用域的关系

JavaScript中的变量具有三大“有违常识”的特殊性。

##### 变量提升

一个在当前作用域的任意位置用var声明的变量，其标识符会被“提升”到当前作用域或其外层的、最低级别的变量作用域（的开始处）。即，从这个外层的变量作用域开始的全部代码都可以访问该变量。

JavaScript采用基于语句的静态语法分析，在这个过程中它将扫描所有顶层的变量声明，以便在顶层—整个引擎的全局，或者函数与模块的最外层—构建包含这些声明名字的环境。

执行代码在声明代码的物理行之前

在实现上，它将声明在作用域中的名字创建为一个“已初始化（为undefined）的绑定”，因此可以在作用域的任意位置访问。

与var语句声明的变量“在初始时置为undefined”不同，函数声明在初始时就绑定了函数体—具体的函数实例。因此函数声明不但是提升的，而且还是静态绑定的。

##### 变量动态声明

JavaScript的词法作用域是静态的，因此一旦它被实例化（Instantiation）就不能再增删。而变量作用域是动态的，在非严格模式中，可以通过eval()来向它添加新的项。

但最终这些变量仍然是作为“全局属性”来实现的

而在模块中，由于没有动态代码能执行于它的顶层环境，即严格模式下的、名字空间所在的环境，因此无法在它的变量环境中添加新项。因此，变量动态声明事实上只会发生在函数的变量作用域中。

##### 变量隐式声明（全局属性）

###  私有属性与私有字段的纷争

#### 私有属性的提出

##### 对象字面量中的作用域问题

但在JavaScript中并没有所谓的“对象作用域”，因此它们事实上都受obj对象更外围的作用域的影响。

所以，就目前的语法设计来说，没有办法在对象字面量中添加一个私有属性，满足：

■ 条件1：“只允许”在特定的作用域中访问；或/并且，

■ 条件2：“只允许”特定的方法或函数来访问。

##### 类声明中的作用域问题

类声明体被分成两个块，其中类的作用域被映射到constructor()方法上，

其次，记录（Record）是ECMAScript规范类型（Specification types）的基础，ECMAScript规范是通过一系列的记录及其成员（也就是Fields）来描述整个语言的实现的。最后，在ECMAScript规范所约定的语言类型（Language types）中的对象—也就是JavaScript的对象中，对象是由一系列内部字段（Field，Internal slots）和属性描述符的集合构成的。

##### 再论私有成员

在所有设计中的所谓类字段，都包括两个层次的含义：

1．如果它是一般类成员，那么它将被类的实例（即对象）继承，所有该类的实例都具有这个类成员的一个独立副本。

2．如果它是静态类成员，那么它将只是类的字段，（如果是私有的，则只能）被类的方法访问。



■ 字段不用于实现“保护属性”这样的语言特性，不得使用下标存取语法和“可计算属性名”这样的特性。

■ 字段在词法上被说明为使用私有标识（PrivateIdentifier）的类成员，属于标记（Tokens），与一般标识符名字（IdentifierName）处于同一抽象层级。

■ 字段不得使用“非名字”的标识符，例如不可以声明“1”或空字符串作为字段名，因为它不是合法的标识符名（与此相反，“1”和空字符串都是可以作为属性名的）。



私有名是一个特殊名字，只在类处理＃xxx这样的私有字段声明时产生，并且每个私有名都是唯一的—即使它们声明的字面名字相同。

通过这种新的规范类型，类字段提案阻止了通过a.＃x去访问b.＃x这样的欺诈行为。

由于每个对象都通过一个称为[[PrivateFieldValues]]的私有槽来持有自己的私有名/值对，因此数量巨大以至于有必要启动自动回收机制。

##### 概要分析

在静态语法阶段，该提案需要验证一个类声明环境中所有的标识符的有效性（AllPrivateIdentifiersValid）；在运行期，它需要为每一个实例初始化私有结构；在引擎级别，它需要在对象的回收阶段加入单独的字段回收处理；

##### 语法与语义的关系

■ 只有使用new MyClass()语法风格得到的obj才具有一个指向原型的[[Private]]私有槽，其他情况下的对象私有槽的原型将指向null。

#### “私有属性”提案的实现

##### 核心的实现逻辑

■ 定义为只有声明在对象或类中的那些方法能访问的属性，称为“私有属性”。

■ 在调用对象方法时，使用对象的[[Private]]私有槽创建一个对象闭包。

##### 保护属性的实现

保护属性在概念上是可继承的私有属性，而[[Private]]私有槽本身是一个标准的JavaScript对象

可以为每个类的原型属性（MyClass.prototype）添加一个[[Protected]]私有槽，并使它的原型指向父类的[[Protected]]

在这个继承结构上，MyClass的私有属性只会对它的实例（例如a）可见，而它的保护属性将对所有子类（例如MyClassEx）和子类的实例（例如b）可见。而这正是OOP中对保护属性、私有属性的可见性定义。

##### 可见性的管理（unscopables）

由于私有属性是使用标准JavaScript对象保存，并在方法调用中使用with()语义创建的一个对象闭包，因此我们就可以简单操作原型的＠＠unscopables符号属性，来使某个私有成员可见/不可见。

## JavaScript的函数式语言特性

### 概述

■ 数学函数是集合A（称为定义域）中成员到集合B（称为值域）中成员的映射。

■ 函数式程序设计就是对函数定义、函数应用加以说明，其运算过程即是对函数应用求值。

所谓函数式语言，就是通过连续表达式运算来求值的语言

为什么可以将语言分成命令式和说明式？

连续运算是函数式语言的基本语言特征之一。

■ 运算（表达式）以产生结果。

■ 结果（值）用于更进一步的运算。

尾递归是在一个函数的执行序列的最后一个表达式中出现的递归调用

#### 运算式语言

一是陈述运算，二是求值。

#### 函数式语言

函数式语言中的“函数（function）”除了能被调用之外，还具有其他三个方法的性质：是操作数、可保存数据，以及无副作用。

##### 函数内的运算对函数外无副作用

所谓运算对函数外无副作用，其含义在于：

■ 函数使用入口参数进行运算，而不修改它（作为值参数而不是变量参数使用）。

■ 在运算过程中不会修改函数外部的其他数据的值（例如全局变量）。

■ 运算结束后通过函数返回向外部系统的传值。

##### 函数式的特性集

■ 在函数外消除语句，只使用表达式和函数，通过连续求值来组织代码。

◎ 在值概念上，函数可作为操作数参与表达式运算。

◎ 在逻辑概念上，函数等义于表达式运算符，其参数是操作数，返回运算结果。

■ 函数严格强调无副作用。

### JavaScript中的函数

##### 默认参数

尽管所有的默认参数都是有名字的形式参数，但是从第一个默认参数开始，后续的所有参数都不会再记入形式参数计数（也就是不会反映在aFunction.length属性中）。

##### 参数对象

参数对象arguments只有一个简单的规则：用一个类数组的对象顺序包含全部传入参数。

arguments是函数内部的代码可以访问的一个变量。除了箭头函数之外，所有函数在被调用时都自动创建一个对象用作该变量。属性arguments.length总是表明实际传入的参数个数。由于它是类数组的，所以可以使用绝大多数数组操作（包括模板赋值、展开语法和剩余参数等）。

##### 非简单参数

默认参数、剩余参数和模板参数被统称为“非简单参数（non-simple parameters）”。当参数声明中使用了非简单参数时，会导致函数进入一种特殊模式。在该模式下会有三种限制：

■ 函数无法通过显式地使用"use strict"语言切换到严格模式，但能接受它被包含在一个严格模式的语法块中（从而隐式地切换到严格模式）。

■ 无论是否在严格模式中，函数参数声明都将不接受“重名参数”。

■ 无论是否在严格模式中，形式参数与arguments之间都将解除绑定关系。

“用初始器赋值”和“形式参数与参数对象绑定”两种实现方式中二选一，用来处理调用时的传入参数。而那些非简单参数（参数声明中的默认参数、剩余参数或模板参数）只能使用“初始器赋值”来初始化，于是所有的参数都不再支持“形式参数与参数对象绑定”了。

当使用非简单参数时，通过arguments获得的参数是不被赋于初值的，因此它可能会与通过参数名得到的值不一样。

##### 非惰性求值

这种在不需要使用某个操作数的情况下，仍然处理了它的求值运算的特性，就称为“非惰性求值”，它总是积极地、优先地获得操作数，而无视它是否最终被使用。

#### 函数

“具名”列表明该函数是否支持一个确定的函数名。有且仅有通过声明方式得到的函数才是可以具名的。函数具名的主要意义在于：在声明该函数的同时，在上下文中具有了一个该名字的标识符。函数也可以另外有一个与此无关的name属性：一方面，它并不能表明函数是否具名，另一方面具名函数也可能有着与名字不同的name属性。

##### 一般函数

所以具名函数与匿名函数的差异也仅仅体现在这两个方面：1.是否有一个可影响当前作用域的标识符；2.是否可以用作声明语句。除此之外，它们具有的函数性质是一样的，包括用作构造器（new运算符）和可以调用（函数调用运算符“()”）。

##### 生成器函数

生成器函数是可以作为函数调用的，但不能用作构造器（这与“类”正好相反）。调用生成器函数时，该函数声明的函数体并不执行，而是直接返回一个生成器对象（Generator/Generator Object，例如tor），生成器对象同时也是一个可迭代对象。

与此类似的是Symbol设计，所有符号都是Symbol值集之一，但Symbol()只能执行而不能用new运算来创建实例。

在生成器函数内，this引用总是指向调用该生成器函数（以得到tor对象）时所传入的this。

yield也是仅在生成器函数内可用的一个运算符

与return只能用于函数内类似，而又有所不同：1.yield是运算符，而return是语句/子句；2.yield运算符“产生”一个值，但并不退出函数；3.yield本身（由于是运算符）是有计算结果的。



ES6风格的方法

■ 方法不能作为构造器使用。

■ 方法没有prototype属性（生成器作为方法时例外）。

■ 方法不能具名。

##### 箭头函数

箭头函数（Arrow function）总是用字面量声明语法来声明的，但它不是声明语句，只能作为表达式的操作数，并以表达式所在的上下文作为它的执行环境。此外，箭头函数总是匿名的，因为它的声明语法无法声明标识符。

箭头函数不能用作构造器，但可以作为函数或方法（这里指对象的“函数类型的属性”）来调用。另外，箭头函数也可以声明是异步的，因此在它的函数体内也可以使用await运算符。

箭头函数除了在语法形式上“更简单”之外，主要的特点是：1.它永远不持有自己的this引用；2.不会有参数对象（arguments）来代表传入参数。

它的代码总是会访问到当前上下文中的this：

如果它声明在某个函数中，则是该函数调用时有效的this。出于这一点限制，apply/call调用也无法传入thisArg，且bind()方法也无法将新的thisArg关联给它，尽管bind()方法会返回一个有效的绑定函数。

##### 绑定函数

■ 内部原型被置为与targetFunc的原型一致。

■ 没有自有的prototype属性。

其中thisArg总是使用bind()方法传入的参数且不可替换，而新的、在调用绑定函数时传入的参数会追加在已绑定的arg1...n参数的后面。

##### 代理态的函数

函数与一般对象的区别在于，前者的内部结构中初始化了[[Call]]和[[Construct]]这两个内部方法（之一或全部）。

■ 类声明中的方法是严格模式的，因为类工作在严格模式中。

■ 箭头函数没有arguments引用，尽管剩余参数（例如...args）可以部分替代之，但仍然无法访问到类似arguments.callee这样的属性。

##### 块级作用域中的函数

所以即使两个函数体相同，只要它们在不同的块中，那么它们就是不同的函数；又或者在循环的不同迭代中的函数，也是不同的。

函数声明是在语法分析期完成的，但函数实例的初始化是在运行期进行的。因此函数初始化的位置与运行期性能有关，这也是更推荐将函数放在模块中的原因

相较于在函数甚至循环体内“即用即声明”，在最外层的块级作用域中初始化次数最少。
