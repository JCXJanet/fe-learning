## CSS盒子模型

w3c盒模型：width不包含border和padding

IE盒模型：width包含border和padding

可以通过css去设置盒模型，通过box-sizing去设置，content-box为w3c盒模型，border-box为IE盒模型。

## scoped

### Vue 样式属性 scoped 原理详解

##### （一）什么是 scoped

当 style 标签有 scoped 属性时，它的 CSS 只作用于当前组件中的元素。通过该属性，可以使组件之间的样式互不污染，实现样式的模块化。

##### （二）scoped 原理

主要通过使用 PostCSS 来实现以下转换：

```
<style scoped>
    .example {
          color: red;
    }
</style>

<template>
      <div class="example">hi</div>
</template>
```

转换结果：

```
<style>
    .example[data-v-7668812d] {
          color: red;
    }
</style>

<template>
      <div class="example" data-v-7668812d>hi</div>
</template>
```

通过给 dom 增加一个动态属性，然后 css 选择器也额外添加对应的属性来选择该 dom ，达到该样式只作用于含有该属性的 dom，实现组件样式的模块化。

### 缺陷

如果你子组件的根元素上有一个类已经在这个父组件中定义过了，那么这个父组件的样式就会**泄露到子组件中**
我们在父元素中定义wrapper类边框为蓝色，在子组件中定义同一个名字的类wrapper类边框为橙色，结果会导致子组件中既有父元素的蓝色边框又有自己的橙色边框。

因此，我们要避免在父组件中书写和子组件同名的css类。

## **px、em、rem、vw的区别**

px：px就是pixel的缩写,pixel即像素,它不是自然界的长度单位。px是就是一张图片中最小的点,一张位图就是由这些点构成的。

像素px是相对于显示器屏幕分辨率而言的。

vw：视窗宽度的百分比（1vw 代表视窗的宽度为 1%）

vh：视窗高度的百分比

rem: 大小继承根元素的大小； 

rem是CSS3新增的一个相对单位，这个单位与em有什么区别呢？区别在于使用rem为元素设定字体大小时，仍然是相对大小，但相对的只是HTML根元素。

em：相对于当前对象内文本的字体尺寸。如当前对行内文本的字体尺寸未被人为设置，则相对于浏览器的默认字体尺寸。

**em特点**

- em的值并不是固定的；
- em会继承父级元素的字体大小。

## min-width、max-width、width的包含(优先级关系)关系

MDN说法：
max-height 这个属性会阻止 height 属性的设置值变得比 max-height 更大。
max-height 属性用来设置给定元素的最大高度. 如果height 属性设置的高度比该属性设置的高度还大,则height 属性会失效.
max-height 重载（覆盖掉） height, 但是 min-height 又会重载（覆盖掉） max-height.

实际效果：
**当 height 和 max-height一起使用时，谁小听谁的**
　　max-height < height 元素高度： max-height
　 　height < max-height 元素高度： height

当 height，max-height，min-height一起使用时
　　height > max-height > min-height 元素高度：max-height
　　**height > min-height > max-height 元素高度：min-height（虽然这里的height没有小于min-height，但是它大于了max-height，因此导致height失效；而max-height又小于min-height，导致max-height失效，因此最后对元素高度起作用的就只是min-height）**
　　min-height > height > max-height 元素高度：min-height

若同时设置了width和max-width两个属性，则以下面**规则**显示宽度：

（1）当width< max-width时，元素宽度以width为准
（2）若width>=max-width则以max-width为准。

若同时设置了width和min-width两个属性，则以下面**规则**显示宽度：
 （1）当width< min-width时，元素宽度以min-width为准
 （2）若width>=min-width则以width为准。

#### min-width不生效的问题

会发现min-width并没有生效。
问题原因是：
**min-width会继承父元素的width，而min-height不会。
如果无父元素，也就是最外层是body，则默认100%。**

**解决方法：浮动，定位，使之脱离文档流
(1) position: absolute：**

```css
  .test{
        min-width: 100px;
        height: 60px;
        background-color: #E5B783;
        position: absolute;
        top: 0;
        left: 0;
    }
```

**(2) position: fixed：**

```css
  .test{
        min-width: 100px;
        height: 60px;
        background-color: #E5B783;
        position: fixed;
        top: 0;
        left: 0;
    }
```

**(3) float进行元素浮动：**

```css
    .test{
        min-width: 100px;
        height: 60px;
        background-color: #E5B783;
        float: left;
    }
```

## display的属性

| 值                  | 描述                                         |
|:------------------ |:------------------------------------------ |
| none               | 此元素不会被显示。                                  |
| block              | 此元素将显示为块级元素，此元素前后会带有换行符。                   |
| inline             | 默认。此元素会被显示为内联元素，元素前后没有换行符。                 |
| inline-block       | 行内块元素。（CSS2.1 新增的值）                        |
| list-item          | 此元素会作为列表显示。                                |
| run-in             | 此元素会根据上下文作为块级元素或内联元素显示。                    |
| compact            | CSS 中有值 compact，不过由于缺乏广泛支持，已经从 CSS2.1 中删除。 |
| marker             | CSS 中有值 marker，不过由于缺乏广泛支持，已经从 CSS2.1 中删除。  |
| table              | 此元素会作为块级表格来显示（类似 <table>），表格前后带有换行符。       |
| inline-table       | 此元素会作为内联表格来显示（类似 <table>），表格前后没有换行符。       |
| table-row-group    | 此元素会作为一个或多个行的分组来显示（类似 <tbody>）。            |
| table-header-group | 此元素会作为一个或多个行的分组来显示（类似 <thead>）。            |
| table-footer-group | 此元素会作为一个或多个行的分组来显示（类似 <tfoot>）。            |
| table-row          | 此元素会作为一个表格行显示（类似 <tr>）。                    |
| table-column-group | 此元素会作为一个或多个列的分组来显示（类似 <colgroup>）。         |
| table-column       | 此元素会作为一个单元格列显示（类似 <col>）                   |
| table-cell         | 此元素会作为一个表格单元格显示（类似 <td> 和 <th>）            |
| table-caption      | 此元素会作为一个表格标题显示（类似 <caption>）               |
| inherit            | 规定应该从父元素继承 display 属性的值。                   |

### css的display属性的三个属性值block,inline和inline-block有什么区别？

#### 基本概念

block和inline这两个概念是简略的说法，完整确切的说应该是 block-level elements (块级元素) 和 inline elements (内联元素)。block元素通常被现实为独立的一块，会单独换一行；inline元素则前后不会产生换行，一系列inline元素都在一行内显示，直到该行排满。
block元素可以包含block元素和inline元素；但inline元素只能包含inline元素。要注意的是这个是个大概的说法，每个特定的元素能包含的元素也是特定的，所以具体到个别元素上，这条规律是不适用的。比如 P 元素，只能包含inline元素，而不能包含block元素。
一般来说，可以通过display:inline和display:block的设置，改变元素的布局级别。

常见的块级元素有 DIV, FORM, TABLE, P, PRE, H1~H6, DL, OL, UL 等。
常见的内联元素有 SPAN, A, STRONG, EM, LABEL, INPUT, SELECT, TEXTAREA, IMG, BR 等。

#### block，inline和inlinke-block细节对比

##### display：block

block元素会独占一行，多个block元素会各自新起一行。默认情况下，block元素宽度自动填满其父元素宽度。
block元素可以设置width,height属性。块级元素即使设置了宽度,仍然是独占一行。
block元素可以设置margin和padding属性。

##### display：inline

inline元素不会独占一行，多个相邻的行内元素会排列在同一行里，直到一行排列不下，才会新换一行，其宽度随元素的内容而变化。
inline元素设置width,height属性无效。
inline元素的margin和padding属性，水平方向的 margin-left, margin-right 会产生边距效果；但竖直方向的 margin-top, margin-bottom 不会产生边距效果。padding 属性会产生边距效果。

##### display：inline-block

简单来说就是将对象呈现为inline对象，但是对象的内容作为block对象呈现。之后的内联对象会被排列在同一行内。比如我们可以给一个link（a元素）inline-block属性值，使其既具有block的宽度高度特性又具有inline的同行特性。

## **伪类、伪元素：**

- css引入伪类和伪元素概念是为了格式化文档树以外的信息。也就是说，伪类和伪元素是用来修饰不在文档树中的部分；比如，一句话中的第一个字母，或者是列表中的第一个元素。下面分别对伪类和伪元素进行解释：

- 伪类用于当已有元素处于的某个状态时，为其添加对应的样式，这个状态是根据用户行为而动态变化的。比如说，当用户悬停在指定的元素时，我们可以通过:hover来描述这个元素的状态。虽然它和普通的css类相似，可以为已有的元素添加样式，但是它只有处于dom树无法描述的状态下才能为元素添加样式，所以将其称为伪类。

- 伪元素用于创建一些不在文档树中的元素，并为其添加样式。比如说，我们可以通过:before来在一个元素前增加一些文本，并为这些文本添加样式。虽然用户可以看到这些文本，但是这些文本实际上不在文档树中。

##### 伪元素&伪类的特点：

