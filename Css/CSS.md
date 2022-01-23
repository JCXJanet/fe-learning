## 布局

display: flex

position:relative  相对于其正常位置进行定位。

position:absolute 相对于 static 定位以外的第一个父元素进行定位。

position:fixed 相对于浏览器窗口进行定位。

position:sticky 基于用户的滚动位置来定位。

使用场景：顶部导航栏，backtop

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

## 水平垂直居中

##### 水平

1. 若是行内元素, 给其父元素设置 text-align:center,即可实现行内元素水平居中.
2. 若是块级元素, 该元素设置 margin:0 auto即可.
3. 使用flex 2012年版本布局, 可以轻松的实现水平居中, 子元素设置如下:

```
.son{ 
    display: flex; 
    justify-content: center; 
} 
```

1. 使用CSS3中新增的transform属性, 子元素设置如下:

```
son{ 
    position:absolute; 
    left:50%; 
    transform:translate(-50%,0); 
} 
```

1. 使用绝对定位方式, 以及负值的margin-left, 子元素设置如下:

```
.son{ 
    position:absolute; 
    width:固定; 
    left:50%; 
    margin-left:-0.5宽度; 
} 
```

##### **垂直居中**

1. 若元素是单行文本, 则可设置 line-height 等于父元素高度

元素高度不定

1. 可用 **vertical-align** 属性, 而vertical-align只有在父层为 td 或者 th 时, 才会生效, 对于其他块级元素, 例如 div、p 等, 默认情况是不支持的. 为了使用vertical-align, 我们需要设置父元素display:table, 子元素 display:table-cell;vertical-align:middle;

**优点**

元素高度可以动态改变, 不需再CSS中定义, 如果父元素没有足够空间时, 该元素内容也不会被截断.

**缺点**

IE6~7, 甚至IE8 beta中无效.

1. 父元素做如下设置即可保证子元素垂直居中:

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

1. 可用 **transform** , 设置父元素相对定位(position:relative), 子元素如下css样式:

```
.son{ 
    position:absolute; 
    top:50%; 
    -webkit-transform: translate(-50%,-50%);  
    -ms-transform: translate(-50%,-50%); 
    transform: translate(-50%,-50%); 
} 
```

1. 设置父元素相对定位(position:relative), 子元素如下css样式:

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

## css选择器

