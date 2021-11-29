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

模板字面量本质上来说是一个字面量的引用—该字面量在JavaScript内部表达为一个对象（array-like object）或数组。

```javascript
foo = tpl => console.log(typeof tpl, tpl instanceof Array, ref = tpl)
foo `xyz`
object true ['xyz', raw: Array(1)]
```

符号没有字面量声明形式。由于符号是值而非对象（我称之为“近似对象的”一种值类型数据），所以也不能使用new运算符来创建它。

或者也可以不声明形式参数，这时也可以在函数体内使用一个名为arguments的内部对象来存取函数调用时的传入值。

在ES5以后的规范中，明确规定了在表达式中出现的具名函数名只影响该函数内的代码，而不会影响该表达式所在的作用域

在声明函数的形式参数时，可以为参数指定默认值“（defaultparameters）”

如果默认值并没有放在参数列表的尾部，那么可以使用undefined，在传参时表明该参数使用默认值。

可以在参数列表尾部使用“剩余参数（rest parameter）”，它用一个带...前缀的参数名（形参）来捕获所有未被声明的实参。在这种情况下，该形参总是一个包含所有剩余参数的数组

也可以在剩余参数中使用赋值模板。例如，下面用一个数组赋值模板替代了上例中的形参z：

```javascript
function foo(x, y, ...[m,n]){
    console.log(m)
}
foo('a','b','c','d','e')
```

3.表达式运算

