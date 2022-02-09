# fe-learning
# my-problems

记录问题

小数四舍五入至整数


```javascript
function toSmallRounding(nums) {
  return Math.round(nums.toFixed(1))
}
```

倒计时切换至后台后会被暂停的问题：

由于倒计时是基于requestAnimationFrame进行开发的，而在大多数浏览器里，当requestAnimationFrame() 运行在后台标签页或者隐藏的<iframe> 里时，requestAnimationFrame() 会被暂停调用以提升性能和电池寿命，所以会导致该问题。

可以采用监听页面可见性来刷新倒计时解决该问题

```javascript
// 应用demo
// startSimulation 和 pauseSimulation 在其他地方定义
function handleVisibilityChange() {
  if (document.hidden) {
    pauseSimulation();
  } else  {
    startSimulation();
  }
}

document.addEventListener("visibilitychange", handleVisibilityChange, false);
```

相关API

[`Document.hidden`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/hidden) 只读

如果页面处于被认为是对用户隐藏状态时返回true，否则返回false。

[`Document.visibilityState`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/visibilityState) 只读

是一个用来展示文档当前的可见性的[`DOMString`](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMString) 。该属性的值为以下值之一：

- `visible` : 页面内容至少是部分可见。 在实际中，这意味着页面是非最小化窗口的前景选项卡。
- `hidden` : 页面内容对用户不可见。 在实际中，这意味着文档可以是一个后台标签，或是最小化窗口的一部分，或是在操作系统锁屏激活的状态下。
- `prerender` : 页面内容正在被预渲染且对用户是不可见的(被document.hidden当做隐藏). 文档可能初始状态为prerender，但绝不会从其它值转为该值。>
- 注释：有的浏览器不支持此功能`unloaded` : 页面正在从内存中卸载。
- 注释：有的浏览器不支持此功能

[`Document.onvisibilitychange`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/onvisibilitychange)

