---
title: 小白初探Webpack
date: 2021-02-16 00:36:42
tags: webpack
---

本质上,webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时,它会递归地构建一个依赖关系图(dependency graph),其中包含应用程序需要的每个模块,然后将所有这些模块打包成一个或多个 bundle。简单讲，Webpack是一个根据依赖关系来进行构建的工具，而gulp、grunt是一种流水线型的打包工具。

#### webpack的优势

##### 1、代码拆分（支持异步模块加载）

> Webpack 有两种组织模块依赖的方式，同步(默认)和异步（高级）。异步依赖作为分割点，形成一个新的块。在优化了依赖树后，每一个异步区块都作为一个文件被打包。

##### 2、Loader（支持任意模块加载，比如图片、less、css等等）

> Webpack 本身只能处理原生的 JavaScript 模块，但是 loader 转换器可以将各种类型的资源转换成 JavaScript 模块。这样，任何资源都可以成为 Webpack 可以处理的模块。

##### 3、智能解析

> Webpack 有一个智能解析器，几乎可以处理任何第三方库，无论它们的模块形式是 CommonJS、 AMD 还是普通的 JS 文件。甚至在加载依赖的时候，允许使用动态表达式 require("./templates/" + name + ".jade")。

##### 4、插件系统

> Webpack 还有一个功能丰富的插件系统。大多数内容功能都是基于这个插件系统运行的，还可以开发和使用开源的 Webpack 插件，来满足各式各样的需求。

##### 5、快速运行

> Webpack 使用异步 I/O （NodeJs）和多级“缓存”提高运行效率，这使得 Webpack 能够以令人难以置信的速度快速增量编译。

#### webpack基础概念

##### Entry

__入口起点(entry point)__指示 webpack 应该使用哪个模块，来作为构建其内部 [依赖图(dependency graph)](https://webpack.docschina.org/concepts/dependency-graph/) 的开始。进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。

##### Output

**output** 属性告诉 webpack 在哪里输出它所创建的 *bundle*，以及如何命名这些文件。主要输出文件的默认值是 `./dist/main.js`，其他生成文件默认放置在 `./dist` 文件夹中。

你可以通过在配置中指定一个 `output` 字段，来配置这些处理过程

##### Loader

webpack 只能理解 JavaScript 和 JSON 文件，这是 webpack 开箱可用的自带能力。**loader** 让 webpack 能够去处理其他类型的文件，并将它们转换为有效 [模块](https://webpack.docschina.org/concepts/modules)，以供应用程序使用，以及被添加到依赖图中。

##### Plugin

loader 用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。包括：打包优化，资源管理，注入环境变量。

#### webpack打包原理

Webpack 的运行流程是一个串行的过程,从启动到结束会依次执行以下流程 :

1. 初始化参数：从配置文件和 Shell 语句中读取与合并参数,得出最终的参数。
2. 开始编译：用上一步得到的参数初始化 Compiler 对象,加载所有配置的插件,执行对象的 run 方法开始执行编译。
3. 确定入口：根据配置中的 entry 找出所有的入口文件。
4. 编译模块：从入口文件出发,调用所有配置的 Loader 对模块进行翻译,再找出该模块依赖的模块,再递归本步骤直到所有入口依赖的文件都经过了本步骤的处理。
5. 完成模块编译：在经过第 4 步使用 Loader 翻译完所有模块后,得到了每个模块被翻译后的最终内容以及它们之间的依赖关系。
6. 输出资源：根据入口和模块之间的依赖关系,组装成一个个包含多个模块的 Chunk,再把每个 Chunk 转换成一个单独的文件加入到输出列表,这步是可以修改输出内容的最后机会。
7. 输出完成：在确定好输出内容后,根据配置确定输出的路径和文件名,把文件内容写入到文件系统。

在以上过程中,Webpack 会在特定的时间点广播出特定的事件,插件在监听到感兴趣的事件后会执行特定的逻辑,并且插件可以调用 Webpack 提供的 API 改变 Webpack 的运行结果。

#### Webpack配置

##### webpack安装

npm install -g webpack

##### 初始化package.json文件

npm init

##### webpack的使用

webpack {entry file} {destination file}

##### webpack.config.js

```
module.exports = {
	entry: __dirname +"/app/main.js",
	output: {
		path: __dirname +"/public",
		filename: "bundle.js"
	}
}
```

在有配置文件之后可以使用webpack指令（非全局使用node_modules/.bin/webpack）

##### webpack常用的loader

- 样式：style-loader、css-loader、less-loader、sass-loader等
- 文件：raw-loader、file-loader 、url-loader等
- 编译：babel-loader、coffee-loader 、ts-loader等
- 校验测试：mocha-loader、jshint-loader 、eslint-loader等

##### webpack常用的plugin

- 首先webpack内置`UglifyJsPlugin`，压缩和混淆代码。
- webpack内置`CommonsChunkPlugin`，提高打包效率，将第三方库和业务代码分开打包。
- `html-webpack-plugin`可以根据模板自动生成html代码，并自动引用css和js文件
- `extract-text-webpack-plugin`将js文件中引用的样式单独抽离成css文件
- `DefinePlugin`编译时配置全局变量，这对开发模式和发布模式的构建允许不同的行为非常有用。
- `HotModuleReplacementPlugin` 热更新
- `optimize-css-assets-webpack-plugin` 不同组件中重复的css可以快速去重
- `compression-webpack-plugin` 生产环境可采用gzip压缩JS和CSS

另外介绍一下webpack的热更新：

热更新就是 **在应用程序的开发环境，方便开发人员在不刷新页面的情况下，就能修改代码，并且直观地在页面上看到变化的机制**。

##### 热更新原理

- 使用`express`启动本地服务，当浏览器访问资源时对此做响应。
- 服务端和客户端使用`websocket`实现长连接
- `webpack`监听源文件的变化，即当开发者保存文件时触发`webpack`的重新编译。
  - 每次编译都会生成`hash值`、`已改动模块的json文件`、`已改动模块代码的js文件`
  - 编译完成后通过`socket`向客户端推送当前编译的`hash戳`
- 客户端的`websocket`监听到有文件改动推送过来的`hash戳`，会和上一次对比
  - 一致则走缓存
  - 不一致则通过`ajax`和`jsonp`向服务端获取最新资源
- 使用`内存文件系统`去替换有修改的内容实现局部刷新

使用方法如下：

```
//HotModuleReplacementPlugin();
module.exports = {
  // ...
  devServer: {
    hot: true,
    // ...
  }
}
```