- 伪元素和伪类都不会出现在源文档或者文档树中
- 伪类允许出现在选择器的任何位置，而一个伪元素只能跟在选择器的最后一个简单选择器后面
- 伪元素名和伪类名都是大小写不敏感的
- 有些伪类是互斥的，而其它的可以同时用在一个元素上。（在规则冲突的情况下，常规[层叠顺序](https://links.jianshu.com/go?to=http%3A%2F%2Fwww.ayqy.net%2Fdoc%2Fcss2-1%2Fcascade.html%23cascading-order)决定结果）。

##### 伪元素和伪类的区别：

- 伪类的操作对象是文档树中已有的元素，而伪元素则创建了一个文档数外的元素。因此，伪类与伪元素的区别在于：有没有创建一个文档树之外的元素。

### 伪元素：

- 伪元素用于创建一些不在文档树中的元素并为其添加样式。

![](https://upload-images.jianshu.io/upload_images/14333787-dac5cb3f7528f34e.png?imageMogr2/auto-orient/strip|imageView2/2/w/616/format/webp)

##### 单双冒号：

1. E::before/E:before在E元素内部创建一个行内元素，作为E的第一个孩子,需要使用content属性来指定要插入的内容。被插入的内容实际上不在文档树中;
2. E::after/E:after 在E元素内部创建一个行内元素，作为E的最后一个孩子,其用法和特性与:before相似;
3. E::first-letter/E:first-letter匹配E元素内容的第一个字母,被修饰的首字母不在文档树中;
4. E::first-lineE/:first-line 匹配E元素内容的第一行,这个伪元素只能用在块元素中，不能用在内联元素中

##### 双冒号：

1. E::selection应用于文档中被用户高亮的部分（比如使用鼠标或其他选择设备选中的部分）改变字体大小，添加背景色等等,在火狐浏览器使用时需要添加-moz前缀。该伪元素只支持双冒号的形式；

```css
::-moz-selection {
    color: orange;
    background: #333;
}
::selection  {
    color: orange;
    background: #333;
}
```

2. E::placeholder匹配占位符的文本，只有元素设置了placeholder属性时，该伪元素才能生效;用于修改input默认的灰色文字的样式，字体大小和字体颜色等等；为了浏览器兼容性，一般写为： input::-webkit-input-placeholder,  
   该伪元素不是CSS的标准，它的实现可能在将来会有所改变，所以要决定使用时必须谨慎。在一些浏览器中（IE10和Firefox18及其以下版本）会使用单冒号的形式；

```bash
<input type="email" placeholder="name@domain.com">
```

```css
input::-moz-placeholder {
    color:#666;
}
input::-webkit-input-placeholder {
    color:#666;
}
/* IE 10 only */
input:-ms-input-placeholder {
    color:#666;
}

/* Firefox 18 and below */
input:-moz-input-placeholder {
    color:#666;
}
```

3. E::backdorp是在任何处于全屏模式的元素下的即刻渲染的盒子（并且在所有其他在堆中的层级更低的元素之上）,用于改变全屏模式下的背景颜色，全屏模式的默认颜色为黑色。该伪元素只支持双冒号的形式,该伪类用得少，兼容性差，甚至许多浏览器并未使用该功能）

```xml
<h1 id="element">This heading will have a solid background color in full-screen mode.</h1>
<button onclick="var el = document.getElementById('element'); el.webkitRequestFullscreen();">Trigger full screen!</button>
```

```css
h1:fullscreen::backdrop {
    background: orange;
}
```

- CSS3规范中的要求使用双冒号(::)表示伪元素，以此来区分伪元素和伪类，比如::before和::after等伪元素使用双冒号(::)，:hover和:active等伪类使用单冒号(:)。除了一些低于IE8版本的浏览器外，大部分浏览器都支持伪元素的双冒号(::)表示方法。然而，除了少部分伪元素，如::backdrop必须使用双冒号，大部分伪元素都支持单冒号和双冒号的写法，比如::after，写成:after也可以正确运行；但是为了向后兼容，目前在考虑兼容性的情况下可以使用单冒号的写法，但是也建议全都使用双冒号；
- 使用::before和::after为了节省标签，其中content不可或缺但是可以是空字符；可以用于清除浮动，替换标签。
- 某些伪类或伪元素仍然处于试验阶段，在使用前建议先在[Can I Use](https://links.jianshu.com/go?to=http%3A%2F%2Fcaniuse.com%2F)等网站查一查其浏览器兼容性。

### 伪类：

- CSS 伪类是添加到选择器的关键字，指定要选择的元素的特殊状态。例如，:hover可被用于在用户将鼠标悬停在按钮上时改变按钮的颜色。

![](https://upload-images.jianshu.io/upload_images/14333787-0ae823b149ed21f1.png?imageMogr2/auto-orient/strip|imageView2/2/w/748/format/webp)

##### 状态:

1. :link用于选择未访问的链接；
2. :visited用于选择已访问的链接；
3. :hover用于选择鼠标指针浮动在其上的元素；
4. :active用于选择活动的链接；
5. :focus用于选择获取焦点的输入字段。

##### 结构化：

1. :not是一个否定伪类，用于匹配不符合参数选择器的元素。

```xml
<ul>
    <li class="first-item">一些文本</li>
    <li>一些文本</li>
    <li>一些文本</li>
    <li>一些文本</li>
</ul>
```

```css
li:not(.first-item) {
    color: orange;
}
```

- 上述列表除了第一个<li>元素外，其他<li>元素的文本都会变为橙色。
2. :first-child用于匹配元素的第一个子元素。
3. : last-child用于匹配元素的最后一个子元素。
4. ：first-of-type用于匹配属于其父元素的首个特定类型的子元素的每个元素。
5. :last-of-type用于匹配元素的最后一个子元素。
6. :nth-child根据元素的位置匹配一个或者多个元素，它接受一个an+b形式的参数，an+b匹配到的元素示例如下：
   - 1n+0，或n，匹配每一个子元素。
   - 2n+0，或2n，匹配位置为2、4、6、8…的子元素，该表达式与关键字even等价。
   - 2n+1匹配位置为1、3、5、7…的子元素、该表达式与关键字odd等价。
   - 3n+4匹配位置为4、7、10、13…的子元素。  
     如下例，有以下HTML列表：

```xml
<ol>
    <li>Alpha</li>
    <li>Beta</li>
    <li>Gamma</li>
    <li>Delta</li>
    <li>Epsilon</li>
    <li>Zeta</li>
    <li>Eta</li>
    <li>Theta</li>
    <li>Iota</li>
    <li>Kappa</li>
</ol>
```

CSS:  
选择第二个元素，”Beta”会变成橙色：

```css
ol :nth-child(2) {
    color: orange;
}
```

选择位置序号是2的倍数的元素，”Beta”, “Delta”, “Zeta”, “kappa”会变成橙色:

```css
ol :nth-child(2n) {
    color: orange;
}
```

选择位置序号为偶数的元素：

```css
ol :nth-child(even) {
    color: orange;
}
```

选择从第6个开始，位置序号是2的倍数的元素，”Zeta”, “Theta”, “Kappa”会变成橙色：

```css
ol :nth-child(2n+6) {
    color: orange;
}
```

7. :nth-last-child与:nth-child相似，不同之处在于它是从最后一个子元素开始计数的。
8. :nth-of-type与nth-child相似，不同之处在于它是只匹配特定类型的元素。  
   如下例，第二个<p>元素会变为橙色。

```jsx
HTML:

<article>
    <h1>我是标题</h1>
    <p>一些文本</p>
    <a href=""><img src="images/rwd.png" alt="Mastering RWD"></a>
    <p>这里的文本是橙色的</p>
</article>
```

```css
p:nth-of-type(2) {
    color: orange;
}
```

9. :nth-last-of-type与nth-of-type相似，不同之处在于它是从最后一个子元素开始计数的。
10. 当元素是其父元素中唯一一个子元素时，:only-child匹配该元素。

```xml
<ul>
    <li>这里的文本是橙色的</li>
</ul>
<ul>
    <li>一些文本</li>
    <li>一些文本</li>
</ul>
```

```css
ul :only-child {
    color: orange;
}
```

11. :only-of-type用于当元素是其父元素中唯一一个特定类型的子元素时，:only-child匹配该元素。  
    如下例，第一个ul元素只有一个li类型的元素，该li元素的文本会变为橙色。

```xml
<ul>
    <li>这里的文本是橙色的</li>
    <p>这里不是橙色</p>
</ul> 
<ul>
    <li>一些文本</li>
    <li>一些文本</li>
</ul>
```

```css
li:only-of-type {
    color: orange;
}
```

12. :target是当该页面的URL带有锚名称，指向文档内某个具体的元素时，:target匹配该元素。  
    如下例，url中的target命中id值为target的article元素，article元素的背景会变为黄色。

```ruby
URL:
http://example.com/#target
```

```xml
<article id="target">
    <h1><code>:target</code> pseudo-class</h1>
    <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit!</p>
</article>
```

```css
:target {
    background: yellow;
}
```

##### 表单相关：

1. :checked匹配被选中的input元素，这个input元素包括radio和checkbox。  
   如下例，当复选框被选中时，与其相邻的<label>元素的背景会变成黄色。

```xml
<input type="checkbox"/>
<label>我同意</label>
```

```css
input:checked + label {
    background: yellow;
}
```

2. :default匹配默认选中的元素，例如：提交按钮总是表单的默认按钮。  
   如下例，只有提交按钮的背景变成了黄色。

```xml
<form action="#">
    <button>重置</button>
    <button type="submit">提交</button>
</form>
```

```css
:default  {
    background: yellow;
}
```

3. :disabled匹配禁用的表单元素。

如下例，被禁用input输入框的透明度会变成50%。

```bash
<input type="text" disabled/>
```

```css
:disabled {
    opacity: .5;
}
```

4. :empty匹配没有子元素的元素。如果元素中含有文本节点、HTML元素或者一个空格，则:empty不能匹配这个元素。  
   如下例，:empty能匹配的元素会变为黄色。  
   第一个元素中有文本节点，所以其背景不会变成黄色；  
   第二个元素中有一个空格，有空格则该元素不为空，所以其背景不会变成黄色；  
   第三个元素中没有任何内容，所以其背景会变成黄色；  
   第四个元素中只有一个注释，此时该元素是空的，所以其背景会变成黄色；

```xml
<div>这个容器里的背景是橙色的</div>
<div> </div>
<div></div>
<div><!-- This comment is not considered content --></div>
```

```css
div {
    background: orange;
    height: 30px;
    width: 200px;
}
div:empty {
    background: yellow;
}
```

5. :enabled用于匹配没有设置disabled属性的表单元素。
6. :in-range匹配在指定区域内元素。  
   如下例，当数字选择器的数字在5到10是，数字选择器的边框会设为绿色。

```swift
<input type="number" min="5" max="10">
```

```css
input[type=number] {
    border: 5px solid orange;
}
input[type=number]:in-range {
    border: 5px solid green;
}
```

7. :out-of-range与:in-range相反，它匹配不在指定区域内的元素。
8. indeterminate的英文意思是“不确定的”。当某组中的单选框或复选框还没有选取状态时，:indeterminate匹配该组中所有的单选框或复选框。  
   如下例，当下面的一组单选框没有一个处于被选中时，与input相邻的label元素的背景会被设为橙色。

```xml
<ul>
    <li>
        <input type="radio" name="list" id="option1">
        <label for="option1">Option 1</label>
    </li>
    <li>
        <input type="radio" name="list" id="option2">
        <label for="option2">Option 2</label>
    </li>
    <li>
        <input type="radio" name="list" id="option3">
        <label for="option3">Option 3</label>
    </li>
</ul>
```

```css
:indeterminate + label {
    background: orange;
}
```

9. :valid匹配条件验证正确的表单元素。  
   如下例，当email输入框内的值符合email格式时，输入框的边框会被设为绿色。

```bash
<input type="email"/>
```

```css
input[type=email]:valid {
    border: 1px solid green;
}
```

10. :invalid与:valid相反，匹配条件验证错误的表单元素。
11. :optional用于匹配是具有optional属性的表单元素。当表单元素没有设置为required时，即为optional属性。  
    如下例，第一个input的背景不会被设为黄色，第二个input的背景会被设为黄色。

```bash
<input type="text" required />
<input type="text" />
```

```css
:optional {
    background: yellow;
}
```

12. :required与:optional相反匹配设置了required属性的表单元素。

13. :read-only匹配设置了只读属性的元素，表单元素可以通过设置“readonly”属性来定义元素只读。  
    如下例，input元素的背景会被设为黄色。

```bash
<input type="text" value="I am read only" readonly>
```

```css
put:read-only {
    background-color: yellow;
}
```

14. :read-write匹配处于编辑状态的元素。input，textarea和设置了contenteditable的HTML元素获取焦点时即处于编辑状态。  
    如下例，input输入框和副文本框获取焦点时，背景变成黄色。

```xml
<input type="text" value="获取焦点时背景变黄"/>

<div class="editable" contenteditable>
    <h1>点击这里可以编辑</h1>
    <p>获取焦点时背景变黄</p>
</div>
```

```css
:read-write:focus {
    background: yellow;
}
```

15. :scope(处于试验阶段)匹配处于style作用域下的元素。当style没有设置scope属性时，style内的样式会对整个html起作用。  
    如下例，第二个section中的元素的文本会变为斜体。

```xml
<article>
    <section>
        <h1>很正常的一些文本</h1>
        <p>很正常的一些文本</p>
    </section>
    <section>
        <style scoped>
            :scope {
                font-style: italic;
            }
        </style>
        <h1>这里的文本是斜体的</h1>
        <p>这里的文本是斜体的</p>
    </section>
</article>
注：目前支持这个伪类的浏览器只有火狐。
```

###### 语言相关

1. :dir(处于实验阶段)匹配指定阅读方向的元素，当HTML元素中设置了dir属性时该伪类才能生效。现时支持的阅读方向有两种：ltr（从左往右）和rtl（从右往左）。目前，只有火狐浏览器支持:dir伪类，并在火狐浏览器中使用时需要添加前缀( -moz-dir() )。  
   如下例，p元素中的阿拉伯语（阿拉伯语是从右往左阅读的）文本会变成橙色。

```xml
<article dir="rtl">
<p>التدليك واحد من أقدم العلوم الصحية التي عرفها الانسان والذي يتم استخدامه لأغراض الشفاء منذ ولاده الطفل.</p>
</article>
```

```css
/* prefixed */
article :-moz-dir(rtl) {
    color: orange;
}
/* unprefixed */
article :dir(rtl) {
    color: orange;
}
```

如下例，p元素中的英语文本会变成蓝色

```xml
<article dir="ltr">
    <p>اIf you already know some HTML and CSS and understand the principles of responsive web design, then this book is for you.</p>
</article>
```

```css
article :-moz-dir(ltr) {
    color: blue;
}

/* unprefixed */
article :dir(ltr) {
    color: blue;
}
```

2. :lang匹配设置了特定语言的元素，设置特定语言可以通过为了HTML元素设置lang=””属性，设置meta元素的charset=””属性，或者是在http头部上设置语言属性。实际上，lang=””属性不只可以在html标签上设置，也可以在其他的元素上设置。  
   如下例，分别给不同的语言设置不同的引用样式：

```xml
<article lang="en">
    <q>Lorem ipsum dolor sit amet.</q>
</article>
<article lang="fr">
    <q>Lorem ipsum dolor sit amet.</q>
</article>
<article lang="de">
    <q>Lorem ipsum dolor sit amet.</q>
</article>
```

```css
:lang(en) q { quotes: '“' '”'; }
:lang(fr) q { quotes: '«' '»'; }
:lang(de) q { quotes: '»' '«'; }
```

##### 其他

1. :root匹配文档的根元素。一般的html文件的根元素是html元素，而SVG或XML文件的根元素则可能是其他元素。  
   如下例，将html元素的背景设置为橙色

```css
:root {
    background: orange;
}
```

2. :fullscreen匹配处于全屏模式下的元素。全屏模式不是通过按F11来打开的全屏模式，而是通过Javascript的Fullscreen API来打开的，不同的浏览器有不同的Fullscreen API。目前，:fullscreen需要添加前缀才能使用。  
   如下例，当处于全屏模式时，h1元素的背景会变成橙色

```xml
<h1 id="element">在全屏模式下，这里的文本的背景会变成橙色.</h1>
<button>进入全屏模式!</button>
```

```jsx
JAVASCRIPT:

var docelem = document.getElementById('element');
var button = document.querySelector('button');
button.onclick = function() {

    if (docelem.requestFullscreen) {
        docelem.requestFullscreen();
    }else if (docelem.webkitRequestFullscreen) {
        docelem.webkitRequestFullscreen();
    } else if(docelem.mozRequestFullScreen) {
        docelem.mozRequestFullScreen();
    } else if(docelem.msRequestFullscreen) {
        docelem.msRequestFullscreen();
    }
}
```

```css
CSS:h1:fullscreen {
    background: orange;
}
h1:-webkit-full-screen {
    background: orange;
}

h1:-moz-full-screen {
    background: orange;
}

h1:-ms-fullscreen {
    background: orange;
}
```

## tramsform 和translate 以及animation

translate:移动，transform的一个方法

transform:变形。改变

| 值                                                                         | 描述                         |
| ------------------------------------------------------------------------- | -------------------------- |
| none                                                                      | 定义不进行转换。                   |
| matrix(*n*,*n*,*n*,*n*,*n*,*n*)                                           | 定义 2D 转换，使用六个值的矩阵。         |
| matrix3d(*n*,*n*,*n*,*n*,*n*,*n*,*n*,*n*,*n*,*n*,*n*,*n*,*n*,*n*,*n*,*n*) | 定义 3D 转换，使用 16 个值的 4x4 矩阵。 |
| translate(*x*,*y*)                                                        | 定义 2D 转换。                  |
| translate3d(*x*,*y*,*z*)                                                  | 定义 3D 转换。                  |
| translateX(*x*)                                                           | 定义转换，只是用 X 轴的值。            |
| translateY(*y*)                                                           | 定义转换，只是用 Y 轴的值。            |
| translateZ(*z*)                                                           | 定义 3D 转换，只是用 Z 轴的值。        |
| scale(*x*,*y*)                                                            | 定义 2D 缩放转换。                |
| scale3d(*x*,*y*,*z*)                                                      | 定义 3D 缩放转换。                |
| scaleX(*x*)                                                               | 通过设置 X 轴的值来定义缩放转换。         |
| scaleY(*y*)                                                               | 通过设置 Y 轴的值来定义缩放转换。         |
| scaleZ(*z*)                                                               | 通过设置 Z 轴的值来定义 3D 缩放转换。     |
| rotate(*angle*)                                                           | 定义 2D 旋转，在参数中规定角度。         |
| rotate3d(*x*,*y*,*z*,*angle*)                                             | 定义 3D 旋转。                  |
| rotateX(*angle*)                                                          | 定义沿着 X 轴的 3D 旋转。           |
| rotateY(*angle*)                                                          | 定义沿着 Y 轴的 3D 旋转。           |
| rotateZ(*angle*)                                                          | 定义沿着 Z 轴的 3D 旋转。           |
| skew(*x-angle*,*y-angle*)                                                 | 定义沿着 X 和 Y 轴的 2D 倾斜转换。     |
| skewX(*angle*)                                                            | 定义沿着 X 轴的 2D 倾斜转换。         |
| skewY(*angle*)                                                            | 定义沿着 Y 轴的 2D 倾斜转换。         |
| perspective(*n*)                                                          | 为 3D 转换元素定义透视视图。           |

transition: 允许CSS属性值在一定的时间区间内平滑的过渡，**（过渡动画）**

与Transition不同的是：

1. Animation可以通过keyframe显示控制当前帧的属性值，而   Transition只能隐式来进行（不能指定每帧的属性值），所以相对 而言Animation的功能更加灵活。
2. Animation通过模拟属性值改变来实现动画，动画结束之后元素的属性没有变化；而Transiton确实改变了元素的属性值，动画结束之后元素的属性发生了变化；这一点，这在实际应用中会产生很大的区别。

## translate和top/left的比较

translate是transform的一个方法，top、left是基于父元素的：  translate的参数：left（x 坐标） 和 top（y 坐标） 位置参数 ，
如果是百分比，会以本身的长宽做参考top/left是布局类的样式，

top/left是布局类样式，这个样式的变化会导致重排（reflow/relayout），所谓重排即指对这些节点以及受这些节点影响的其它节点，进行CSS计算->布局->重绘过程，这个过程的前2步是消耗大量资源的

translate是一个绘制样式（这名词我瞎编的），这个样式的变化会导致重绘（repaint），即在屏幕上重新画一下，不会进行CSS计算和布局这2个性能大户，所以我们认为translate性能上要明显好于top/left

translate3D传说因为走的是3D，所以能得到更完整的GPU加速的支持，在GPU中还有贴图缓存等手段帮你优化性能，所以更快

## 媒体查询

案例宽度

```
/* 超小屏幕（手机，小于 768px） */
/* 没有任何媒体查询相关的代码，因为这在 Bootstrap 中是默认的（还记得 Bootstrap 是移动设备优先的吗？） */

/* 小屏幕（平板，大于等于 768px） */
@media (min-width: @screen-sm-min) { ... }

/* 中等屏幕（桌面显示器，大于等于 992px） */
@media (min-width: @screen-md-min) { ... }

/* 大屏幕（大桌面显示器，大于等于 1200px） */
@media (min-width: @screen-lg-min) { ... }
```

### rem 移动端布局原理(媒体查询加fontsize)

#### 1. 媒体查询改变根元素的字体大小

```
/* 媒体查询以@media开头，然后后面可以跟上 判断的条件，比如：screen表示屏幕设备, and是并且的意思， min-width是最小宽度的意思。 */
@media screen and (min-width: 320px) {
    html {font-size: 50px;}
}

@media screen and (min-width: 360px) {
    html {font-size: 56.25px;}
}

@media screen and (min-width: 400px) {
    html {font-size: 62.5px;}
}

@media screen and (min-width: 440px) {
    html {font-size: 68.75px;}
}

@media screen and (min-width: 480px) {
    html {font-size: 75px;}
}

@media screen and (min-width: 640px) {
    html {font-size: 100px;}
}
```

#### 2.js实现动态改变根元素的字体大小

```javascript
!(function(doc, win) {
    var docEle = doc.documentElement,
        evt = "onorientationchange" in window ? "orientationchange" : "resize",
        fn = function() {
            var width = docEle.clientWidth;
            width = width < 320 ? 320 : width;
            width = width > 640 ? 640 : width;
            width && (docEle.style.fontSize = 100 * (width / 640) + "px");
        };

    win.addEventListener(evt, fn, false);
    doc.addEventListener("DOMContentLoaded", fn, false);

}(document, window));
```

## viewport

- **width**: 设置viewport宽度，为一个正整数，或字符串 device-width
- **device-width**: 设备宽度
- **height**: 设置viewport高度，一般设置了宽度，会自动解析出高度，可以不用设置
- **initial-scale**: 默认缩放比例（初始缩放比例），为一个数字，可以带小数
- **minimum-scale**: 允许用户最小缩放比例，为一个数字，可以带小数
- **maximum-scale**: 允许用户最大缩放比例，为一个数字，可以带小数
- **user-scalable**: 是否允许手动缩放

**延伸提问：怎样处理 移动端 1px 被渲染成 2px 问题？**

1、局部处理

meta 标签中的 viewport 属性 ，initial-scale 设置为 1

rem 按照设计稿标准走，外加利用 transfrome 的 scale(0.5) 缩小一倍即可；

2、全局处理

meta 标签中的 viewport 属性 ，initial-scale 设置为 0.5

rem 按照设计稿标准走即可

## 谈谈CSS中link和@import的区别是

1）link属于HTML标签，而@import是CSS提供的；

2）页面被加载时，link会同时被加载，而@import引用的CSS会等到页面被加载完再加载；

3）import只在IE5以上才能识别，而link是HTML标签，无兼容问题；

4）link方式的样式权重高于@import的权重。

## CSS文件没下载完会影响DOM树吗？

1、css是由单独的下载线程异步下载的。

2、css加载不会阻塞DOM树解析（异步加载时DOM照常构建）

3、但会阻塞render树渲染（渲染时需等css加载完毕，因为render树需要css信息）

## IFC

IFC(Inline Formatting Contexts)直译为"内联格式化上下文"，在常规流中横着排列。

**行级盒子高度是由font-size决定的**
宽度等于其子行级盒子的外宽度，但是当行盒的宽度大于父宽度时会被拆分为多个行盒。

**如何计算行级盒子的高度**：

1、位于该行上的所有in-flow的inline-level box均参与该行line box高度的计算;

2、各inline-level box根据vertical-align属性值相对各自的父容器作垂直方向对齐;

3、最上方的box的上边界到最下方的下边界则是line box的高度。

**IFC的规则**

1、盒子是水平一个接一个的排列，水平的margin，内边距，边框是可以有的。

2、垂直方向的对齐，可能是底部对齐，顶部对齐，也可能是基线对齐（这个是默认的）；

3、行框中的内联盒子的高度小于行框的高度时，内联盒子的垂直方向的对齐方式取决于vertical-align属性

4、当一个行框水平不能容纳内联盒子时，他们将会在垂直方向上产生多个行框，他们上下一个挨着一个，但是不会重叠

5、一般来说，行框的左边界紧挨着包含容器的左边界，行框的右边界紧挨着包含容器的右边界。

6、多个内联盒子的宽度小于包含他们的行框时，他们在水平方向的分布取决于text-align属性（默认是left）

## 隐藏元素的三种方式，区别？display，visibility，opacity三个属性有什么区别？

1. display: none (不占空间，不能点击)（场景，显示出原来这里不存在的结构）
2. visibility: hidden（占据空间，不能点击）（场景：显示不会导致页面结构发生变动，不会撑开）
3. opacity: 0（占据空间，可以点击）（场景：可以跟transition搭配）

## 布局

display: flex

position:relative  相对于其正常位置进行定位，可以使用top和left等属性，使其相对于原位置进行偏移

position:absolute 相对于 static 定位以外的第一个父元素进行定位。

position:fixed 相对于浏览器窗口进行定位。

父级标签设置transform可以让子标签fixed定位失效

position:sticky 基于用户的滚动位置来定位。

sticky：粘性定位的元素是依赖于用户的滚动，在 **position:relative** 与 **position:fixed** 定位之间切换。

使用场景：顶部导航栏，backtop

inherit：从父元素那继承position属性

initial：默认样式

position:static 元素出现在正常的流中

相对定位是根据正常流位置进行偏移的

绝对定位脱离文档流，相对于 static 定位以外的第一个父元素进行定位。

```
子绝父相
<div style={{position: relative}}>
    <div style={{position: absolute}}>
    </div>
</div>
```

## 七阶层叠水平

**1. 层叠水平**

![img](https://img2018.cnblogs.com/blog/1292376/201908/1292376-20190818204227027-1817973976.png)

当两个元素层叠水平相同的时候，这时候就要遵循下面两个准则：

1. 后来居上原则
2. 谁 z-index 大，谁在上的准则

## **2. 层叠上下文**

层叠上下文，你可以理解为 JS 中的作用域，一个页面中往往不仅仅只有一个层叠上下文(因为有很多种方式可以**生成**层叠上下文，只是你没有意识到而已)，在一个层叠上下文内，我们按照层叠水平的规则来堆叠元素。

介绍完层叠上下文的概念，我们先来看看哪些方式可以创建层叠上下文？

正常情况下，一共有三种大的类型创建层叠上下文：

1. 默认创建层叠上下文
2. 需要配合 z-index 触发创建层叠上下文
3. 不需要配合 z-index 触发创建层叠上下文

一、默认创建层叠上下文

默认创建层叠上下文，只有 HTML 根元素，这里你可以理解为 body 标签。它属于根层叠上下文元素，不需要任何 CSS 属性来触发。

二、需要配合 z-index 触发创建层叠上下文

依赖 z-index 值创建层叠上下文的情况：

1. position 值为 relative/absolute/fixed(部分浏览器)
2. flex 项(父元素 display 为 flex|inline-flex)，注意是**子元素会创建层叠上下文**，不是父元素创建层叠上下文

**这两种情况下，需要设置具体的 z-index 值**，不能设置 z-index 为 auto（要设置成z-index: 0），这也就是 z-index: auto 和 z-index: 0 的一点细微差别。

前面我们提到，设置 position: relative 的时候 z-index 的值为 auto 会生效，但是这时候并没有创建层叠上下文，当设置 z-index 不为 auto，哪怕设置 z-index: 0 也会触发元素创建层叠上下文。

三、不需要配合 z-index 触发创建层叠上下文

这种情况下，基本上都是由 CSS3 中新增的属性来触发的，常见的有：

- 元素的透明度 opacity 小于1
- 元素的 mix-blend-mode 值不是 normal
- 元素的以下属性的值不是 none：
  - transform
  - filter
  - perspective
  - clip-path
  - mask / mask-image / mask-border
- 元素的 isolution 属性值为 isolate
- 元素的 -webkit-overflow-scrolling 属性为 touch
- 元素的 will-change 属性具备会创建层叠上下文的值

介绍完如何创建层叠上下文概念以及创建方式后，需要说明的是，**创建了层叠上下文的元素可以理解局部层叠上下文，它只影响其子孙代元素，它自身的层叠水平是由它的父层叠上下文所决定的。**

## 三栏布局

#### 1、绝对定位布局：position + margin

html结构：

    <div class="container">
        <div class="left">Left</div>
        <div class="right">Right</div>
        <div class="main">Main</div>
    </div>

css样式：

     body,html{
        height: 100%;
        padding: 0;
        margin: 0;
        overflow: hidden;
    }
    /*左右进行绝对定位*/
    .left,.right{
        position: absolute;
        height:100%;  
        top: 0;
        background: #ff69b4;
    }
    .left{
        left: 0;
        width: 100px;
    }
    .right{
        right: 0;
        width: 200px;
    }
    /*中间用margin空出左右元素所占的空间*/
    .main{
        height:100%; 
        margin: 0 100px 200px 0;
        background: #659;
    }

缺点: 如果中间栏含有最小宽度限制，或是含有宽度的内部元素，当浏览器宽度小到一定程度，会发生层重叠的情况。

2、浮动布局： float + margin
html结构：

    <div class="container">
        <div class="left">Left</div>
        <div class="right">Right</div>
        <div class="main">Main</div>
    </div>

css样式：

        body,html{
            height: 100%;
            padding:0;
            margin: 0;
        }
        /*左边栏左浮动*/
        .left{
            float:left;
            height:100%;
            width:100px;
            background:#ff69b4;
        }
        /*中间栏自适应*/
        .main{
            height:100%;
            margin:0 200px 0 100px;
            background: #659;
        }
        /*右边栏右浮动*/
        .right{
            float:right;
            height:100%;
            width:200px;
            background:#ff69b4;
        }

3、flex布局
html结构：

    <div class="container">
        <div class="left"></div>
        <div class="main">center</div>
        <div class="right">right</div>
    </div>

css样式：

     .container{
            display: flex;
        }
        .left{
            width:200px;
            background: red;
        }
        .main{
            flex: 1;
            background: blue;
        }
        .right{
            width:200px;
            background: red;
        }

这种布局方式，高度由内容决定。

4、table布局
html结构:

    <div class="container">
        <div class="left">left</div>
        <div class="main">center</div>
        <div class="right">right</div>
    </div>

css样式：

         .container{
            display: table;
            width:100%;
        }
        .container>div{
            display: table-cell;
        }
        .left{
            width: 100px;
            background: red;
        }
        .main{
            background: blue;
        }
        .right{
            width: 200px;
            background: red;
        }

高度由内容决定。

5、Grid网格布局
html结构：

    <div class="container">
        <div class="left">left</div>
        <div class="main">center</div>
        <div class="right">right</div>
    </div>

css样式：

        .container{
            display: grid;
            width: 100%;
            grid-template-rows: 100px;  /*设置行高*/
            grid-template-columns: 100px auto 200px;  /*设置列数属性*/
        }
        .left{
            background: red;
        }
        .main{
            background: blue;
        }
        .right{
            background:red;
        }

## **圣杯布局和双飞翼布局**

转自文档：https://juejin.im/post/5caf4043f265da039f0eff94

##### 圣杯布局

```html
!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="wrap">
        <div id="header">header</div>
        <div id="content">
            <div id="middle">
                middle
            </div>
            <div id="left">left</div>         
            <div id="right">right</div>
        </div>
        <div id="footer">footer</div>
    </div>
</body>
</html>
```

```css
*{
    margin: 0;
    padding: 0;
}
body{
    min-width: 600px;
}
#header,#footer{
    height: 50px;
    width: 100%;
    background: grey;
}
#middle#left,#right{
    float: left;
}
#content{
    overflow: hidden;
    padding: 0 200px;
}
#left,#right{
    width: 200px;
    height: 200px;
    background: pink;
}
#middle{
    width: 100%;
    height: 200px;
    background: yellowgreen; 
}
 #left{
    margin-left:-100% ;
    position: relative;
    left:-200px;
} 
#right{
    margin-left: -200px;
    position: relative;
    left:200px; 
}
```

##### 双飞翼

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="wrap">
        <div id="header">header</div>
        <div id="content">
            <div id="middle">
                <div class="middle-inner">
                    middle
                </div>
            </div>
            <div id="left">left</div>         
            <div id="right">right</div>
        </div>
        <div id="footer">footer</div>
    </div>
</body>
</html>
```

```css
*{
    margin: 0;
    padding: 0;
}
.wrap{
    min-width: 600px;
}
#header,#footer{
    height: 50px;
    width: 100%;
    background:grey;
}
#left,#right{
    width: 200px;
    height: 200px;
    background: green;
}

#middle{
    background: pink;
    width: 100%;
    float: left;
    height:200px;
}
#content{
    overflow: hidden;
}
#left{
    float: left;
    margin-left: -100%;
}
#right{
    float: left;
    margin-left: -200px;
}
.middle-inner{
    margin: 0 200px;
}
```

## Flex布局

flex: *flex-grow* *flex-shrink* *flex-basis*|auto|initial|inherit;

| 值             | 描述                                                                                       |
| ------------- | ---------------------------------------------------------------------------------------- |
| *flex-grow*   | 一个数字，规定项目将相对于其他灵活的项目进行扩展的量。                                                              |
| *flex-shrink* | 一个数字，规定项目将相对于其他灵活的项目进行收缩的量。                                                              |
| *flex-basis*  | 项目的长度。合法值："auto"、"inherit" 或一个后跟 "%"、"px"、"em" 或任何其他长度单位的数字。                             |
| auto          | 与 1 1 auto 相同。                                                                           |
| none          | 与 0 0 auto 相同。                                                                           |
| initial       | 设置该属性为它的默认值，即为 0 1 auto。请参阅 [*initial*](https://www.runoob.com/cssref/css-initial.html)。 |
| inherit       | 从父元素继承该属性。请参阅 [*inherit*](https://www.runoob.com/cssref/css-inherit.html)。               |

## flex-grow

表示当子元素的空间小于父元素的空间时,如何处理剩余空间,  
默认值为0表示不占有剩余空间;  
当子元素都设置为1时表示平均分配剩余空间;  
当一个子元素为2其余子元素为1时为2的子元素占据的剩余空间比其他子元素多一倍;  
当一个子元素为1其余子元素为0,为1的子元素占据全局剩余空间

## felx-shrink

表示当子元素的空间大于父元素的空间时,如何缩小子元素  
默认值为1表示等比缩小  
当所有子元素都设置为1时所有子元素都会等比例缩小  
当一个子元素为0其余子元素为1是表示为0的子元素不缩小,其余元素缩小

## flex-basis

用于设置项目占据的主轴空间,设置为auto表示项目占据的主轴大小等于项目的实际内容大小,设置为固定值表示项目占据的主轴大小等于固定值

### align-items

align-items默认是stretch元素被拉伸以适应容器。

如果指定侧轴大小的属性值为'auto'，则其值会使项目的边距盒的尺寸尽可能接近所在行的尺寸，但同时会遵照'min/max-width/height'属性的限制。

### 阮一峰：Flex

采用 Flex 布局的元素，称为 Flex 容器（flex container），简称"容器"。它的所有子元素自动成为容器成员，称为 Flex 项目（flex item），简称"项目"。

![](https://www.ruanyifeng.com/blogimg/asset/2015/bg2015071004.png)

容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。主轴的开始位置（与边框的交叉点）叫做`main start`，结束位置叫做`main end`；交叉轴的开始位置叫做`cross start`，结束位置叫做`cross end`。

项目默认沿主轴排列。单个项目占据的主轴空间叫做`main size`，占据的交叉轴空间叫做`cross size`。

## 三、容器的属性

以下6个属性设置在容器上。

> - flex-direction
> - flex-wrap
> - flex-flow
> - justify-content
> - align-items
> - align-content

### 3.1 flex-direction属性

`flex-direction`属性决定主轴的方向（即项目的排列方向）。

> ```css
> .box {
>   flex-direction: row | row-reverse | column | column-reverse;
> }
> ```

![](https://www.ruanyifeng.com/blogimg/asset/2015/bg2015071005.png)

它可能有4个值。

> - `row`（默认值）：主轴为水平方向，起点在左端。
> - `row-reverse`：主轴为水平方向，起点在右端。
> - `column`：主轴为垂直方向，起点在上沿。
> - `column-reverse`：主轴为垂直方向，起点在下沿。

### 3.2 flex-wrap属性

默认情况下，项目都排在一条线（又称"轴线"）上。`flex-wrap`属性定义，如果一条轴线排不下，如何换行。

![](https://www.ruanyifeng.com/blogimg/asset/2015/bg2015071006.png)

> ```css
> .box{
>   flex-wrap: nowrap | wrap | wrap-reverse;
> }
> ```

它可能取三个值。

（1）`nowrap`（默认）：不换行。

![](https://www.ruanyifeng.com/blogimg/asset/2015/bg2015071007.png)

（2）`wrap`：换行，第一行在上方。

![](https://www.ruanyifeng.com/blogimg/asset/2015/bg2015071008.jpg)

（3）`wrap-reverse`：换行，第一行在下方。

![](https://www.ruanyifeng.com/blogimg/asset/2015/bg2015071009.jpg)

### 3.3 flex-flow

`flex-flow`属性是`flex-direction`属性和`flex-wrap`属性的简写形式，默认值为`row nowrap`。

> ```css
> .box {
>   flex-flow: <flex-direction> || <flex-wrap>;
> }
> ```

### 3.4 justify-content属性

`justify-content`属性定义了项目在主轴上的对齐方式。

> ```css
> .box {
>   justify-content: flex-start | flex-end | center | space-between | space-around;
> }
> ```

![](https://www.ruanyifeng.com/blogimg/asset/2015/bg2015071010.png)

它可能取5个值，具体对齐方式与轴的方向有关。下面假设主轴为从左到右。

> - `flex-start`（默认值）：左对齐
> - `flex-end`：右对齐
> - `center`： 居中
> - `space-between`：两端对齐，项目之间的间隔都相等。
> - `space-around`：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。

### 3.5 align-items属性

`align-items`属性定义项目在交叉轴上如何对齐。

> ```css
> .box {
>   align-items: flex-start | flex-end | center | baseline | stretch;
> }
> ```

![](https://www.ruanyifeng.com/blogimg/asset/2015/bg2015071011.png)

它可能取5个值。具体的对齐方式与交叉轴的方向有关，下面假设交叉轴从上到下。

> - `flex-start`：交叉轴的起点对齐。
> - `flex-end`：交叉轴的终点对齐。
> - `center`：交叉轴的中点对齐。
> - `baseline`: 项目的第一行文字的基线对齐。
> - `stretch`（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度。

### 3.6 align-content属性

`align-content`属性定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。

> ```css
> .box {
>   align-content: flex-start | flex-end | center | space-between | space-around | stretch;
> }
> ```

![](https://www.ruanyifeng.com/blogimg/asset/2015/bg2015071012.png)

该属性可能取6个值。

> - `flex-start`：与交叉轴的起点对齐。
> - `flex-end`：与交叉轴的终点对齐。
> - `center`：与交叉轴的中点对齐。
> - `space-between`：与交叉轴两端对齐，轴线之间的间隔平均分布。
> - `space-around`：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
> - `stretch`（默认值）：轴线占满整个交叉轴。

### 四、项目的属性

以下6个属性设置在项目上。

> - `order`
> - `flex-grow`
> - `flex-shrink`
> - `flex-basis`
> - `flex`
> - `align-self`

### 4.1 order属性

`order`属性定义项目的排列顺序。数值越小，排列越靠前，默认为0。

> ```css
> .item {
>   order: <integer>;
> }
> ```

![](https://www.ruanyifeng.com/blogimg/asset/2015/bg2015071013.png)

### 4.2 flex-grow属性

`flex-grow`属性定义项目的放大比例，默认为`0`，即如果存在剩余空间，也不放大。

> ```css
> .item {
>   flex-grow: <number>; /* default 0 */
> }
> ```

![](https://www.ruanyifeng.com/blogimg/asset/2015/bg2015071014.png)

如果所有项目的`flex-grow`属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的`flex-grow`属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。

### 4.3 flex-shrink属性

`flex-shrink`属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。

> ```css
> .item {
>   flex-shrink: <number>; /* default 1 */
> }
> ```

![](https://www.ruanyifeng.com/blogimg/asset/2015/bg2015071015.jpg)

如果所有项目的`flex-shrink`属性都为1，当空间不足时，都将等比例缩小。如果一个项目的`flex-shrink`属性为0，其他项目都为1，则空间不足时，前者不缩小。

负值对该属性无效。

### 4.4 flex-basis属性

`flex-basis`属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为`auto`，即项目的本来大小。

> ```css
> .item {
>   flex-basis: <length> | auto; /* default auto */
> }
> ```

它可以设为跟`width`或`height`属性一样的值（比如350px），则项目将占据固定空间。

### 4.5 flex属性

`flex`属性是`flex-grow`, `flex-shrink` 和 `flex-basis`的简写，默认值为`0 1 auto`。后两个属性可选。

> ```css
> .item {
>   flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
> }
> ```

该属性有两个快捷值：`auto` (`1 1 auto`) 和 none (`0 0 auto`)。

建议优先使用这个属性，而不是单独写三个分离的属性，因为浏览器会推算相关值。

### 4.6 align-self属性

`align-self`属性允许单个项目有与其他项目不一样的对齐方式，可覆盖`align-items`属性。默认值为`auto`，表示继承父元素的`align-items`属性，如果没有父元素，则等同于`stretch`。

> ```css
> .item {
>   align-self: auto | flex-start | flex-end | center | baseline | stretch;
> }
> ```

![](https://www.ruanyifeng.com/blogimg/asset/2015/bg2015071016.png)

该属性可能取6个值，除了auto，其他都与align-items属性完全一致。

（完）

### 说说align-items和align-content的区别

align-items属性适用于所有的flex容器，它是用来设置每个flex元素在侧轴上的默认对齐方式。

align-items和align-content有相同的功能，不过不同点是它是用来让每一个单行的容器居中而不是让整个容器居中。

align-content属性只适用于多行的flex容器，并且当侧轴上有多余空间使flex容器内的flex线对齐。

## position, display, float一起设置，会怎么样

当display: none，position和float无作用;

当position: absolute或 fixed,float为none。

## 水平垂直居中

##### 水平

1) 若是行内元素, 给其父元素设置 text-align:center,即可实现行内元素水平居中.

2) 若是块级元素, 该元素设置 margin:0 auto即可.

3) 使用flex, 可以轻松的实现水平居中, 子元素设置如下:

```
.son{ 
    display: flex; 
    justify-content: center; 
} 
```

4) 使用CSS3中新增的transform属性, 子元素设置如下: 

```
son{ 
    position:absolute; 
    left:50%; 
    transform:translate(-50%,0); 
} 
```

5) 使用绝对定位方式, 以及负值的margin-left, 子元素设置如下:

```
.son{ 
    position:absolute; 
    width:固定; 
    left:50%; 
    margin-left:-0.5宽度; 
} 
```

##### **垂直居中**

1) 若元素是单行文本, 则可设置 line-height 等于父元素高度

元素高度不定 

2) 可用 **vertical-align** 属性, 而vertical-align只有在父层为 td 或者 th 时, 才会生效, 对于其他块级元素, 例如 div、p 等, 默认情况是不支持的. 为了使用vertical-align, 我们需要设置父元素display:table, 子元素 display:table-cell;vertical-align:middle; 

**优点**

元素高度可以动态改变, 不需再CSS中定义, 如果父元素没有足够空间时, 该元素内容也不会被截断.

**缺点**

IE6~7, 甚至IE8 beta中无效.

3) 父元素做如下设置即可保证子元素垂直居中:

```
.parent { 
  display: flex; 
  align-items: center; 
} 
```

**优点**

- 内容块的宽高任意, 优雅的溢出. 

- 可用于更复杂高级的布局技术中. 

**缺点**

- IE8/IE9不支持 

- 需要浏览器厂商前缀 

- 渲染上可能会有一些问题 
4) 可用 **transform** , 设置父元素相对定位(position:relative), 子元素如下css样式: 

```
.son{ 
    position:absolute; 
    top:50%; 
    -webkit-transform: translate(-50%,-50%);  
    -ms-transform: translate(-50%,-50%); 
    transform: translate(-50%,-50%); 
} 
```

7) 设置父元素相对定位(position:relative), 子元素如下css样式: 

```
.son{ 
    position:absolute; 
    top:50%; 
    height:固定; 
    margin-top:-0.5高度; 
} 
```

**优点**

适用于所有浏览器.

**缺点**

父元素空间不够时, 子元素可能不可见(当浏览器窗口缩小时,滚动条不出现时).如果子元素设置了overflow:auto, 则高度不够时, 会出现滚动条.

**总结** 

水平居中较为简单, 一般情况下 text-align:center,marin:0 auto; 足矣

- text-align:center; 

- margin:0 auto; 

- flex  

- transform 

垂直居中

- 单行文本, line-height  
- vertical-align 
- flex  
- transform  

## **如何实现左侧宽度固定，右侧宽度自适应的布局**

**利用float + margin实现** 

```
.box { 
height: 200px; 
} 
.box > div { 
  height: 100%; 
} 
.box-left { 
  width: 200px; 
  float: left; 
  background-color: blue; 
} 
.box-right { 
  margin-left: 200px; 
  background-color: red; 
} 
```

**利用calc计算宽度** 

```
.box { 
height: 200px; 
} 
.box > div { 
  height: 100%; 
} 
.box-left { 
  width: 200px; 
  float: left; 
  background-color: blue; 
} 
.box-right { 
  width: calc(100% - 200px); 
  float: right; 
  background-color: red; 
} 
```

**利用float + overflow实现** 

```
.box { 
height: 200px; 
} 