[`EventListener`](https://developer.mozilla.org/zh-CN/docs/Web/API/EventListener) 提供在`visibilitychange (en-US)` 事件被触发时要调用的代码。



1.边界值问题

​	使用text-overflow,如果要使这个属性生效必须使元素有宽度。

```css
*{
	text-overflow: ellipsis;
  overflow: hidden;
  white-space: nowrap;
}
```

2.布局问题

​	absolute、fixed

​	flex

可以考虑少使用div，多使用类似ul、li的元素。

设计的原则是：能够让元素在正常的文档流下布局则不适用absolute，如果必要使用absolute则必须要注意子绝父相的道理。

3.禁止双击放大

```javascript
<meta name="viewport" content="width=device-width,inital-scale=1.0,maximum-scale=1.0,user-scale=0">

window.onload=function() {
  document.addEventListener('touchstart',function (event){
    if(event.touches.length>1){
      event.preventDefault();
    }
  });
  var lastTouchEnd = 0;
  document.addEventListener('touchend',function (event){
    var now = (new Date()).getTime();
    if(now -lastTouchEnd <= 300){
      event.preventDefault();
    }
    lastTouchEnd = now;
  },false);
  document.addEventListener('gesturestart',function (event){
    event.preventDefault();
  });
}
```

4.CSS overscroll-behavior让滚动嵌套时父滚动不触发、scroll-behavior用于滚动平滑、scroll snap用于滚动定位加上文本的滚动边界。

在模板字符串中，空格、缩进、换行都会被保留

  
1.接口返回，统一结构

2.try-catch融入

3.sql本身字段

				
				
				# 深入解析CSS

每一个CSS属性都有初始（默认）值。如果将initial值赋给某个属性，那么就会有效地将其重置为默认值，这种操作相当于硬复位了该值。

声明display: initial等价于display: inline。不管应用于哪种类型的元素，它都不会等于display: block。这是因为initial重置为属性的初始值，而不是元素的初始值。inline才是display属性的初始值。

##### 简写属性会默默覆盖其他样式

大多数简写属性可以省略一些值，只指定我们关注的值。但是要知道，这样做仍然会设置省略的值，即它们会被隐式地设置为初始值。这会默默覆盖在其他地方定义的样式。

##### 简写值的顺序

##### 上、右、下、左

指定三个值时，左边和右边都会使用第二个值。指定两个值时，上边和下边会使用第一个值。如果只指定一个值，那么四个方向都会使用这个值。

background-position:25% 75%则先指定水平方向的右/左属性值，然后才是垂直方向的上/下属性值。

box-shadow先指定x值再指定y值



CSS为网页带来了后期绑定（late-binding）的样式：直到内容和样式都完成了，二者才会结合起来。

响应式——在CSS中指的是样式能够根据浏览器窗口的大小有不同的“响应”。这要求有意地考虑任何尺寸的手机、平板设备，或者桌面屏幕。



视口——浏览器窗口里网页可见部分的边框区域。它不包括浏览器的地址栏、工具栏、状态栏。

❑ vh：视口高度的1/100。

❑ vw：视口宽度的1/100。

❑ vmin：视口宽、高中较小的一方的1/100（IE9中叫vm，而不是vmin）。

❑ vmax：视口宽、高中较大的一方的1/100（本书写作时IE和Edge均不支持vmax）



使用无单位的数值时，继承的是声明值，即在每个继承子元素上会重新算它的计算值。这样得到的结果几乎总是我们想要的。我们可以用一个无单位的数值给body设置行高，之后就不用修改了，除非有些地方想要不一样的行高。



### css自定义属性

变量名前面必须有两个连字符（--），用来跟CSS属性区分，剩下的部分可以随意命名。

变量必须在一个声明块内声明。这里使用了：root选择器，因此该变量可以在整个网页使用，稍后会解释这一点。

#### 使用自定义属性

```css
:root {
  --main-font: Helvetica, Arial, sans-serif;
}

p {
  font-family: var(--main-font);
}
```

var()函数接受第二个参数，它指定了备用值。如果第一个参数指定的变量未定义，那么就会使用第二个值。

说明如果var()函数算出来的是一个非法值，对应的属性就会设置为其初始值。比如，如果在padding: var(--brand-color)中的变量算出来是一个颜色，它就是一个非法的内边距值。这种情况下，内边距会设置为0。

css自定义属性，它真正的意义在于，自定义属性的声明能够层叠和继承：可以在多个选择器中定义相同的变量，这个变量在网页的不同地方有不同的值。（可以通过javascript对自定义属性进行更改）

```javascript
var rootElement = document.documentElement;
var styles = getComputedStyle(rootElement);
var mainColor = styles.getPropertyValue('--main-bg');

var rootElement = document.documentElement;
rootElement.style.setProperty('--main-bg', '#cdf');
```

普通文档流——指的是网页元素的默认布局行为。行内元素跟随文字的方向从左到右排列，当到达容器边缘时会换行。块级元素会占据完整的一行，前后都有换行。

用百分比指定高度存在问题。百分比参考的是元素容器块的大小，但是容器的高度通常是由子元素的高度决定的。这样会造成死循环，浏览器处理不了，因此它会忽略这个声明。要想让百分比高度生效，必须给父元素明确定义一个高度。



可以用表格元素的border-spacing属性来定义单元格的间距。该属性接受两个长度值：水平间距和垂直间距。（也可以将这两个长度值指定为同一值。）可以给容器加上border-spacing:1.5em 0，但这会产生一个特殊的副作用：这个值也会作用于表格的外边缘。这样两列就无法跟头部左右对齐了。



vertical-align声明只会影响行内元素或者table-cell元素。对于行内元素，它控制着该元素跟同一行内其他元素之间的对齐关系。比如，可以用它控制一个行内的图片跟相邻的文字对齐。对于显示为table-cell的元素，vertical-align控制了内容在单元格内的对齐。如果你的页面用了CSS表格布局，那么可以用vertical-align来实现垂直居中。
