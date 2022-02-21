# TypeScript
## 选择TypeScript的理由

1.类型有利于代码的重构，它有利于编译器在编译时而不是运行时捕获错误。

2.类型是出色的文档形式之一，函数签名是一个定理，而函数体是具体的实现。

## Void
某种程度上来说，void类型像是与any类型相反，它表示没有任何类型。 当一个函数没有返回值时，你通常会见到其返回值类型是 void：

```
function warnUser(): void {
    console.log("This is my warning message");
}
```

声明一个void类型的变量没有什么大用，因为你只能为它赋予undefined和null：
```
let unusable: void = undefined;
```

## Null 和 Undefined
TypeScript里，undefined和null两者各自有自己的类型分别叫做undefined和null。 和 void相似，它们的本身的类型用处不是很大：

```
// Not much else we can assign to these variables!
let u: undefined = undefined;
let n: null = null;
```

默认情况下null和undefined是所有类型的子类型。 就是说你可以把 null和undefined赋值给number类型的变量。

然而，当你指定了--strictNullChecks标记，null和undefined只能赋值给void和它们各自。 这能避免 很多常见的问题。 也许在某处你想传入一个 string或null或undefined，你可以使用联合类型string | null | undefined。 再次说明，稍后我们会介绍联合类型。

注意：我们鼓励尽可能地使用--strictNullChecks，但在本手册里我们假设这个标记是关闭的。

## Never
never类型表示的是那些永不存在的值的类型。 例如， never类型是那些总是会抛出异常或根本就不会有返回值的函数表达式或箭头函数表达式的返回值类型； 变量也可能是 never类型，当它们被永不为真的类型保护所约束时。

never类型是任何类型的子类型，也可以赋值给任何类型；然而，没有类型是never的子类型或可以赋值给never类型（除了never本身之外）。 即使 any也不可以赋值给never。

下面是一些返回never类型的函数：
```
// 返回never的函数必须存在无法达到的终点
function error(message: string): never {
    throw new Error(message);
}

// 推断的返回值类型为never
function fail() {
    return error("Something failed");
}

// 返回never的函数必须存在无法达到的终点
function infiniteLoop(): never {
    while (true) {
    }
}
```

## TS 中 type 和 interface 的区别
### 概念
#### interface 接口
接口主要用于类型检查，它只是一个结构契约，定义了具有相似的名称和类型的对象结构。除此之外，接口还可以定义方法和事件。

#### type (alias)类型别名
不同于 interface 只能定义对象类型，type 声明还可以定义基础类型、联合类型或交叉类型。

### 差异点
1. 定义类型的范围
interface 只能定义对象类型, 而 type 声明可以声明任何类型，包括基础类型、联合类型或交叉类型。

```
// 基本类型
type Person = string;
// 联合类型
interface Dog {
  name: string;
}
interface Cat {
  age: number;
}
type animal = Dog | Cat;
// 元组
interface Dog {
  name: string;
}
interface Cat {
  age: number;
}
type animal = [Dog, Cat];
```
2. 扩展性
接口可以 extends、implements,从而扩展多个接口或类。类型没有扩展功能，只能交叉合并。
```
interface extends interface
interface Person {
  name: string;
}
interface User extends Person {
  age: number;
}
interface extends type
type Person = { name: string };
interface User extends Person {
  age: number;
}
```
type 使用交叉类型&来合并不同成员的类型
type & type
```
type Person = { name: string };
type User = Person & { age: number };
type & interface
interface Person {
  name: string;
}
type User = { age: number } & Person;
```
3. 合并声明
定义两个相同名称的接口会合并声明，定义两个同名的 type 会出现异常。
```
interface Person {
  name: string;
}
interface Person {
  age: number;
}
// 合并为:interface Person { name: string age: number}

type User = {
  name: string;
};
type User = {
  age: number;
};
// 声明重复报错：标识符“User”重复。
```
4. type 可以获取 typeof 返回的值作为类型
```
let div = document.createElement("div");
type B = typeof div; // HTMLDivElement
```

## 泛型
泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。
```
//泛型函数的类型与非泛型函数的类型没什么不同，只是有一个类型参数在最前面，像函数声明一样
function createArray<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}

createArray<string>(3, 'x'); // ['x', 'x', 'x']

//泛型参数的默认类型
function createArray<T = string>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}
```
泛型接口
```
interface CreateArrayFunc {
    <T>(length: number, value: T): Array<T>;
}

let createArray: CreateArrayFunc;
createArray = function<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}

createArray(3, 'x'); // ['x', 'x', 'x']

//也可以把泛型参数提前到接口名上
interface CreateArrayFunc<T> {
    (length: number, value: T): Array<T>;
}

let createArray: CreateArrayFunc<any>;
createArray = function<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}

createArray(3, 'x'); // ['x', 'x', 'x']
```
泛型类
```
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };
```
泛型约束
```
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);
    return arg;
}
//对泛型进行约束，只允许这个函数传入那些包含 length 属性的变量
```
定义一个接口来描述约束条件。 创建一个包含 .length属性的接口，使用这个接口和extends关键字来实现约束。

## Partial (可选属性，但仍然不允许添加接口中没有的属性)

ts中就是让一个定义中的所有属性都变成可选参数，参数可以变多也可以少。

我们定义 一个user 接口，如下

```
interface IUser {
  name: string
  age: number
  department: string
}
 
//经过 Partial 类型转化后得到
 
type optional = Partial<IUser>
 
// optional的结果如下
 
type optional = {
    name?: string | undefined;
    age?: number | undefined;
    department?: string | undefined;
}
```
当我们不能明确地确定对象中包含哪些属性时，我们就可以通过Partial来声明。
## Required（必选属性）
和Partial刚好相反，将一个定义中的属性全部变成必选参数

让一个类型的属性全部必填

## Pick（部分选择）
可能需要从一个已声明的类型中抽取出一个子类型，在子类型中包含父类型中的部分或全部属性，这时我们可以使用Pick来实现，

ts中可以选择一个原来的接口中一部分的属性定义
```
interface User {
    id: number;
    name: string;
    age: number;
    gender: number;
    email: string;
}
 
type PickUser = Pick<User, "id" | "name" | "gender">;
 
// 等价于
type PickUser = {
    id: number;
    name: string;
    gender: number;
};
 
let user: PickUser = {
    id: 1,
    name: 'tom',
    gender: 1
};

// 等价于
type PickUser = {
    id: number;
    name: string;
    gender: number;
};
 
let user: PickUser = {
    id: 1,
    name: 'tom',
    gender: 1
};
```
## Omit（属性忽略） 
与Pick相反，Pick用于拣选出我们需要关心的属性，而Omit用于忽略掉我们不需要关心的属性

```
interface User {
    id: number;
    name: string;
    age: number;
    gender: number;
    email: string;
}
 
// 表示忽略掉User接口中的age和email属性
type OmitUser = Omit<User, "age" | "email">;
// 等价于
type OmitUser = {
  id: number;
  name: string;
  gender: number;
};
 
let user: OmitUser = {
    id: 1,
    name: 'tom',
    gender: 1
};
```

## Readonly （只读属性）
如果要求对象中的一些字段只能在创建的时候被赋值，使用 readonly 定义只读属性(只读的约束存在于第一次给对象赋值的时候，而不是第一次给只读属性赋值的时候)
```
interface Person {
    readonly id: number;
    name: string;
    age?: number;
    [propName: string]: any;
}
let tom: Person = {
    id: 89757,
    name: 'Tom',
    gender: 'male'
};
tom.id = 9527;      //  Cannot assign to 'id' because it is a constant or a read-only property
```