.box > div { 
  height: 100%; 
} 
.box-left { 
  width: 200px; 
  float: left; 
  background-color: blue; 
} 
.box-right { 
  overflow: hidden; 
  background-color: red; 
} 
```

**利用flex实现** 

这里不是最佳答案，应该是使用flex-basis实现更合理

```
.box { 
  height: 200px; 
  display: flex; 
} 
.box > div { 
  height: 100%; 
} 
.box-left { 
  width: 200px; 
  background-color: blue; 
} 
.box-right { 
  flex: 1; // 设置flex-grow属性为1，默认为0 
  overflow: hidden; 
  background-color: red; 
} 
```

## css选择器

| 选择器                                                                                                | 例子                    | 例子描述                                 |
|:-------------------------------------------------------------------------------------------------- |:--------------------- |:------------------------------------ |
| [.*class*](https://www.w3school.com.cn/cssref/selector_class.asp)                                  | .intro                | 选择 class="intro" 的所有元素。              |
| .*class1*.*class2*                                                                                 | .name1.name2          | 选择 class 属性中同时有 name1 和 name2 的所有元素。 |
| .*class1* .*class2*                                                                                | .name1 .name2         | 选择作为类名 name1 元素后代的所有类名 name2 元素。     |
| [#*id*](https://www.w3school.com.cn/cssref/selector_id.asp)                                        | #firstname            | 选择 id="firstname" 的元素。               |
| [*](https://www.w3school.com.cn/cssref/selector_all.asp)                                           | *                     | 选择所有元素。                              |
| [*element*](https://www.w3school.com.cn/cssref/selector_element.asp)                               | p                     | 选择所有 <p> 元素。                         |
| [*element*.*class*](https://www.w3school.com.cn/cssref/selector_element_class.asp)                 | p.intro               | 选择 class="intro" 的所有 <p> 元素。         |
| [*element*,*element*](https://www.w3school.com.cn/cssref/selector_element_comma.asp)               | div, p                | 选择所有 <div> 元素和所有 <p> 元素。             |
| [*element* *element*](https://www.w3school.com.cn/cssref/selector_element_element.asp)             | div p                 | 选择 <div> 元素内的所有 <p> 元素。              |
| [*element*>*element*](https://www.w3school.com.cn/cssref/selector_element_gt.asp)                  | div > p               | 选择父元素是 <div> 的所有 <p> 元素。             |
| [*element*+*element*](https://www.w3school.com.cn/cssref/selector_element_plus.asp)                | div + p               | 选择紧跟 <div> 元素的首个 <p> 元素。             |
| [*element1*~*element2*](https://www.w3school.com.cn/cssref/selector_gen_sibling.asp)               | p ~ ul                | 选择前面有 <p> 元素的每个 <ul> 元素。             |
| [[*attribute*\]](https://www.w3school.com.cn/cssref/selector_attribute.asp)                        | [target]              | 选择带有 target 属性的所有元素。                 |
| [[*attribute*=*value*\]](https://www.w3school.com.cn/cssref/selector_attribute_value.asp)          | [target=_blank]       | 选择带有 target="_blank" 属性的所有元素。        |
| [[*attribute*~=*value*\]](https://www.w3school.com.cn/cssref/selector_attribute_value_contain.asp) | [title~=flower]       | 选择 title 属性包含单词 "flower" 的所有元素。      |
| [[*attribute*\|=*value*\]](https://www.w3school.com.cn/cssref/selector_attribute_value_start.asp)  | [lang\|=en]           | 选择 lang 属性值以 "en" 开头的所有元素。           |
| [[*attribute*^=*value*\]](https://www.w3school.com.cn/cssref/selector_attr_begin.asp)              | a[href^="https"]      | 选择其 src 属性值以 "https" 开头的每个 <a> 元素。   |
| [[*attribute*$=*value*\]](https://www.w3school.com.cn/cssref/selector_attr_end.asp)                | a[href$=".pdf"]       | 选择其 src 属性以 ".pdf" 结尾的所有 <a> 元素。     |
| [[*attribute**=*value*\]](https://www.w3school.com.cn/cssref/selector_attr_contain.asp)            | a[href*="w3schools"]  | 选择其 href 属性值中包含 "abc" 子串的每个 <a> 元素。  |
| [:active](https://www.w3school.com.cn/cssref/selector_active.asp)                                  | a:active              | 选择活动链接。                              |
| [::after](https://www.w3school.com.cn/cssref/selector_after.asp)                                   | p::after              | 在每个 <p> 的内容之后插入内容。                   |
| [::before](https://www.w3school.com.cn/cssref/selector_before.asp)                                 | p::before             | 在每个 <p> 的内容之前插入内容。                   |
| [:checked](https://www.w3school.com.cn/cssref/selector_checked.asp)                                | input:checked         | 选择每个被选中的 <input> 元素。                 |
| [:default](https://www.w3school.com.cn/cssref/selector_default.asp)                                | input:default         | 选择默认的 <input> 元素。                    |
| [:disabled](https://www.w3school.com.cn/cssref/selector_disabled.asp)                              | input:disabled        | 选择每个被禁用的 <input> 元素。                 |
| [:empty](https://www.w3school.com.cn/cssref/selector_empty.asp)                                    | p:empty               | 选择没有子元素的每个 <p> 元素（包括文本节点）。           |
| [:enabled](https://www.w3school.com.cn/cssref/selector_enabled.asp)                                | input:enabled         | 选择每个启用的 <input> 元素。                  |
| [:first-child](https://www.w3school.com.cn/cssref/selector_first-child.asp)                        | p:first-child         | 选择属于父元素的第一个子元素的每个 <p> 元素。            |
| [::first-letter](https://www.w3school.com.cn/cssref/selector_first-letter.asp)                     | p::first-letter       | 选择每个 <p> 元素的首字母。                     |
| [::first-line](https://www.w3school.com.cn/cssref/selector_first-line.asp)                         | p::first-line         | 选择每个 <p> 元素的首行。                      |
| [:first-of-type](https://www.w3school.com.cn/cssref/selector_first-of-type.asp)                    | p:first-of-type       | 选择属于其父元素的首个 <p> 元素的每个 <p> 元素。        |
| [:focus](https://www.w3school.com.cn/cssref/selector_focus.asp)                                    | input:focus           | 选择获得焦点的 input 元素。                    |
| [:fullscreen](https://www.w3school.com.cn/cssref/selector_fullscreen.asp)                          | :fullscreen           | 选择处于全屏模式的元素。                         |
| [:hover](https://www.w3school.com.cn/cssref/selector_hover.asp)                                    | a:hover               | 选择鼠标指针位于其上的链接。                       |
| [:in-range](https://www.w3school.com.cn/cssref/selector_in-range.asp)                              | input:in-range        | 选择其值在指定范围内的 input 元素。                |
| [:indeterminate](https://www.w3school.com.cn/cssref/selector_indeterminate.asp)                    | input:indeterminate   | 选择处于不确定状态的 input 元素。                 |
| [:invalid](https://www.w3school.com.cn/cssref/selector_invalid.asp)                                | input:invalid         | 选择具有无效值的所有 input 元素。                 |
| [:lang(*language*)](https://www.w3school.com.cn/cssref/selector_lang.asp)                          | p:lang(it)            | 选择 lang 属性等于 "it"（意大利）的每个 <p> 元素。    |
| [:last-child](https://www.w3school.com.cn/cssref/selector_last-child.asp)                          | p:last-child          | 选择属于其父元素最后一个子元素每个 <p> 元素。            |
| [:last-of-type](https://www.w3school.com.cn/cssref/selector_last-of-type.asp)                      | p:last-of-type        | 选择属于其父元素的最后 <p> 元素的每个 <p> 元素。        |
| [:link](https://www.w3school.com.cn/cssref/selector_link.asp)                                      | a:link                | 选择所有未访问过的链接。                         |
| [:not(*selector*)](https://www.w3school.com.cn/cssref/selector_not.asp)                            | :not(p)               | 选择非 <p> 元素的每个元素。                     |
| [:nth-child(*n*)](https://www.w3school.com.cn/cssref/selector_nth-child.asp)                       | p:nth-child(2)        | 选择属于其父元素的第二个子元素的每个 <p> 元素。           |
| [:nth-last-child(*n*)](https://www.w3school.com.cn/cssref/selector_nth-last-child.asp)             | p:nth-last-child(2)   | 同上，从最后一个子元素开始计数。                     |
| [:nth-of-type(*n*)](https://www.w3school.com.cn/cssref/selector_nth-of-type.asp)                   | p:nth-of-type(2)      | 选择属于其父元素第二个 <p> 元素的每个 <p> 元素。        |
| [:nth-last-of-type(*n*)](https://www.w3school.com.cn/cssref/selector_nth-last-of-type.asp)         | p:nth-last-of-type(2) | 同上，但是从最后一个子元素开始计数。                   |
| [:only-of-type](https://www.w3school.com.cn/cssref/selector_only-of-type.asp)                      | p:only-of-type        | 选择属于其父元素唯一的 <p> 元素的每个 <p> 元素。        |
| [:only-child](https://www.w3school.com.cn/cssref/selector_only-child.asp)                          | p:only-child          | 选择属于其父元素的唯一子元素的每个 <p> 元素。            |
| [:optional](https://www.w3school.com.cn/cssref/selector_optional.asp)                              | input:optional        | 选择不带 "required" 属性的 input 元素。        |
| [:out-of-range](https://www.w3school.com.cn/cssref/selector_out-of-range.asp)                      | input:out-of-range    | 选择值超出指定范围的 input 元素。                 |
| [::placeholder](https://www.w3school.com.cn/cssref/selector_placeholder.asp)                       | input::placeholder    | 选择已规定 "placeholder" 属性的 input 元素。    |
| [:read-only](https://www.w3school.com.cn/cssref/selector_read-only.asp)                            | input:read-only       | 选择已规定 "readonly" 属性的 input 元素。       |
| [:read-write](https://www.w3school.com.cn/cssref/selector_read-write.asp)                          | input:read-write      | 选择未规定 "readonly" 属性的 input 元素。       |
| [:required](https://www.w3school.com.cn/cssref/selector_required.asp)                              | input:required        | 选择已规定 "required" 属性的 input 元素。       |
| [:root](https://www.w3school.com.cn/cssref/selector_root.asp)                                      | :root                 | 选择文档的根元素。                            |
| [::selection](https://www.w3school.com.cn/cssref/selector_selection.asp)                           | ::selection           | 选择用户已选取的元素部分。                        |
| [:target](https://www.w3school.com.cn/cssref/selector_target.asp)                                  | #news:target          | 选择当前活动的 #news 元素。                    |
| [:valid](https://www.w3school.com.cn/cssref/selector_valid.asp)                                    | input:valid           | 选择带有有效值的所有 input 元素。                 |
| [:visited](https://www.w3school.com.cn/cssref/selector_visited.asp)                                | a:visited             | 选择所有已访问的链接。                          |

### 子代选择器与后代选择器的区别

##### 使用后代选择器

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>后代与子代选择器的区别</title>
    <!--<link rel="stylesheet" type="text/css" href="*.css"/>-->
    <script language="javascript" type="text/javascript"></script>
    <style>
        .zero li
        {
            border:1px solid red;
        }
    </style>
</head>
<body>
    <ul class="zero" >
        <li>我是祖先</li>
       <ul>zero
           <li>我是第二代</li>
                <ul>
                    <li>
                    我是第三代
                    </li>
                </ul>
      </ul>
    </ul>
</body>
</html>
```

