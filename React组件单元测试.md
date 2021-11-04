### 什么是单元测试？

> 测试是一种**验证**我们的代码是否可以按预期工作的手段。

被测试的对象可以是我们程序的任何一个组成部分。大到一个分为多步骤的下单流程，小到代码中的一个函数。

单元测试特指被测试对象为程序中**最小组成单元**的测试。这里的最小组成单元可以是一个函数、一个类等等。

### 单元测试的优势

由于被测试对象的简单（通常只有一个或多个输入以及一个输出），这就决定了单元测试**开发起来也很简单**，通常每个测试只有几行到十几行不等。测试代码的简单表示它可以被**更频繁的执行**（**事实上，很多单元测试框架都有 watch 模式。每次改动代码时都会自动执行单元测试**）。更频繁的执行意味着**更早的发现问题**。

试想，随着代码的不断迭代，程序中总有某些位置会频繁出现某类问题。在没有单元测试时程序员之间往往都是“口口相传”，隔一段时间很可能由于疏忽还会犯同一个错误。有了单元测试我们就可以为这些问题点编写对应的测试代码，每次提交代码前都执行一遍，可以极大的降低相同 bug 重复出现的概率。

此外，要为一个被测试对象编写单元测试，那么它应该首先是容易被测试的（这似乎是一句废话）。反过来讲，如果你面对一个函数、类却很难编写测试代码的时候，很可能是你的代码设计上存在问题。比如和外部依赖耦合过于紧密。这种情况下，编写单元测试的过程会倒逼我们优化我们代码的结构。将复杂的代码拆解成为更简单、更容易测试的片段。这个过程本身也会**潜移默化的提高我们代码的质量**。

### 单元测试的限制/不足