| 选择器                                                       | 例子                  | 例子描述                                             |
| :----------------------------------------------------------- | :-------------------- | :--------------------------------------------------- |
| [.*class*](https://www.w3school.com.cn/cssref/selector_class.asp) | .intro                | 选择 class="intro" 的所有元素。                      |
| .*class1*.*class2*                                           | .name1.name2          | 选择 class 属性中同时有 name1 和 name2 的所有元素。  |
| .*class1* .*class2*                                          | .name1 .name2         | 选择作为类名 name1 元素后代的所有类名 name2 元素。   |
| [#*id*](https://www.w3school.com.cn/cssref/selector_id.asp)  | #firstname            | 选择 id="firstname" 的元素。                         |
| [*](https://www.w3school.com.cn/cssref/selector_all.asp)     | *                     | 选择所有元素。                                       |
| [*element*](https://www.w3school.com.cn/cssref/selector_element.asp) | p                     | 选择所有 <p> 元素。                                  |
| [*element*.*class*](https://www.w3school.com.cn/cssref/selector_element_class.asp) | p.intro               | 选择 class="intro" 的所有 <p> 元素。                 |
| [*element*,*element*](https://www.w3school.com.cn/cssref/selector_element_comma.asp) | div, p                | 选择所有 <div> 元素和所有 <p> 元素。                 |
| [*element* *element*](https://www.w3school.com.cn/cssref/selector_element_element.asp) | div p                 | 选择 <div> 元素内的所有 <p> 元素。                   |
| [*element*>*element*](https://www.w3school.com.cn/cssref/selector_element_gt.asp) | div > p               | 选择父元素是 <div> 的所有 <p> 元素。                 |
| [*element*+*element*](https://www.w3school.com.cn/cssref/selector_element_plus.asp) | div + p               | 选择紧跟 <div> 元素的首个 <p> 元素。                 |
| [*element1*~*element2*](https://www.w3school.com.cn/cssref/selector_gen_sibling.asp) | p ~ ul                | 选择前面有 <p> 元素的每个 <ul> 元素。                |
| [[*attribute*\]](https://www.w3school.com.cn/cssref/selector_attribute.asp) | [target]              | 选择带有 target 属性的所有元素。                     |
| [[*attribute*=*value*\]](https://www.w3school.com.cn/cssref/selector_attribute_value.asp) | [target=_blank]       | 选择带有 target="_blank" 属性的所有元素。            |
| [[*attribute*~=*value*\]](https://www.w3school.com.cn/cssref/selector_attribute_value_contain.asp) | [title~=flower]       | 选择 title 属性包含单词 "flower" 的所有元素。        |
| [[*attribute*\|=*value*\]](https://www.w3school.com.cn/cssref/selector_attribute_value_start.asp) | [lang\|=en]           | 选择 lang 属性值以 "en" 开头的所有元素。             |
| [[*attribute*^=*value*\]](https://www.w3school.com.cn/cssref/selector_attr_begin.asp) | a[href^="https"]      | 选择其 src 属性值以 "https" 开头的每个 <a> 元素。    |
| [[*attribute*$=*value*\]](https://www.w3school.com.cn/cssref/selector_attr_end.asp) | a[href$=".pdf"]       | 选择其 src 属性以 ".pdf" 结尾的所有 <a> 元素。       |
| [[*attribute**=*value*\]](https://www.w3school.com.cn/cssref/selector_attr_contain.asp) | a[href*="w3schools"]  | 选择其 href 属性值中包含 "abc" 子串的每个 <a> 元素。 |
| [:active](https://www.w3school.com.cn/cssref/selector_active.asp) | a:active              | 选择活动链接。                                       |
| [::after](https://www.w3school.com.cn/cssref/selector_after.asp) | p::after              | 在每个 <p> 的内容之后插入内容。                      |
| [::before](https://www.w3school.com.cn/cssref/selector_before.asp) | p::before             | 在每个 <p> 的内容之前插入内容。                      |
| [:checked](https://www.w3school.com.cn/cssref/selector_checked.asp) | input:checked         | 选择每个被选中的 <input> 元素。                      |
| [:default](https://www.w3school.com.cn/cssref/selector_default.asp) | input:default         | 选择默认的 <input> 元素。                            |
| [:disabled](https://www.w3school.com.cn/cssref/selector_disabled.asp) | input:disabled        | 选择每个被禁用的 <input> 元素。                      |
| [:empty](https://www.w3school.com.cn/cssref/selector_empty.asp) | p:empty               | 选择没有子元素的每个 <p> 元素（包括文本节点）。      |
| [:enabled](https://www.w3school.com.cn/cssref/selector_enabled.asp) | input:enabled         | 选择每个启用的 <input> 元素。                        |
| [:first-child](https://www.w3school.com.cn/cssref/selector_first-child.asp) | p:first-child         | 选择属于父元素的第一个子元素的每个 <p> 元素。        |
| [::first-letter](https://www.w3school.com.cn/cssref/selector_first-letter.asp) | p::first-letter       | 选择每个 <p> 元素的首字母。                          |
| [::first-line](https://www.w3school.com.cn/cssref/selector_first-line.asp) | p::first-line         | 选择每个 <p> 元素的首行。                            |
| [:first-of-type](https://www.w3school.com.cn/cssref/selector_first-of-type.asp) | p:first-of-type       | 选择属于其父元素的首个 <p> 元素的每个 <p> 元素。     |
| [:focus](https://www.w3school.com.cn/cssref/selector_focus.asp) | input:focus           | 选择获得焦点的 input 元素。                          |
| [:fullscreen](https://www.w3school.com.cn/cssref/selector_fullscreen.asp) | :fullscreen           | 选择处于全屏模式的元素。                             |
| [:hover](https://www.w3school.com.cn/cssref/selector_hover.asp) | a:hover               | 选择鼠标指针位于其上的链接。                         |
| [:in-range](https://www.w3school.com.cn/cssref/selector_in-range.asp) | input:in-range        | 选择其值在指定范围内的 input 元素。                  |
| [:indeterminate](https://www.w3school.com.cn/cssref/selector_indeterminate.asp) | input:indeterminate   | 选择处于不确定状态的 input 元素。                    |
| [:invalid](https://www.w3school.com.cn/cssref/selector_invalid.asp) | input:invalid         | 选择具有无效值的所有 input 元素。                    |
| [:lang(*language*)](https://www.w3school.com.cn/cssref/selector_lang.asp) | p:lang(it)            | 选择 lang 属性等于 "it"（意大利）的每个 <p> 元素。   |
| [:last-child](https://www.w3school.com.cn/cssref/selector_last-child.asp) | p:last-child          | 选择属于其父元素最后一个子元素每个 <p> 元素。        |
| [:last-of-type](https://www.w3school.com.cn/cssref/selector_last-of-type.asp) | p:last-of-type        | 选择属于其父元素的最后 <p> 元素的每个 <p> 元素。     |
| [:link](https://www.w3school.com.cn/cssref/selector_link.asp) | a:link                | 选择所有未访问过的链接。                             |
| [:not(*selector*)](https://www.w3school.com.cn/cssref/selector_not.asp) | :not(p)               | 选择非 <p> 元素的每个元素。                          |
| [:nth-child(*n*)](https://www.w3school.com.cn/cssref/selector_nth-child.asp) | p:nth-child(2)        | 选择属于其父元素的第二个子元素的每个 <p> 元素。      |
| [:nth-last-child(*n*)](https://www.w3school.com.cn/cssref/selector_nth-last-child.asp) | p:nth-last-child(2)   | 同上，从最后一个子元素开始计数。                     |
| [:nth-of-type(*n*)](https://www.w3school.com.cn/cssref/selector_nth-of-type.asp) | p:nth-of-type(2)      | 选择属于其父元素第二个 <p> 元素的每个 <p> 元素。     |
| [:nth-last-of-type(*n*)](https://www.w3school.com.cn/cssref/selector_nth-last-of-type.asp) | p:nth-last-of-type(2) | 同上，但是从最后一个子元素开始计数。                 |
| [:only-of-type](https://www.w3school.com.cn/cssref/selector_only-of-type.asp) | p:only-of-type        | 选择属于其父元素唯一的 <p> 元素的每个 <p> 元素。     |
| [:only-child](https://www.w3school.com.cn/cssref/selector_only-child.asp) | p:only-child          | 选择属于其父元素的唯一子元素的每个 <p> 元素。        |
| [:optional](https://www.w3school.com.cn/cssref/selector_optional.asp) | input:optional        | 选择不带 "required" 属性的 input 元素。              |
| [:out-of-range](https://www.w3school.com.cn/cssref/selector_out-of-range.asp) | input:out-of-range    | 选择值超出指定范围的 input 元素。                    |
| [::placeholder](https://www.w3school.com.cn/cssref/selector_placeholder.asp) | input::placeholder    | 选择已规定 "placeholder" 属性的 input 元素。         |
| [:read-only](https://www.w3school.com.cn/cssref/selector_read-only.asp) | input:read-only       | 选择已规定 "readonly" 属性的 input 元素。            |
| [:read-write](https://www.w3school.com.cn/cssref/selector_read-write.asp) | input:read-write      | 选择未规定 "readonly" 属性的 input 元素。            |
| [:required](https://www.w3school.com.cn/cssref/selector_required.asp) | input:required        | 选择已规定 "required" 属性的 input 元素。            |
| [:root](https://www.w3school.com.cn/cssref/selector_root.asp) | :root                 | 选择文档的根元素。                                   |
| [::selection](https://www.w3school.com.cn/cssref/selector_selection.asp) | ::selection           | 选择用户已选取的元素部分。                           |
| [:target](https://www.w3school.com.cn/cssref/selector_target.asp) | #news:target          | 选择当前活动的 #news 元素。                          |
| [:valid](https://www.w3school.com.cn/cssref/selector_valid.asp) | input:valid           | 选择带有有效值的所有 input 元素。                    |
| [:visited](https://www.w3school.com.cn/cssref/selector_visited.asp) | a:visited             | 选择所有已访问的链接。                               |

## css的权重

!important > 行内样式>ID选择器 > 类选择器/属性/伪类 > 标签 > 通配符 > 继承 > 浏览器默认属性

!important :无穷

Style:1000

id:0100

Class/伪类:0010

标签:0001

通配符：0000

## 拓展问题

1.如果不用定位能怎么实现重叠盒子的效果？

使用margin，transform

2.有什么效果是浮动能做到而flex做不到的？

float作用于图片实现文字环绕图片。

3.css margin，padding百分比是相对于谁

**margin padding 四个方向的参照的都是父元素的宽度**