##### 使用子代选择器

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>后代与子代选择器的区别</title>
    <!--<link rel="stylesheet" type="text/css" href="*.css"/>-->
    <script language="javascript" type="text/javascript"></script>
    <style>
        .zero>li
        {
            border:1px solid red;
        }
    </style>
</head>
<body>
    <ul class="zero" >
        <li>我是祖先</li>
       <ul>zero
           <li>我是第二代</li>
                <ul>
                    <li>
                    我是第三代
                    </li>
                </ul>
      </ul>
    </ul>
</body>
</html>
```

子代选择器与后代选择器的区别是：

后代包含子代，但是后代不全是子代。

##### 验证中会遇到的问题

当使用的css属性是可继承的会导致，使用子代选择器、后代选择器展示的效果是一样的。

##### 可继承的属性：

可继承的样式： font-size, font-family, color, text-indent;

不可继承的样式：border, padding, margin, width, height ;

**同理**，可以使用子代选择器和后代选择器去验证属性是否可继承。

## css的权重

!important > 行内样式>ID选择器 > 类选择器/属性/伪类 > 标签 > 通配符 > 继承 > 浏览器默认属性

!important :无穷

Style:1000

id:0100

Class/伪类:0010

标签:0001

通配符：0000

## **BFC 是什么？如何生成 BFC？BFC 有什么作用？举例说明。**

BFC即块状格式化上下文，BFC 是一个独立的布局环境,可以理解为一个容器,在这个容器中按照一定规则进行物品摆放,并且不会影响其它环境中的物品。如果一个元素符合触发 BFC 的条件，则 BFC 中的元素布局不受外部影响。

创建BFC的方法：浮动元素 、绝对定位元素、display值为 inline-block | flex | inline-flex | table-cell 或 table-caption、overflow值为hidden，auto，scroll

\- 浮动元素 (元素的 `float` 不是 `none`) 

\- 绝对定位元素 (元素具有 `position` 为 `absolute` 或 `fixed`) 

\- 内联块 (元素具有 `display: inline-block`) 

\- 表格单元格 (元素具有 `display: table-cell`，HTML表格单元格默认属性) 

\- 表格标题 (元素具有 `display: table-caption`, HTML表格标题默认属性) 

\- 具有`overflow` 且值不是 `visible` 的块元素 

\- 弹性盒（`flex`或`inline-flex`） 

比如浮动元素会形成BFC，浮动元素内部子元素的主要受该浮动元素影响，两个浮动元素之间是互不影响的。这里有点类似一个BFC就是一个独立的行政单位的意思。可以说BFC就是一个作用范围，把它理解成是一个独立的容器，并且这个容器里box的布局与这个容器外的box毫不相干。

\- `display: flow-root` 

\- `column-span: all` 

#### BFC的约束规则

\- 内部的盒会在垂直方向一个接一个排列（可以看作BFC中有一个的常规流） 

\- 处于同一个BFC中的元素相互影响，可能会发生外边距重叠 

\- BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素，反之亦然 

\- 计算BFC的高度时，考虑BFC所包含的所有元素，连浮动元素也参与计算 

\- 浮动盒区域不叠加到BFC上 

#### BFC可以解决的问题

\- 垂直外边距重叠问题 

\- 去除浮动 

\- 自适用两列布局（`float` + `overflow`） 

## 浮动

### float布局的高度坍塌，如何解决

原因：浮动元素脱离文档流，使包含它的无高度样式的父容器感知不到其存在而发生内容无法撑开高度的现象

解决方法：

**1.给父容器设置高度样式(`height`)**

缺点：只能适用于父容器高度固定的情况下，注定了这种方案很少用；

![](https://img2018.cnblogs.com/i-beta/1163900/202002/1163900-20200202151554886-210169599.png)

给父容器设置高度

**2.因为浮动元素可以感知到浮动元素的存在，所以可以给父容器也设置浮动样式，使其感知到其内的浮动元素**

缺点：给父容器设置了浮动样式后，父容器的父容器也会产生高度坍塌问题；

![](https://img2018.cnblogs.com/i-beta/1163900/202002/1163900-20200202151608600-1383566924.png)

给父容器设置浮动

**3.对父容器设置`overflow: hidden/auto`，触发其BFC**

缺点：设置hidden会导致超出部分直接被隐藏，且不占据文档流位置，而设置auto的话超出部分会生成一个滚动条，影响视觉效果。

比如相对定位的内容会被隐藏

![](https://img2018.cnblogs.com/i-beta/1163900/202002/1163900-20200202151619707-932192551.png)

设置overflow样式触发父容器BFC

**4.使用伪类元素，再用伪类元素清除浮动**

缺点：CSS代码量增多

![](https://img2018.cnblogs.com/i-beta/1163900/202002/1163900-20200202151626970-2069105413.png)

设置伪元素清除浮动

PS：此法要注意的是，必须是`after`伪元素，且该伪类元素必须为`block`元素，原因是`after`伪元素在其父容器的其他标签的最后添加一个元素，渲染顺序排在了其父容器内部的最后，然后设置`block`元素是为了不让其它元素与其一排，再对其设置清除浮动，父容器自然就被这个`after`伪元素撑开了高度

**5.使用块级标签撑开高度，此法和伪类法类似**

缺点：增加了无意义的标签

![](https://img2018.cnblogs.com/i-beta/1163900/202002/1163900-20200202151635152-1092925363.png)

设置标签清除浮动

### 清除浮动

**方法一：使用带clear属性的空元素**

.clear{clear:both;}

**优点：简单，代码少，浏览器兼容性好。**

**缺点：需要添加大量无语义的html元素，代码不够优雅，后期不容易维护。**

**方法二：使用CSS的overflow属性**

overflow:hidden;

overflow:auto;

**方法三：使用CSS的:after伪元素**

**结合 :after 伪元素（注意这不是伪类，而是伪元素，代表一个元素之后最近的元素）和 IEhack ，可以完美兼容当前主流的各大浏览器，这里的 IEhack 指的是触发 hasLayout。**

**给浮动元素的容器添加一个clearfix的class，然后给这个class添加一个:after伪元素实现元素末尾添加一个看不见的块元素（Block element）清理浮动。**

```
.clearfix :before, clear :after{
    content: " ";
    display:table;
}
.clearfix:after{
    clear:both;

//触发BFC，BFC可以清除浮动
}
.clearfix {
    zoom:1;
//触发haslayout，这个属性只有2个值，true代表有自己的布局，false代表继承至父元素
}
```

## 图片预加载和懒加载

### 懒加载

##### 场景：

对于图片过多的页面，为了加快页面加载速度，需要将页面内未出现的可视区域内的图片先不做加载，等到滚动可视区域后再去加载。

##### 原理：

img标签的src属性用来表示图片的URL，当这个属性值不为空时，浏览器就会根据这个值发送请求，如果没有src属性就不会发送请求。所以，在页面加入时将img标签的src指向为空或者指向一个小图片（loading或者缺省图），将真实地址存在一个自定义属性data-src中，当页面滚动时，将可视区域的图片的src值赋为真实的值。

### 预加载

##### 场景：

图鼠标移入一张图片时，换成另一张图片，移出时换回原来的图片，正常做法是，鼠标移入的时候，改变图片的src，但这时就要去加载图片了，会等待一段时间，这样体验不好。预加载的做法是，在页面加载完，鼠标移入之前就通过Image对象把图片加载进缓存了，这样鼠标移入的时候直接从缓存里读取了，速度很快，解决此问题的方案就是实现图片预加载。

##### 原理：

事先把网页的图片记载到本地，之后就直接到缓存中拿图片
实现方法一般有三种：

##### 1）、使用CSS进行图片预加载

原理：将需要加载的图片作为标签的背景图预先加载出来，但是不显示在可视区域内
缺点：加载的图片会同页面的其他内容一起加载，增加了页面的整体加载时间

##### 2）、使用CSS+JS进行图片预加载

为了解决上述问题，可以增加一些JS代码来推迟加载的时间，直到页面加载完毕

##### 3）、使用Ajax实现预加载

使用Ajax方法实现预加载，不仅仅是针对图片的预加载，还会预加载CSS、JS等相关的东西

## 可继承的样式

可继承的样式： font-size, font-family, color, text-indent;

不可继承的样式：border, padding, margin, width, height ;

## ransition 闪屏

**启动硬件加速**

最常用的方式：translate3d、translateZ、transform

opacity 属性/过渡动画（需要动画执行的过程中才会创建合成层，动画没有开始或结束后元素还会回到之前的状态）

will-chang 属性（这个比较偏僻），一般配合opacity与translate使用（而且经测试，除了上述可以引发硬件加速的属性外，其它属性并不会变成复合层）。

弊端：硬件加速会导致 CPU 性能占用量过大，电池电量消耗加大 ；因此尽量避免泛滥使用硬件加速。

## CSS3新特性

新增选择器 p:nth-child（n）{color: rgba（255, 0, 0, 0.75）}

弹性盒模型 display: flex;

多列布局 column-count: 5;

媒体查询 @media （max-width: 480px） {.box: {column-count: 1;}}

个性化字体 @font-face{font-family:BorderWeb;src:url（BORDERW0.eot）；}

颜色透明度 color: rgba（255, 0, 0, 0.75）；

圆角 border-radius: 5px;

渐变 background:linear-gradient（red, green, blue）；

阴影 box-shadow:3px 3px 3px rgba（0, 64, 128, 0.3）；

倒影 box-reflect: below 2px;

文字装饰 text-stroke-color: red;

文字溢出 text-overflow:ellipsis;

背景效果 background-size: 100px 100px;

边框效果 border-image:url（bt_blue.png） 0 10;

##### 转换

旋转 transform: rotate（20deg）；

倾斜 transform: skew（150deg, -10deg）；

位移 transform:translate（20px, 20px）；

缩放 transform: scale（.5）；

平滑过渡 transition: all .3s ease-in .1s;

动画 @keyframes anim-1 {50% {border-radius: 50%;}} animation: anim-1 1s;

## CSS绘图

1.圆形

```
.circle{    
        width: 100px;      
        height: 100px;      
        background-color:red;      
        border-radius: 50%;      
        -moz-border-radius: 50%;      
        -webkit-border-radius: 50%;    
} 
```

2.正方形

```
#square {
    width:100px;
    height: 100px;
    background: red;
}
```

3.长方形

```
#rectangle {
    width: 200px;
    height: 100px;
    background: red;
}
```

4.椭圆

```
#oval {
    width: 200px;
    height: 100px;
    background: red;
    -moz-border-radius: 100px / 50px;
    -webkit-border-radius: 100px / 50px;
    border-radius: 100px / 50px;
}
```

5.上三角

```
#triangle-up {
    width: 0;
    height: 0;
    border-left: 50px solid transparent;
    border-right: 50px solid transparent;
    border-bottom: 100px solid red;
}
```

6.下三角

```
#triangle-down {
   width: 0;
   height: 0;
   border-left: 50px solid transparent;
   border-right: 50px solid transparent;
   border-top: 100px solid red;
}
```

7.左三角

```
#triangle-left {
    width: 0;
    height: 0;
    border-top: 50px solid transparent;
    border-right: 100px solid red;
    border-bottom: 50px solid transparent;
}
```

8.右三角

```
#triangle-right {
    width: 0;
    height: 0;
    border-top: 50px solid transparent;
    border-left: 100px solid red;
    border-bottom: 50px solid transparent;
}
```

9.平行四边形

```
#parallelogram {
    width: 150px;
    height: 100px;
    margin-left:20px;
    -webkit-transform: skew(20deg);
    -moz-transform: skew(20deg);
    -o-transform: skew(20deg);
    background: red;
}
```

10.梯形

```
#trapezoid {
    border-bottom: 100px solid red;
    border-left: 50px solid transparent;
    border-right: 50px solid transparent;
    height: 0;
    width: 100px;
}
```

11.六角形

```
#star-six {
    width: 0;
    height: 0;
    border-left: 50px solid transparent;
    border-right: 50px solid transparent;
    border-bottom: 100px solid red;
    position: relative;
}
#star-six:after {
    width: 0;
    height: 0;
    border-left: 50px solid transparent;
    border-right: 50px solid transparent;
    border-top: 100px solid red;
    position: absolute;
    content: "";
    top: 30px;
    left: -50px;
}
```

12.五角星

```
#star-five {
   margin: 50px 0;
   position: relative;
   display: block;
   color: red;
   width: 0px;
   height: 0px;
   border-right:  100px solid transparent;
   border-bottom: 70px  solid red;
   border-left:   100px solid transparent;
   -moz-transform:    rotate(35deg);
   -webkit-transform: rotate(35deg);
   -ms-transform:     rotate(35deg);
   -o-transform:      rotate(35deg);
}
#star-five:before {
   border-bottom: 80px solid red;
   border-left: 30px solid transparent;
   border-right: 30px solid transparent;
   position: absolute;
   height: 0;
   width: 0;
   top: -45px;
   left: -65px;
   display: block;
   content: '';
   -webkit-transform: rotate(-35deg);
   -moz-transform:    rotate(-35deg);
   -ms-transform:     rotate(-35deg);
   -o-transform:      rotate(-35deg);

}
#star-five:after {
   position: absolute;
   display: block;
   color: red;
   top: 3px;
   left: -105px;
   width: 0px;
   height: 0px;
   border-right: 100px solid transparent;
   border-bottom: 70px solid red;
   border-left: 100px solid transparent;
   -webkit-transform: rotate(-70deg);
   -moz-transform:    rotate(-70deg);
   -ms-transform:     rotate(-70deg);
   -o-transform:      rotate(-70deg);
   content: '';
}
```

13.爱心

```
#heart {
    position: relative;
    width: 100px;
    height: 90px;
}
#heart:before,
#heart:after {
    position: absolute;
    content: "";
    left: 50px;
    top: 0;
    width: 50px;
    height: 80px;
    background: red;
    -moz-border-radius: 50px 50px 0 0;
    border-radius: 50px 50px 0 0;
    -webkit-transform: rotate(-45deg);
       -moz-transform: rotate(-45deg);
        -ms-transform: rotate(-45deg);
         -o-transform: rotate(-45deg);
            transform: rotate(-45deg);
    -webkit-transform-origin: 0 100%;
       -moz-transform-origin: 0 100%;
        -ms-transform-origin: 0 100%;
         -o-transform-origin: 0 100%;
            transform-origin: 0 100%;
}
#heart:after {
    left: 0;
    -webkit-transform: rotate(45deg);
       -moz-transform: rotate(45deg);
        -ms-transform: rotate(45deg);
         -o-transform: rotate(45deg);
            transform: rotate(45deg);
    -webkit-transform-origin: 100% 100%;
       -moz-transform-origin: 100% 100%;
        -ms-transform-origin: 100% 100%;
         -o-transform-origin: 100% 100%;
            transform-origin :100% 100%;
}
```

## css高耗能属性

什么 CSS 属性是高消耗的？就是那些绘制前需要浏览器进行大量计算的属性。

- box-shadows
- border-radius
- transparency
- transform
- CSS filter

时间长: 动画 transition,animation

计算量大: filter, box-shadow, gradient 多层级选择器,图层叠加属性

网络加载: @import

不必要的计算: 触发页面重排

## 拓展问题

**1.如果不用定位能怎么实现重叠盒子的效果？**

使用margin，transform

**2.有什么效果是浮动能做到而flex做不到的？**

float作用于图片实现文字环绕图片。

**3.css margin，padding百分比是相对于谁**

**margin padding 四个方向的参照的都是父元素的宽度**

**4.如果设置background-color: red，那么盒子模型各个部分的背景是什么情况？**

background-color 属性为元素设置一种纯色。这种颜色会填充元素的内容、内边距和边框区域，扩展到元素边框的外边界（但不包括外边距）。如果边框有透明部分（如虚线边框），会透过这些透明部分显示出背景色。

**5.在什么场景下会出现外边距合并？如何合并？如何不让相邻元素外边距合并？给个父子外边距合并的范例？**

兄弟元素合并：当一个元素出现在另一个元素上面时，第一个元素的下外边距与第二个元素的上外边距会发生合并。

父子间合并：当一个元素包含在另一个元素中时（假设没有内边距或边框把外边距分隔开），它们的上和/或下外边距也会发生合并。

空元素：假设有一个空元素，它有外边距，但是没有边框或填充。在这种情况下，上外边距与下外边距就碰到了一起，它们会发生合并。

​        如何合并：

- 两个相邻的外边距都是正数时，合并结果是它们两者之间较大的值。

- 两个相邻的外边距都是负数时，合并结果是两者绝对值的较大值。

- 两个外边距一正一负时，合并结果是两者的相加的和。
  
  解决办法：

- 对于兄弟元素：设置浮动，display：inline-block或使其父元素均形成BFC可防止边距合并（如overflow:hidden）

- 对于父子间的元素：可以给父元素设置border或padding属性来防止合并。

- 对于空元素：设置border或padding属性或overflow：hidden等（形成BFC）来防止合并。

**6.单行文本溢出加 ... 如何实现？**

​    text-overflow: ellipsis;

**7.雪碧图怎么操作的？雪碧图在移动端的自适应？**

background-position的值（默认为（0，0），也就是图片的左上角）

```
//方法一，可以使用网易转换rem的那一套，配合scss函数使用
background-size: x y; //为实际上完成后的雪碧图的宽高
@function torem($px){
    @return $px / 64px *1rem;
}

