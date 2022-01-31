# CSS-Modules

### 为什么要使用CSS模块化？

因为在开发过程中，CSS方面往往会遇到这些问题：

- 全局污染（因为CSS没有作用域）

在CSS中使用的是全局选择器机制来设置样式，优点是方便重写样式。缺点是所有的样式都是全局生效，样式可能被错误覆盖，因此产生了非常丑陋的 `!important`，甚至 inline `!important` 和复杂的[选择器权重计数表](https://www.w3.org/TR/selectors/#specificity)，如此提高犯错概率和使用成本。而Web Components 标准中的 Shadow DOM 能彻底解决这个问题，实现了样式隔离。但它的做法有点极端，样式彻底局部化，造成外部无法重写样式，损失了灵活性。

- 命名混乱

在多人协同开发时为了避免样式冲突，选择器越来越复杂，很容易形成不同的命名风格，很难统一。样式变多后，命名将更加混乱，将导致在项目中css命名维护困难的问题。虽然可以使用不同的命名风格去规范命名问题比如bem，但是是通过人工去命名的，说白懒是驱动程序猿的第一生产力...

- 依赖引入复杂

依赖管理不彻底，组件应该相互独立，引入一个组件时，应该只引入它所需要的 CSS 样式。但现在的做法是除了要引入 JS，还要再引入它的 CSS，而且 Saas/Less 很难实现对每个组件都编译出单独的 CSS，引入所有模块的 CSS 又造成浪费。JS 的模块化已经非常成熟，如果能让 JS 来管理 CSS 依赖是很好的解决办法。Webpack 的 `css-loader` 提供了这种能力。

采用模块化的方法主要解决了CSS 样式的导入和导出问题，灵活按需导入以便复用代码；导出时要能够隐藏内部作用域，以免造成全局污染。总结下主要有以下几点优点：

- 提高开发效率、减少沟通成本
- 降低耦合，提高页面容错
- 减少Bug定位时间和Fix成本，降低发布风险
- 便于代码维护，更好的实现快速迭代

CSS 模块化的解决方案有很多，但主要有两类。

一类是彻底抛弃 CSS，使用 JS 或 JSON 来写样式。优点是能给 CSS 提供 JS 同样强大的模块化能力；缺点是不能利用成熟的 CSS 预处理器（或后处理器） Sass/Less/PostCSS，`:hover` 和 `:active` 伪类处理起来复杂。

另一类是依旧使用 CSS，但使用 JS 来管理样式依赖，代表是 [CSS Modules](https://github.com/css-modules/css-modules)。CSS Modules 能最大化地结合现有 CSS 生态和 JS 模块化能力，API 简洁到几乎零学习成本。发布时依旧编译出单独的 JS 和 CSS。它并不依赖于 React，只要你使用 Webpack，可以在 Vue/Angular/jQuery 中使用。

CSS Modules 的命名规范是从 BEM 扩展而来。BEM 把样式名分为 3 个级别，分别是：

- Block：对应模块名，如 Dialog
- Element：对应模块中的节点名 Confirm Button
- Modifier：对应节点相关的状态，如 disabled、highlight

综上，BEM 最终得到的 class 名为 `dialog__confirm-button--highlight`。使用双符号 `__` 和 `--` 是为了和区块内单词间的分隔符区分开来。虽然看起来有点奇怪，但 BEM 被非常多的大型项目和团队采用。我们实践下来也很认可这种命名方法。

CSS Modules 中 CSS 文件名恰好对应 Block 名，只需要再考虑 Element 和 Modifier。BEM 对应到 CSS Modules 的做法是：

```javascript
/* .dialog.css */
.ConfirmButton--disabled {
}
```

你也可以不遵循完整的命名规范，使用 camelCase 的写法把 Block 和 Modifier 放到一起：

```javascript
/* .dialog.css */
.disabledConfirmButton {
}
```

本文主要将使用webpack实现的css模块化：

### 启用 CSS Modules

```javascript
// webpack.config.js
css?modules&localIdentName=[name]__[local]-[hash:base64:5]
```

在cssloader的配置上，加上 `modules` 即为启用，`localIdentName` 是设置生成样式的命名规则。

然后创建一个文件名为：demo.module.css的文件（其中.module.css结尾很重要）

```css
.test {
    width: 100px
}
```

然后可以写一个jsx文件

```jsx
// 整体引入
import styles from './demo.module.less'
// 按需引入
import  { test } from './demo.module.less'

function A () {
    return (
        <div classNames={styles.test}>
        adasdsa
        </div>
    )
}
```

生成的html中的class会按照localIdentName设置的命名规则生产样式名。

- css-loader提供的`localIdentName`参数有`path`、`name`、`local`和`hash`，
- path: 当前css、less或sass的文件路径
- name: 引入的模块名
- local: 局部作用域的类名
- hash:独一无二的hash值

经过这样混淆处理后，class 名基本就是唯一的，大大降低了项目中样式覆盖的几率。同时在生产环境下修改规则，生成更短的 class 名，可以提高 CSS 的压缩率。

其中CSS Modules 对 CSS 中的 class 名都做了处理，使用对象来保存原 class 和混淆后 class 的对应关系。

#### 样式默认局部

使用了 CSS Modules 后，就相当于给每个 class 名外加加了一个 `:local`，以此来实现样式的局部化，如果你想切换到全局模式，使用对应的 `:global`。

```javascript
.normal {
  color: green;
}

/* 以上与下面等价 */
:local(.normal) {
  color: green; 
}

/* 定义全局样式 */
:global(.btn) {
  color: red;
}

/* 定义多个全局样式 */
:global {
  .link {
    color: green;
  }
  .box {
    color: yellow;
  }
}
```

#### 与less和sass配合使用

与less和sass配合使用的方法是一样的。
首先安装相关依赖less-loader、sass-loader和node-sass等，并修改webpack配置：

```
{
   test: /\.less$/,
   loader: 'style-loader!css-loader?modules!less-loader'
},
{
   test: /\.scss$/,
   loader: 'style-loader!css-loader?modules!sass-loader'
},
```

本文参照了：

http://www.cnblogs.com/WebShare-hilda/p/4686067.html

https://segmentfault.com/a/1190000004300065

https://cloud.tencent.com/developer/article/1036799

https://blog.csdn.net/qq_26733915/article/details/54313492
