css3为了区分伪类和伪元素，伪元素采用双冒号写法。

*常见伪类——:hover,:link,:active,:target,:not(),:focus。*

*常见伪元素——::first-letter,::first-line,::before,::after,::selection。*

::before和::after下特有的content，用于在css渲染中向元素逻辑上的头部或尾部添加内容

所以不要用:before或:after展示有实际意义的内容，尽量使用它们显示修饰性内容，例如图标。

举例：网站有些联系电话，希望在它们前加一个icon☎，就可以使用:before伪元素，如下：

```
<!DOCTYPE html>
<meta charset="utf-8" />
<style type="text/css">
    .phoneNumber::before {
    content:'\260E';
    font-size: 15px;
}
</style>
<p class="phoneNumber">12345645654</p>
```

![img](https://images0.cnblogs.com/blog2015/315302/201508/101728385358643.png)

#### Content属性

::before和::after必须配合content属性来使用，content用来定义插入的内容，content必须有值，至少是空。默认情况下，伪类元素的display是默认值inline，可以通过设置display:block来改变其显示。

content可取以下值。

##### 1.string

使用引号包一段字符串，将会向元素内容中添加字符串。如：a:after{content:""}

```
<!DOCTYPE html>
<meta charset="utf-8" />
<style type="text/css">
p::before{
    content: "《";
    color: blue;
}
p::after{
    content: "》";
    color: blue;
}
</style>
<p>平凡的世界</p>
```

![img](https://images0.cnblogs.com/blog2015/315302/201508/110929133328022.png)

##### 2.attr()

通过attr()调用当前元素的属性，比如将图片alt提示文字或者链接的href地址显示出来。

```
<style type="text/css">
a::after{
    content: "(" attr(href) ")";
}
</style>
<a href="http://www.cnblogs.com/starof">starof</a>
```

![img](https://images0.cnblogs.com/blog2015/315302/201508/110937215201245.png)

##### 3.url()/uri()

用于引用媒体文件。

举例：“百度”前面给出一张图片，后面给出href属性。

```
<style>
a::before{
    content: url("https://www.baidu.com/img/baidu_jgylogo3.gif");
}
a::after{
    content:"("attr(href)")";
}
a{
    text-decoration: none;
}
</style>
---------------------------
<body>
<a href="http://www.baidu.com">百度</a>
</body>
```

![img](https://images0.cnblogs.com/blog2015/315302/201505/041517505323703.png)

##### 4、counter()

调用计数器，可以不使用列表元素实现序号功能。

配合counter-increment和counter-reset属性使用：

h2:before { counter-increment: chapter; content: "Chapter " counter(chapter) ". " }

```
<style>
body{
    counter-reset: section;
}
h1{
    counter-reset: subsection;
}
h1::before{
    counter-increment:section;
    content:counter(section) "、";
}
h2::before{
    counter-increment:subsection;
    content: counter(section) "." counter(subsection) "、";
}
</style>
------------------------------------------------
<body>
<h1>HTML tutorials</h1>
<h2>HTML Tutorial</h2>
<h2>XHTML Tutorial</h2>
<h2>CSS Tutorial</h2>

<h1>Scripting tutorials</h1>
<h2>JavaScript</h2>
<h2>VBScript</h2>

<h1>XML tutorials</h1>
<h2>XML</h2>
<h2>XSL</h2>

</body>
```

![img](https://images0.cnblogs.com/blog2015/315302/201505/041646256106350.png)

#### 使用

##### 1.清除浮动

```
.cf::before,
.cf::after {
    content: " ";
    display: table; 
}
.cf::after {
    clear: both;
}
.cf {
    *zoom: 1;
}
```

##### 2、模拟float:center的效果

float没有center这个取值，但是可以通过伪类来模拟实现。

这个效果实现很有意思，左右通过::before float各自留出一半图片的位置，再把图片绝对定位上去。

```
#page-wrap { width: 60%; margin: 40px auto; position: relative; }
#logo { position: absolute; top: 0; left: 50%; margin-left: -125px; }
#l, #r { width: 49%; }
#l { float: left; }
#r { float: right; }
#l:before, #r:before { content: ""; width: 125px; height: 250px; }
#l:before { float: right; }
#r:before { float: left; }
```

```
<!DOCTYPE html>
<html>
<head>
    <meta charset='UTF-8'>
    <title>Float Both</title>
    <style>
    * {
        margin: 0;
        padding: 0;
    }
    body {
        font: 14px/1.8 Georgia, serif;
    }
        #page-wrap { width: 60%; margin: 40px auto; position: relative; }
        #logo { position: absolute; top: 0; left: 50%; margin-left: -125px; }
        #l, #r { width: 49%; }
        #l { float: left; }
        #r { float: right; }
        #l:before, #r:before { content: ""; width: 125px; height: 250px; }
        #l:before { float: right; }
        #r:before { float: left; }
    </style>
</head>
<body>
    <div id="page-wrap">
        <img src="img/cat.jpg" id="logo">
        <div id="l">
            <p>
                Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Vestibulum tortor quam, feugiat vitae, ultricies eget, tempor sit amet, ante. Donec eu libero sit amet quam egestas semper. Aenean ultricies mi vitae est. Mauris placerat eleifend leo. Quisque sit amet est et sapien ullamcorper pharetra. Vestibulum erat wisi, condimentum sed, commodo vitae, ornare sit amet, wisi. Aenean fermentum, elit eget tincidunt condimentum, eros ipsum rutrum orci, sagittis tempus lacus enim ac dui. Donec non enim in turpis pulvinar facilisis. Ut felis. Praesent dapibus, neque id cursus faucibus, tortor neque egestas augue, eu vulputate magna eros eu erat. Aliquam erat volutpat. Nam dui mi, tincidunt quis, accumsan porttitor, facilisis luctus, metus
            </p>
        </div>
        <div id="r">
            <p>
                Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Vestibulum tortor quam, feugiat vitae, ultricies eget, tempor sit amet, ante. Donec eu libero sit amet quam egestas semper. Aenean ultricies mi vitae est. Mauris placerat eleifend leo. Quisque sit amet est et sapien ullamcorper pharetra. Vestibulum erat wisi, condimentum sed, commodo vitae, ornare sit amet, wisi. Aenean fermentum, elit eget tincidunt condimentum, eros ipsum rutrum orci, sagittis tempus lacus enim ac dui. Donec non enim in turpis pulvinar facilisis. Ut felis. Praesent dapibus, neque id cursus faucibus, tortor neque egestas augue, eu vulputate magna eros eu erat. Aliquam erat volutpat. Nam dui mi, tincidunt quis, accumsan porttitor, facilisis luctus, metus
            </p>
        </div>
    </div>
</body>
</html>
```

![img](https://images0.cnblogs.com/blog2015/315302/201508/111029267074149.png)![img](https://images0.cnblogs.com/blog2015/315302/201508/111033227399273.png)

##### 3、做出各种图形效果

```
<style>
#star-six {
  width: 0;
  height: 0;
  border-left: 50px solid transparent;
  border-right: 50px solid transparent;
  border-bottom: 100px solid red;
  position: relative;
}
#star-six::after {
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
</style>
<body>
<div id="star-six"></div>
</body>
```

![img](https://images0.cnblogs.com/blog2015/315302/201508/111044162547286.png)

##### 4、不使用图片创建小图标

```
<!doctype html>
<html>
<head>
    <title>伪类标签使用</title>
</head>
<style type="text/css">
    #wraper{padding:10px;width:380px;height:380px;border:3px solid #ccc;margin:auto;}
    #power{width: 30px;height: 30px;margin:20px;border: 6px solid #EEB422;border-radius: 50%;position: relative;display: inline-block;}
    #power:before{width:7px;height:22px;background:#EEB422;position: absolute;left:8px;top:-13px;content: "";border: 3px solid #fff;}
    #play{width: 30px;height: 30px;margin:20px;border: 6px solid #EEB422;border-radius: 50%;position:relative;display: inline-block;background: #EEB422;}
    #play:before{border:11px solid transparent;border-left:17px solid #fff;content: "";position: absolute;left:9px;top: 3px;}
    #pause{width: 30px;height: 30px;margin:20px;border: 6px solid #EEB422;border-radius: 50%;position:relative;display: inline-block;background: #EEB422;}
    #pause:before{height:20px;width:5px;border:0px solid transparent;border-left:8px solid #fff;border-right:8px solid #fff;content: "";position: absolute;left:4px;top: 5px;}
    #stop{width: 30px;height: 30px;margin:20px;border: 6px solid #EEB422;border-radius: 50%;position:relative;display: inline-block;background: #EEB422;}
    #stop:before{height:17px;width:17px;background:#fff;content: "";position: absolute;left:6px;top: 6px;}
    #comment{width: 50px;height: 25px;margin:20px;border: 6px solid #EEB422;border-radius: 20%;position:relative;display: inline-block;background: #EEB422;}
    #comment:before{border:10px solid transparent;border-top:10px solid #EEB422;content: "";position: absolute;left:28px;top: 28px;}
    #comment:after{content: "....";position: absolute;color: #fff;font-size: 26px;top: -10px;left: 2px;}
    #like{width: 50px;height: 30px;margin:20px;border-radius: 55%;transform:rotate(55deg);-webkit-transform:rotate(55deg);position:relative;display: inline-block;background: #EEB422;}
    #like:before{width:50px;height:30px;border-radius: 55%;transform:rotate(-110deg);-webkit-transform:rotate(-110deg);background:#EEB422;content: "";position: absolute;left:8px;top: -12px;}
    #search{width: 20px;height: 20px;border:4px solid #EEB422;border-radius:50%;margin:20px;position:relative;display: inline-block;top: -5px;left: -5px;}
    #search:before{width:20px;height:5px;background:#EEB422;transform:rotate(45deg);-webkit-transform:rotate(45deg);content: "";position: absolute;left:15px;top: 22px;}
    #home{width: 30px;height: 30px;background:#EEB422;margin:20px;position:relative;display: inline-block;top: 5px;}
    #home:before{width:6px;height:12px;background:#fff;content: "";position: absolute;left:12px;top: 20px;}
    #home:after{border:25px solid transparent;border-bottom:20px solid #EEB422;content: "";position: absolute;top: -38px;left:-10px;}
    #photo{width:40px;height:30px;background:#EEB422;margin:20px;position:relative;display: inline-block;top: 5px;}
    #photo:before{width:13px;height:13px;border:4px solid #fff;border-radius:50%;background:#EEB422;content: "";position: absolute;left:10px;top: 5px;}
    #photo:after{width:15px;height:10px;background:#EEB422;content: "";position: absolute;top: -7px;left:13px;}
    #photo{width:40px;height:30px;background:#EEB422;margin:20px;position:relative;display: inline-block;top: 5px;}
    #email{width:50px;height:35px;background:#EEB422;margin:20px;position:relative;display: inline-block;top: 5px;}
    #email:before{border:25px solid transparent;border-top:20px solid #fff;content: "";position: absolute;left:0px;top: 2px;}
    #email:after{border:25px solid transparent;border-top:20px solid #EEB422;content: "";position: absolute;top:0px;}
    #file{width:30px;height:45px;border:4px solid #EEB422;margin:20px;position:relative;display: inline-block;top: 5px;}
    #file:before{border:10px solid #fff;border-right:10px solid #EEB422;border-bottom:10px solid #EEB422;content: "";position: absolute;left:-4px;top: -4px;}
    #file:after{width:20px;height:5px;border-top:3px solid #EEB422;border-bottom:3px solid #EEB422;content: "";position: absolute;left: 5px;top: 25px;}
    #history{width:35px;height:35px;border:4px solid #EEB422;border-radius: 50%;margin:20px;position:relative;display: inline-block;top: 5px;}
    #history:before{width:14px;height:14px;border-bottom:4px solid #EEB422;border-left:4px solid #EEB422;content: "";position: absolute;left:14px;top: 3px;}
    #video{width:50px;height:35px;background:#EEB422;border-radius: 20%;margin:20px;position:relative;display: inline-block;top: -5px;}
    #video:before{width:6px;height:6px;border:11px solid transparent;border-right:11px solid #EEB422;content: "";position: absolute;left:35px;top: 4px;}
    #video:after{width:10px;height:10px;border:6px solid transparent;border-top:6px solid #EEB422;border-left:6px solid #EEB422;transform:rotate(45deg);-webkit-transform:rotate(45deg);content: "";position: absolute;left:13px;top: 35px;}
    #tags{width:50px;height:25px;background:#EEB422;border-radius: 35% 0% 0% 35%;transform:rotate(45deg);-webkit-transform:rotate(45deg);margin:20px;margin-left:35px;position:relative;display: inline-block;top: -5px;}
    #tags:before{width:10px;height:10px;border-radius:50%;background:#fff;content: "";position: absolute;left:7px;top: 7px;}
    #phone{width:50px;height:50px;border-left:6px solid #EEB422;border-radius:20%;transform:rotate(-30deg);-webkit-transform:rotate(-30deg);margin:20px;margin-right:0px;position:relative;display: inline-block;top: -5px;}
    #phone:before{width:15px;height:15px;background:#EEB422;border-radius: 20%;content: "";position: absolute;left:-2px;top: 1px;}
    #phone:after{width:15px;height:15px;background:#EEB422;border-radius: 20%;content: "";position: absolute;left:-3px;top: 34px;}
    #profile{width: 40px;height:15px;background:#EEB422;border-radius: 45% 45% 0 0;margin:20px;position:relative;display: inline-block;top: 0px;}
    #profile:before{width: 20px;height:22px;background:#EEB422;border-radius:40%;content: "";position: absolute;left: 10px;top: -22px;}
</style>
<body>
    <div id="wraper">
        <div id="power"></div>
        <div id="play"></div>
        <div id="pause"></div>
        <div id="stop"></div>
        <div id="comment"></div>
        <div id="like"></div>
        <div id="search"></div>
        <div id="home"></div>
        <div id="photo"></div>
        <div id="email"></div>
        <div id="file"></div>
        <div id="history"></div>
        <div id="video"></div>
        <div id="tags"></div>
        <div id="phone"></div>
        <div id="profile"></div>
    </div>
</body>
</html>
```

![img](https://images0.cnblogs.com/blog2015/315302/201508/111126032237893.png)

这个效果来自：http://www.w3cfuns.com/blog-5444604-5402127.html

有大神用伪元素创建了84种小图标，具体可查看http://nicolasgallagher.com/pure-css-gui-icons/

##### 5、显示打印网页的URL

```
<style>
@media print {
  a[href]:after {
    content: " (" attr(href) ") ";
  }
}
</style><body>
<a href="http://www.baidu.com">百度</a>
</body>
```

![img](https://images0.cnblogs.com/blog2015/315302/201505/041721073927186.png)

##### 6、给blockquote添加引号

```
<meta charset="utf-8"/>
<style type="text/css">
    blockquote::before {
    content: open-quote;
    color: #ddd;
    z-index: -1;
    font-size:80px;
}
</style>
<blockquote>引用一个段落,双引号用::before伪元素实现</blockquote>
```

![img](https://images0.cnblogs.com/blog2015/315302/201508/111141231299523.png)

##### 7、超链接特效

```
<meta charset="utf-8" />
<style type="text/css">
body{
    background-color: #425a6c;
}
    a {
    position: relative;
    display: inline-block;
    outline: none;
    color: #fff;
    text-decoration: none;
    font-size: 32px;
    padding: 5px 20px;
}
a:hover::before, a:hover::after { position: absolute; }
a:hover::before { content: "\5B"; left: -10px; }
a:hover::after { content: "\5D"; right:  -10px; }
</style>
<a>鼠标移上去出现方括号</a>
```

![img](https://images0.cnblogs.com/blog2015/315302/201508/111326598647483.png)

##### 8、::before和::after实现多背景图片

```
<meta charset="utf-8" />
<style type="text/css">
#silverback {
    position: relative;
    z-index: 1;
    min-width: 200px;
    min-height: 200px;
    padding: 120px 200px 50px;
    background: #d3ff99 url(img/vines-back.png) -10% 0 repeat-x;
}
#silverback:before, #silverback:after {
    position: absolute;
    z-index: -1;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    padding-top: 100px;
}
#silverback:before {
    content: url(img/gorilla-1.png);
    padding-left: 3%;
    text-align: left;
    background: transparent url(img/vines-mid.png) 300% 0 repeat-x;
}
#silverback:after {
    content: url(img/gorilla-2.png);
    padding-right: 3%;
    text-align: right;
    background: transparent url(img/vines-front.png) 70% 0 repeat-x;
}
</style>
<div id="silverback">一个标签应用了5张背景图片</div>
```

![img](https://images0.cnblogs.com/blog2015/315302/201508/111409393792336.png)