@mixin spImg($url, $left, $top. $imgWidth){
    background: url($url) no-repeat $left $top;
    background-size: $imgWidth;
}

//方法二，使用px，使用插件postcss-px-to-viewport
```

**8.文本超过3行省略**

```
display: -webkit-box;
-webkit-box-orient: vertical;
-webkit-line-clamp: 3;
overflow: hidden;
text-overflow: ellipsis;
```

**9.改变 placeholder 的字体颜色大小**

pc可以 移动端有问题

```
input::-webkit-input-placeholder {
    /* WebKit browsers */
    font-size:14px;
    color: #333;
}
input::-moz-placeholder {
    /* Mozilla Firefox 19+ */
    font-size:14px;
    color: #333;
}
input:-ms-input-placeholder {
    /* Internet Explorer 10+ */
    font-size:14px;
    color: #333;
}
```

**10.CSS中如何解决子元素继承父元素的opacity属性**

这里有两个方案，使用rgba()间接的设定opacity的值，这个属性不会向下继承，或者既然opacity会被子级元素继承，那就把opacity属性放到同级元素实现，下面通过示例具体说说这两种方式：

1.使用rgba()间接的设定opacity

rgba()有四个参数，最后一个参数就是opacity的值，和opacity单独设定效果一样，但是这个是有background属性来控制的，background不会向下继承，所以就解决这个问题啦，示例如下：

```
<!DOCTYPE html>
<html>
<head>
 <meta charset="utf-8">
 <title>opacity</title>
