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