> I get paid for code that works, not for tests - [Kent Beck](https://stackoverflow.com/a/153565/1513552)

首先，测试代码再简单，也是需要工作量来开发的。必定占用开发人员的时间。因此需要开发人员在投入与收益之间找到一个最佳的平衡点。

其次，单元测试覆盖率往往会给开发人员一种错觉：这段代码的单元测试都通过了（测试覆盖率以及 100% 了），肯定没有 bug。其实不然，单元测试覆盖率与代码质量没有必然的联系。作为开发人员必须尽早认识到这一点。

### 何时编写单元测试？

- 开发过程中，单元测试应该来测试那些可能会出错的地方，或是那些边界情况。
- 维护过程中，单元测试应该围绕着 bug 进行，每个 bug 都应该编写响应的单元测试。从而保证同一个 bug 不会出现第二次。

### 单元测试中的基本概念？

单元测试一般包含以下几个部分：

- 被测试的对象是什么
- 要测试该对象的什么功能
- 实际得到的结果
- 期望的结果
- mock / spy （下文会详述）

具体到某个单元测试，往往包含以下几个步骤：

- 准备阶段：构造参数，创建 spy 等
- 执行阶段：用构造好的参数执行被测试代码
- 断言阶段：用实际得到的结果与期望的结果比较，以判断该测试是否正常
- 清理阶段：清理准备阶段对外部环境的影响，移除在准备阶段创建的 spy 等

## Jest 简介

[Jest](https://facebook.github.io/jest/) 是 Facebook 开发的一款 JavaScript 测试框架。在 Facebook 内部广泛用来测试各种 JavaScript 代码。其官网上主要列出了以下几个特点：

- 轻松上手
  - 使用 [`create-react-app`](https://facebook.github.io/react/blog/2016/07/22/create-apps-with-no-configuration.html) 或是 [`react-native init`](http://facebook.github.io/react-native/docs/getting-started.html) 创建的项目已经默认集成了 Jest
  - 现有项目，只需创建一个名为 `__test__` 的目录，然后在该目录中创建以 `.spec.js` 或 `.test.js` 结尾的文件即可
- 内置强大的断言与 mock 功能
- 内置测试覆盖率统计功能
- 内置 Snapshot 机制

虽然 Jest 官网介绍中多次 React，但实际上 Jest 并不是和 React 绑定的。你可以使用它测试任何 JavaScript 项目。

## Jest 基础功能介绍

安装:

```bash
npm install --save-dev jest
```

然后配置 `package.json` ：

```json
"scripts": {
  "test": "jest --color"
}
```

接着创建一个名为 `__tests__` 的目录。jest 会自动去该目录下寻找要执行的测试代码。

接下来让我们编写一个最简单测试。

```js
describe("Addition", () => {
  it("knows that 2 and 2 make 4", () => {
    const val1 = 2;
    const val2 = 2;
    const result = val1 + val2;
    const expectedResult = 4;
    expect(result).toBe(expectedResult);
  });
});
```

接下来让我们看看这个单元测试是否满足了我们前文提到的元素与步骤。

元素：

- 被测试的对象是什么: `+` 运算符
- 要测试该对象的什么功能： 2 + 2 = 4
- 实际得到的结果：`result`
- 期望的结果: `expectedResult`

步骤：

- 准备阶段：line3, line4
- 执行阶段：line5
- 断言阶段：line7
- 清理阶段：无

可以看出，单元测试的编写是有“套路”可循的。实际中，我们一般不会创建这么多临时变量，可以简写成：

```js
describe("Addition", () => {
  it("knows that 2 and 2 make 4", () => {
    expect(2 + 2).toBe(4);
  });
});
```

[`toBe`](https://facebook.github.io/jest/docs/en/expect.html#tobevalue) 只是 Jest 强大断言功能中的一个方法。你可以在[这里](https://facebook.github.io/jest/docs/en/expect.html#methods)找到 Jest 支持的全部断言语法的详细文档。

现在让我们来执行一下刚刚编写的测试代码吧：

![img](http://img20.360buyimg.com/uba/jfs/t20962/91/128655922/11420/5ebc57b2/5afd67eeNb798e080.png)

### Jest 中的 mock 与 spy

让我们来通过例子了解 mock 与 spy。

假设有下面这个函数：

```js
function forEach(items, callback) {
  for (let index = 0; index < items.length; index++) {
    callback(items[index]);
  }
}
```

功能很简单，循环第一个参数 `items`，并把数组中的每一项作为参数调用第二个参数 `callback`。该如何测试呢？

我们要创建一个特殊的 callback 函数，它可以记录每次调用时传入的参数供我们进行断言。

下面是一段示例代码：

```js
describe("forEach", () => {
  it("should call callback with each item", () => {
    const callHistory = [];
    const specialCallback = (...args) => callHistory.push(args);
    forEach([1, 2], specialCallback);

    expect(callHistory.length).toBe(2);
    expect(callHistory[0][0]).toBe(1);
    expect(callHistory[1][0]).toBe(2);
  });
});
```

这里的 `specialCallback` 就是一个 mock。它存在的意义就是统计函数被调用的信息供我们使用。这种模式在单元测试中经常被使用，所以 Jest 已经内置了对 mock 的支持。让我们来看看如何使用：

```js
describe("forEach", () => {
  it("should call callback with each item", () => {
    const mockFn = jest.fn();
    forEach([1, 2], mockFn);

    expect(mockFn.mock.calls.length).toBe(2);
    expect(mockFn.mock.calls[0][0]).toBe(1);
    expect(mockFn.mock.calls[1][0]).toBe(2);
  });
});
```

很方便吧，只需要 `jest.fn()` 一下就可以得到一个功能强大的 mock 函数。Jest 中的 mock 远不止是统计一下调用参数那么简单，更多强大用法可以参考[这里](https://facebook.github.io/jest/docs/en/mock-functions.html)和[这里](https://facebook.github.io/jest/docs/en/mock-function-api.html)。

最后再来说一下 spy。其实 spy 和 mock 是非常类似的，唯一的区别点在于，spy 用于监听一个现有对象上的方法。

还是通过一个例子来看，假设我们有对象：

```js
const bot = {
  sayHello: name => {
    console.log(`Hello ${name}!`);
  }
};
```

我们可以像下面这样创建并使用 spy：

```js
describe("bot", () => {
  it("should say hello", () => {
    const spy = jest.spyOn(bot, "sayHello");

    bot.sayHello("Michael");

    expect(spy).toHaveBeenCalledWith("Michael");

    spy.mockRestore();
  });
});
```

我们通过 `jest.spyOn` 创建了一个监听 `bot` 对象的 `sayHello` 方法的 spy。它就像间谍一样监听了所有对 `bot#sayHello` 方法的调用。由于创建 spy 时，Jest 实际上修改了 `bot` 对象的 `sayHello` 属性，所以在断言完成后，我们还要通过 `mockRestore` 来恢复 `bot` 对象原本的 `sayHello` 方法。

[这里](https://facebook.github.io/jest/docs/en/jest-object.html#jestspyonobject-methodname)可以找到关于 Jest#spyOn 更详细的介绍。

## 实战：使用 Jest 编写一个完整的单元测试

到这里，单元测试的套路和 Jest 的基本用法已经介绍的差不多了。让我们最后通过一个完整的示例来结束今天的讨论。

被测试的函数名为 `getImageDomain`。主要功能就是为某个 skuId 选取一个图片服务器域名，如果未传入 skuId，则随机返回一个域名：

```js
const domains = [
  "img10.360buyimg.com",
  "img11.360buyimg.com",
  "img12.360buyimg.com",
  "img13.360buyimg.com",
  "img14.360buyimg.com"
];

const getImageDomain = skuId => {
  if (skuId) {
    return domains[skuId % 5];
  } else {
    return domains[Math.floor(Math.random() * 5)];
  }
};
```

对应的测试代码如下，由于逻辑比较简单，故不再详细分析：

```js
describe("getImageDomain", () => {
  it("should select domain based on skuId if provided", () => {
    expect(getImageDomain(1)).toBe("img11.360buyimg.com");
  });

  it("should select a random domain based on Math.random if skuId not available", () => {
    const spy = jest.spyOn(Math, "random").mockImplementation(() => 0.9);

    expect(getImageDomain()).toBe("img14.360buyimg.com");
    expect(spy).toHaveBeenCalled();

    spy.mockRestore();
  });
});
```

## 写在最后

测试只是一种手段，而不是目的。

> 软件的质量不是测试出来的，而是设计和维护出来的。

### 延伸阅读

- [Unit testing - Wikipedia](https://en.wikipedia.org/wiki/Unit_testing)
- [“单元测试要做多细？”](https://coolshell.cn/articles/8209.html)
- [【译】每个单元测试必须回答的 5 个问题](https://75team.com/post/5-questions-every-unit-test-must-answer.html)