</head>
<style type="text/css">
 .opacity{
  position: relative;
  width: 400px;
  height: 300px;
  color: black;
  background: rgba(255,0,0,0.5);
 }
 .opacity-child{
 }
 .normal{
  width: 400px;
  height: 300px;
  background: red;
  color: black;
 }
 </style>
<body>
 <div class="opacity">
  <div class="opacity-child">子元素会继承父级元素的opacity属性</div>
 </div>
 <div class="normal">子元素会继承父级元素的opacity属性</div>
</body>
</html>
```

2.把opacity属性放到同级元素实现

```
<!DOCTYPE html>
<html>
<head>
 <meta charset="utf-8">
 <title>opacity</title>
</head>
<style type="text/css">
 .opacity{
  position: relative;
  width: 400px;
  height: 300px;
  color: black;
  }
  .opacity-child{
  position: relative;
  z-index: 1;
  width: 100%;
  height: 100%;
 }
  .opacity-child-background{
  position: absolute;
  top : 0px;
  left: 0px;
  width: 100%;
  height: 100%;
  background: red;
  opacity: 0.5;
  z-index: 0;
 }
  .normal{
  width: 400px;
  height: 300px;
  background: red;
  color: black;
 }
 </style>
<body>
 <div class="opacity">
  <div class="opacity-child">子元素会继承父级元素的opacity属性</div>
  <div class="opacity-child-background"></div>
 </div>
 <div class="normal">子元素会继承父级元素的opacity属性</div>
</body>
</html>
```

3.透明实现的另一个技巧

```
filter:alpha(Opacity=0);
```

**11.实现动画，元素先向右移动200px，再返回原点，一共移动n次**

```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8"> 
<style> 
div
{
    width:100px;
    height:100px;
    background:red;
    position:relative;
    animation:myfirst 5s;
    animation-iteration-count: 5;
}

@keyframes myfirst
{
    0%   {background:red; left:0px; top:0px;}
    50%  {background: red; left:200px; top:0px;}
    100% {background:red; left:0px; top:0px;}
}
</style>
</head>
<body>
</body>
</html>
```

**12.CSS画一个边长小于1px的线段或div怎么实现**

用transform的scale去缩小

**13.实现16：9的盒子，不定宽**

```css
.a {
    width: 100vw
    height: 100/16*9vw
}
```

margin属性设置为百分比，相对于宽度的百分比而言，从而实现宽高等比的效果

margin-top的%值是相对于父元素的宽度的。

**14.如何使用 css 实现轮播？**

background-position\animation

animation

transform:translate\animation

position:relataive\animation

**15.知道div为父元素宽度50%，将div设置为正方形**

```html
<!!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8"/>
        <title>正方形</title>
        <style>
            body,html{
                width: 100%;
                height: 100%;
            }
            .box{
                width: 20%;
                height: 0;
                padding-top: 20%;
                background-color: aqua;
            }
        </style>
    </head>
    <body>
        <div class="box"></div>
    </body>
</html>
```

**16.css flex 实现骰子**

```html
<!DOCTYPE html>
<html lang="en">

<head>
 <meta charset="UTF-8">
 <meta http-equiv="X-UA-Compatible" content="IE=edge">
 <meta name="viewport" content="width=device-width, initial-scale=1.0">
 <title>Document</title>
</head>
<style>
 /*骰子点数样式*/

 .item {
 display: block;
 width: 24px;
 height: 24px;
 border-radius: 50%;
 margin: 4px;
 background-color: #333;
 box-shadow: inset 0 3px #111, inset 0 -3px #555;
    }
 /*骰子样式*/

    [class$='box'] {
 margin: 16px;
 padding: 4px;
 background-color: #e7e7e7;
 width: 104px;
 height: 104px;
 /*图片的设置*/
 object-fit: contain;
 box-shadow: inset 0 5px white, inset 0 -5px #bbb, inset 5px 0 #d7d7d7, inset -5px 0 #d7d7d7;
 border-radius: 10%;
    }

 #box1 {
 display: flex;
 justify-content: center;
 /* align-items: center; */
    }

 #box1 .item {
 align-self: center;
    }

 #box2 {
 display: flex;
 flex-direction: row;
 justify-content: space-between;
    }

 #box2 .item:nth-child(2) {
 align-self: flex-end;
    }

 #box3 {
 display: flex;
 flex-direction: row;
 justify-content: space-between;
    }

 #box3 .item:nth-child(2) {
 align-self: center;
    }

 #box3 .item:nth-child(3) {
 align-self: flex-end;
    }

 #box3 {
 display: flex;
 flex-direction: row;
 justify-content: space-between;
    }

 #box3 .item:nth-child(2) {
 align-self: center;
    }

 #box3 .item:nth-child(3) {
 align-self: flex-end;
    }

 #box4 {
 display: flex;
 flex-direction: row;
 flex-wrap: wrap;
 align-content: space-between;
    }

 #box4 .item:nth-child(1) {
 margin-right: 40px;
    }

 #box4 .item:nth-child(3) {
 margin-right: 40px;
    }

 #box5 {
 display: flex;
 flex-direction: row;
 flex-wrap: wrap;
 align-content: space-between;
 justify-content: center;
    }

 #box5 .item:nth-child(1) {
 margin-right: 40px;
    }

 #box5 .item:nth-child(3) {
 margin-left: 40px;
 margin-right: 40px;
 align-self: center;
    }

 #box5 .item:nth-child(4) {
 margin-right: 40px;
    }

 #box6 {
 display: flex;
 flex-wrap: wrap;
 align-content: space-between;
 justify-content: space-between;
    }
</style>

<body>
 <div class="box" id="box1">
 <span class="item"></span>
 </div>
 <div class="box" id="box2">
 <span class="item"></span>
 <span class="item"></span>
 </div>
 <div class="box" id="box3">
 <span class="item"></span>
 <span class="item"></span>
 <span class="item"></span>
 </div>
 <div class="box" id="box4">
 <span class="item"></span>
 <span class="item"></span>
 <span class="item"></span>
 <span class="item"></span>
 </div>
 <div class="box" id="box5">
 <span class="item"></span>
 <span class="item"></span>
 <span class="item"></span>
 <span class="item"></span>
 <span class="item"></span>
 </div>
 <div class="box" id="box6">
 <span class="item"></span>
 <span class="item"></span>
 <span class="item"></span>
 <span class="item"></span>
 <span class="item"></span>
 <span class="item"></span>
 </div>
</body>

</html>
```

**17.拖拽**

```html
<!DOCTYPE HTML>
<html>
<head>
<script>
function allowDrop(ev) {
    ev.preventDefault();
}

function drag(ev) {
    ev.dataTransfer.setData("text", ev.target.id);
}

function drop(ev) {
    ev.preventDefault();
    var data = ev.dataTransfer.getData("text");
    ev.target.appendChild(document.getElementById(data));
}
</script>
</head>
<body>

<div id="div1" ondrop="drop(event)" ondragover="allowDrop(event)"></div>

<img id="drag1" src="img_logo.gif" draggable="true" ondragstart="drag(event)" width="336" height="69">

</body>
</html>
```

dragstart：拖拽开始时在被拖拽元素上触发此事件,监听器需要设置拖拽所需数据,从操作系统拖拽文件到浏览器时不触发此事件.  
dragenter：拖拽鼠标进入元素时在该元素上触发,用于给拖放元素设置视觉反馈,如高亮  
dragover：拖拽时鼠标在目标元素上移动时触发.监听器通过阻止浏览器默认行为设置元素为可拖放元素.  
dragleave：拖拽时鼠标移出目标元素时在目标元素上触发.此时监听器可以取消掉前面设置的视觉效果.  
drag：拖拽期间在被拖拽元素上连续触发  
drop：鼠标在拖放目标上释放时,在拖放目标上触发.此时监听器需要收集数据并且执行所需操作.如果是从操作系统拖放文件到浏览器,需要取消浏览器默认行为.  
dragend：鼠标在拖放目标上释放时,在拖拽元素上触发.将元素从浏览器拖放到操作系统时不会触发此事件.

https://segmentfault.com/a/1190000012427787

**18.幽灵空白结点**

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>css之幽灵空白节点</title>
    <style>
        div{
            background: #000;
        }
        span{
            display: inline-block;
        }
    </style>
</head>
<body>
    <div>
        <span></span>
    </div>
</body>
</html>
```

原因:
因为它存在每个“行框盒子”前面，同时具有该元素字体和行高属性的0宽度的内联盒。

解决方法:

1.img设置display为block；
2.父容器（如示例中的div）font-size设置为0；
