---
title: 可以了解一下的前端路由原理
date: 2021-02-16 01:29:19
tags: React
---

React-Router教程

https://blog.csdn.net/gao_xu_520/article/details/81133691

Vue-Router教程

https://router.vuejs.org/zh/guide/#html

前端路由原理

## 一、什么是路由？

​	路由的概念起源于服务端，在以前前后端不分离的时候，由后端来控制路由，当接收到客户端发来的   `HTTP` 请求，就会根据所请求的相应 `URL`，来找到相应的映射函数，然后执行该函数，并将函数的返回值发送给客户端。对于最简单的静态资源服务器，可以认为，所有 `URL` 的映射函数就是一个文件读取操作。对于动态资源，映射函数可能是一个数据库读取操作，也可能是进行一些数据的处理等等。然后根据这些读取的数据，在服务器端就使用相应的模板来对页面进行渲染后，再返回渲染完毕的页面。它的好处与缺点非常明显：

- 好处：安全性好，`SEO` 好；
- 缺点：加大服务器的压力，不利于用户体验，代码冗合不好维护；

​	也正是由于后端路由还存在着自己的不足，前端路由才有了自己的发展空间。对于前端路由来说，路由的映射函数通常是进行一些 `DOM` 的显示和隐藏操作。这样，当访问不同的路径的时候，会显示不同的页面组件。前端路由主要有以下两种实现方案：

- `Hash`
- `History`

当然，前端路由也存在缺陷：使用浏览器的前进，后退键时会重新发送请求，来获取数据，没有合理地利用缓存。但总的来说，现在前端路由已经是实现路由的主要方式了，前端三大框架 `Angular`、`React`、`Vue` ，它们的路由解决方案 `angular/router`、`react-router`、`vue-router` 都是基于前端路由进行开发的，因此将前端路由进行了解和 掌握是很有必要的，下面我们分别对两种常见的前端路由模式 `Hash` 和 `History` 进行讲解。

## 二、如何实现前端路由？

要实现前端路由，需要解决两个核心：

1. 如何更改URL却不引起页面刷新？
2. 如何检测URL变化了？

**哈希实现**

1. hash是URL中的hash（`#`）及后面的那部分，常用于锚点在页面内进行导航，更改URL中的hash部分不会引起页面刷新
2. 通过[hashchange](https://developer.mozilla.org/en-US/docs/Web/API/Window/hashchange_event)事件监听URL的变化，改变URL的方式只有这几种：通过浏览器前进后退改变URL，通过``标签改变URL，通过`window.location`改变URL，这几种情况改变URL都会触发hashchange事件

**历史实现**

**历史实现**

1. 历史提供了pushState和replaceState两个方法，这两个方法更改URL的路径部分不会引起页面刷新
2. 历史提供类似hashchange事件的[popstate](https://developer.mozilla.org/en-US/docs/Web/API/Window/popstate_event)事件，但popstate事件有些不同：通过浏览器前进后退更改URL时会触发popstate事件，通过`pushState/replaceState`或``标签更改URL不会触发popstate事件。好在我们可以拦截`pushState/replaceState`的调用和``标签的点击事件来检测URL变化，所以监听URL变化可以实现，只是没有hashchange那么方便

# 利用`URL`的`Hash(#)`

在 `H5` 还没有流行开来时，一般 `SPA` 都采用 `url` 的 `hash(#)` 作为锚点，获取到 # 之后的值，并监听其改变，再进行渲染对应的子页面。[网易云音乐官网](https://music.163.com)就是利用的此技术。

| 属性     | 描述                                |
| :------- | :---------------------------------- |
| hash     | 设置或返回从 # 开始的 URL （锚）    |
| host     | 设置或返回主机名和当前 URL 的端口号 |
| hostname | 设置或返回当前 URL 的主机名         |
| href     | 设置或返回完整的 URL                |
| pathname | 设置或返回当前 URL 的路径部分       |
| port     | 设置或返回当前 URL 的端口号         |
| protocol | 设置或返回当前 URL 的协议           |
| search   | 设置或返回从 ? 开始的 URL 部分      |

```
<body>
  <h1 id="id"></h1>
  <a href="#/id1">id1</a>
  <a href="#/id2">id2</a>
  <a href="#/id3">id3</a>
</body>

<script>
  window.addEventListener('hashchange', e => {
    e.preventDefault()
    document.querySelector('#id').innerHTML = location.hash
  })
</script>
```

#### `AJAX` 方式

定义一个 `HTML` 文件，名为 `demo2.html`，在里面写入一些内容（由于主页面已经有`head`，`body`等根标签，此文件只需写入需要替换的标签）：

```
<div>
  我是AJAX加载进来的HTML文件
</div>
```

```
<body>
  <h1 id="id"></h1>
  <a href="#/id1">id1</a>
  <a href="#/id2">id2</a>
  <a href="#/id3">id3</a>
</body>
<script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.min.js"></script>
<script type="module">
  // import demo1 from './demo1.js'
  // document.querySelector('#id').innerHTML = demo1
  $.ajax({
    url: './demo2.html',
    success: (res) => {
      document.querySelector('#id').innerHTML = res
    }
  })
  window.addEventListener('hashchange', e => {
    e.preventDefault()
    document.querySelector('#id').innerHTML = location.hash
  })
</script>
```

既然加载不同页面的内容都已经生效，那么只需要包装一下我们的监听，利用观察者模式封装路由的变化：

```
<body>
  <h1 id="id">我是空白页</h1>
  <a href="#/id1">id1</a>
  <a href="#/id2">id2</a>
  <a href="#/id3">id3</a>
</body>
<script type="module">
  import demo1 from './demo1.js'
  // 创建一个 newRouter 类
  class newRouter {
    // 初始化路由信息
    constructor() {
      this.routes = {};
      this.currentUrl = '';
    }
    // 传入 URL 以及 根据 URL 对应的回调函数
    route(path, callback = () => {}) {
      this.routes[path] = callback;
    }
    // 切割 hash，渲染页面
    refresh() {
      this.currentUrl = location.hash.slice(1) || '/';
      this.routes[this.currentUrl] && this.routes[this.currentUrl]();
    }
    // 初始化
    init() {
      window.addEventListener('load', this.refresh.bind(this), false);
      window.addEventListener('hashchange', this.refresh.bind(this), false);
    }
  }
  // new 一个 Router 实例
  window.Router = new newRouter();
  // 路由实例初始化
  window.Router.init();

  // 获取关键节点
  var content = document.querySelector('#id');

  Router.route('/id1', () => {
    content.innerHTML = 'id1'
  });
  Router.route('/id2', () => {
    content.innerHTML = demo1
  });
  Router.route('/id3', () => {
    $.ajax({
      url: './demo2.html',
      success: (res) => {
        content.innerHTML = res
      }
    })
  });
</script>
```

#### 利用 `H5` 新增方法 `History interface`

|     API     |                             说明                             |
| :---------: | :----------------------------------------------------------: |
|  `back()`   |     回退到上次访问的 `URL` （与浏览器点击后退按钮相同）      |
| `forward()` |     前进到回退之前的 `URL` （与浏览器点击向前按钮相同）      |
|   `go(n)`   | `n` 接收一个整数，移动到该整数指定的页面，比如`go(1)`相当于`forward()`，`go(-1)` 相当于 `back()`，`go(0)`相当于刷新当前页面 |

#### 往历史记录栈中添加记录：pushState(state, title, url)

浏览器支持度: `IE10+`

- state: 一个 `JS` 对象（不大于640kB），主要用于在 `popstate` 事件中作为参数被获取。如果不需要这个对象，此处可以填 `null`
- title: 新页面的标题，部分浏览器(比如 Firefox )忽略此参数，因此一般为 `null`
- url: 新历史记录的地址，**可为页面地址，也可为一个锚点值**，新 `url` 必须与当前 `url` 处于同一个域，否则将抛出异常，此参数若没有特别标注，会被设为当前文档 `url`

#### 改变当前的历史记录：replaceState(state, title, url)

浏览器支持度: `IE10+`

- 参数含义同 `pushstate`
- 改变当前的历史记录而不是添加新的记录
- 同样不会触发 `popstate`

##### history.state

浏览器支持度: `IE10+`

- 返回当前历史记录的 `state`。

##### popstate

定义：每当同一个文档的浏览历史（即 `history` 对象）出现变化时，就会触发 `popstate` 事件。

注意：若仅仅调用 `pushState` 方法或 `replaceState` 方法 ，并不会触发该事件，只有用户点击浏览器**倒退**按钮和**前进**按钮，或者使用 `JavaScript` 调用 `back` 、 `forward` 、 `go` 方法时才会触发。另外，该事件只针对同一个文档，如果浏览历史的切换，导致加载不同的文档，该事件也不会触发。

### 实现

```
<body>
  <h1 id="id">我是空白页</h1>
  <a class="route" href="/id1">id1</a>
  <a class="route" href="/id2">id2</a>
  <a class="route" href="/id3">id3</a>
</body>
```

```
import demo1 from './demo1.js'
  // 创建一个 newRouter 类
  class newRouter {
    // 初始化路由信息
    constructor() {
      this.routes = {};
      this.currentUrl = '';
    }
    route(path, callback) {
      this.routes[path] = (type) => {
        if (type === 1) history.pushState( { path }, path, path );
        if (type === 2) history.replaceState( { path }, path, path );
        callback()
      };
    }
    refresh(path, type) {
      this.routes[this.currentUrl] && this.routes[this.currentUrl](type);
    }
    init() {
      window.addEventListener('load', () => {
        // 获取当前 URL 路径
        this.currentUrl = location.href.slice(location.href.indexOf('/', 8))
        this.refresh(this.currentUrl, 2)
      }, false);
      window.addEventListener('popstate', () => {
        this.currentUrl = history.state.path
        this.refresh(this.currentUrl, 2)
      }, false);
      const links = document.querySelectorAll('.route')
      links.forEach((item) => {
        // 覆盖 a 标签的 click 事件，防止默认跳转行为
        item.onclick = (e) => {
          e.preventDefault()
          // 获取修改之后的 URL
          this.currentUrl = e.target.getAttribute('href')
          // 渲染
          this.refresh(this.currentUrl, 2)
        }
      })
    }
  }
  // new 一个 Router 实例
  window.Router = new newRouter();
  // 实例初始化
  window.Router.init();

  // 获取关键节点
  var content = document.querySelector('#id');

  Router.route('/id1', () => {
    content.innerHTML = 'id1'
  });
  Router.route('/id2', () => {
    content.innerHTML = demo1
  });
  Router.route('/id3', () => {
    $.ajax({
      url: './demo2.html',
      success: (res) => {
        content.innerHTML = res
      }
    })
  });
```

## React版前端路由实现

### 基于hash 实现

```
  <BrowserRouter>
    <ul>
      <li>
        <Link to="/home">home</Link>
      </li>
      <li>
        <Link to="/about">about</Link>
      </li>
    </ul>

    <Route path="/home" render={() => <h2>Home</h2>} />
    <Route path="/about" render={() => <h2>About</h2>} />
  </BrowserRouter>
```

BrowserRouter实现

```
export default class BrowserRouter extends React.Component {
  state = {
    currentPath: utils.extractHashPath(window.location.href)
  };

  onHashChange = e => {
    const currentPath = utils.extractHashPath(e.newURL);
    console.log("onHashChange:", currentPath);
    this.setState({ currentPath });
  };

  componentDidMount() {
    window.addEventListener("hashchange", this.onHashChange);
  }

  componentWillUnmount() {
    window.removeEventListener("hashchange", this.onHashChange);
  }

  render() {
    return (
      <RouteContext.Provider value={{currentPath: this.state.currentPath}}>
        {this.props.children}
      </RouteContext.Provider>
    );
  }
}
```

Route 实现

```
export default ({ path, render }) => (
  <RouteContext.Consumer>
    {({currentPath}) => currentPath === path && render()}
  </RouteContext.Consumer>
);
```

Link 实现

```
export default ({ to, ...props }) => <a {...props} href={"#" + to} />;
```

### 基于 history 实现

```
 <HistoryRouter>
    <ul>
      <li>
        <Link to="/home">home</Link>
      </li>
      <li>
        <Link to="/about">about</Link>
      </li>
    </ul>

    <Route path="/home" render={() => <h2>Home</h2>} />
    <Route path="/about" render={() => <h2>About</h2>} />
  </HistoryRouter>
```

HistoryRouter 实现

```
export default class HistoryRouter extends React.Component {
  state = {
    currentPath: utils.extractUrlPath(window.location.href)
  };

  onPopState = e => {
    const currentPath = utils.extractUrlPath(window.location.href);
    console.log("onPopState:", currentPath);
    this.setState({ currentPath });
  };

  componentDidMount() {
    window.addEventListener("popstate", this.onPopState);
  }

  componentWillUnmount() {
    window.removeEventListener("popstate", this.onPopState);
  }

  render() {
    return (
      <RouteContext.Provider value={{currentPath: this.state.currentPath, onPopState: this.onPopState}}>
        {this.props.children}
      </RouteContext.Provider>
    );
  }
}
```

Route 实现

```
export default ({ path, render }) => (
  <RouteContext.Consumer>
    {({currentPath}) => currentPath === path && render()}
  </RouteContext.Consumer>
);
```

Link 实现

```
export default ({ to, ...props }) => (
  <RouteContext.Consumer>
    {({ onPopState }) => (
      <a
        href=""
        {...props}
        onClick={e => {
          e.preventDefault();
          window.history.pushState(null, "", to);
          onPopState();
        }}
      />
    )}
  </RouteContext.Consumer>
);
```

## Vue 版本前端路由实现

### 基于 hash 实现

```
<div>
      <ul>
        <li><router-link to="/home">home</router-link></li>
        <li><router-link to="/about">about</router-link></li>
      </ul>
      <router-view></router-view>
    </div>
```

```
const routes = {
  '/home': {
    template: '<h2>Home</h2>'
  },
  '/about': {
    template: '<h2>About</h2>'
  }
}

const app = new Vue({
  el: '.vue.hash',
  components: {
    'router-view': RouterView,
    'router-link': RouterLink
  },
  beforeCreate () {
    this.$routes = routes
  }
})
```

router-view 实现

```
<template>
  <component :is="routeView" />
</template>

<script>
import utils from '~/utils.js'
export default {
  data () {
    return {
      routeView: null
    }
  },
  created () {
    this.boundHashChange = this.onHashChange.bind(this)
  },
  beforeMount () {
    window.addEventListener('hashchange', this.boundHashChange)
  },
  mounted () {
    this.onHashChange()
  },
  beforeDestroy() {
    window.removeEventListener('hashchange', this.boundHashChange)
  },
  methods: {
    onHashChange () {
      const path = utils.extractHashPath(window.location.href)
      this.routeView = this.$root.$routes[path] || null
      console.log('vue:hashchange:', path)
    }
  }
}
</script>
```

router-link 实现

```
<template>
  <a @click.prevent="onClick" href=''><slot></slot></a>
</template>

<script>
export default {
  props: {
    to: String
  },
  methods: {
    onClick () {
      window.location.hash = '#' + this.to
    }
  }
}
</script>
```

### 基于 history 实现

```
<div>
      <ul>
        <li><router-link to="/home">home</router-link></li>
        <li><router-link to="/about">about</router-link></li>
      </ul>
      <router-view></router-view>
    </div>
```

```
const routes = {
  '/home': {
    template: '<h2>Home</h2>'
  },
  '/about': {
    template: '<h2>About</h2>'
  }
}

const app = new Vue({
  el: '.vue.history',
  components: {
    'router-view': RouterView,
    'router-link': RouterLink
  },
  created () {
    this.$routes = routes
    this.boundPopState = this.onPopState.bind(this)
  },
  beforeMount () {
    window.addEventListener('popstate', this.boundPopState) 
  },
  beforeDestroy () {
    window.removeEventListener('popstate', this.boundPopState) 
  },
  methods: {
    onPopState (...args) {
      this.$emit('popstate', ...args)
    }
  }
})
```

router-view 实现：

```
<template>
  <component :is="routeView" />
</template>

<script>
import utils from '~/utils.js'
export default {
  data () {
    return {
      routeView: null
    }
  },
  created () {
    this.boundPopState = this.onPopState.bind(this)
  },
  beforeMount () {
    this.$root.$on('popstate', this.boundPopState)
  },
  beforeDestroy() {
    this.$root.$off('popstate', this.boundPopState)
  },
  methods: {
    onPopState (e) {
      const path = utils.extractUrlPath(window.location.href)
      this.routeView = this.$root.$routes[path] || null
      console.log('[Vue] popstate:', path)
    }
  }
}
</script>
```

router-link 实现

```
<template>
  <a @click.prevent="onClick" href=''><slot></slot></a>
</template>

<script>
export default {
  props: {
    to: String
  },
  methods: {
    onClick () {
      history.pushState(null, '', this.to)
      this.$root.$emit('popstate')
    }
  }
}
</script>
```

数据驱动的vue-router

```
new Router({
  id: 'router-view', // 容器视图
  mode: 'hash', // 模式
  routes: [
    {
      path: '/',
      name: 'home',
      component: '<div>Home</div>',
      beforeEnter: (next) => {
        console.log('before enter home')
        next()
      },
      afterEnter: (next) => {
        console.log('enter home')
        next()
      },
      beforeLeave: (next) => {
        console.log('start leave home')
        next()
      }
    },
    {
      path: '/bar',
      name: 'bar',
      component: '<div>Bar</div>',
      beforeEnter: (next) => {
        console.log('before enter bar')
        next()
      },
      afterEnter: (next) => {
        console.log('enter bar')
        next()
      },
      beforeLeave: (next) => {
        console.log('start leave bar')
        next()
      }
    },
    {
      path: '/foo',
      name: 'foo',
      component: '<div>Foo</div>'
    }
  ]
})
```

#### 思路整理

首先是数据驱动，所以我们可以通过一个`route`对象来表述当前路由状态，比如：

```
current = {
    path: '/', // 路径
    query: {}, // query
    params: {}, // params
    name: '', // 路由名
    fullPath: '/', // 完整路径
    route: {} // 记录当前路由属性
}
```

`current.route`内存放当前路由的配置信息，所以我们只需要监听`current.route`的变化来动态`render`页面便可。

接着我么需要监听不同的路由变化，做相应的处理。以及实现`hash`和`history`模式。

#### 数据驱动

这里我们延用`vue`数据驱动模型，实现一个简单的数据劫持，并更新视图。首先定义我们的`observer`

```
class Observer {
  constructor (value) {
    this.walk(value)
  }

  walk (obj) {
    Object.keys(obj).forEach((key) => {
      // 如果是对象，则递归调用walk，保证每个属性都可以被defineReactive
      if (typeof obj[key] === 'object') {
        this.walk(obj[key])
      }
      defineReactive(obj, key, obj[key])
    })
  }
}

function defineReactive(obj, key, value) {
  let dep = new Dep()
  Object.defineProperty(obj, key, {
    get: () => {
      if (Dep.target) {
        // 依赖收集
        dep.add()
      }
      return value
    },
    set: (newValue) => {
      value = newValue
      // 通知更新，对应的更新视图
      dep.notify()
    }
  })
}

export function observer(value) {
  return new Observer(value)
}
```

再接着，我们需要定义`Dep`和`Watcher`:

```
export class Dep {
  constructor () {
    this.deppend = []
  }
  add () {
    // 收集watcher
    this.deppend.push(Dep.target)
  }
  notify () {
    this.deppend.forEach((target) => {
      // 调用watcher的更新函数
      target.update()
    })
  }
}

Dep.target = null

export function setTarget (target) {
  Dep.target = target
}

export function cleanTarget() {
  Dep.target = null
}

// Watcher
export class Watcher {
  constructor (vm, expression, callback) {
    this.vm = vm
    this.callbacks = []
    this.expression = expression
    this.callbacks.push(callback)
    this.value = this.getVal()

  }
  getVal () {
    setTarget(this)
    // 触发 get 方法，完成对 watcher 的收集
    let val = this.vm
    this.expression.split('.').forEach((key) => {
      val = val[key]
    })
    cleanTarget()
    return val
  }

  // 更新动作
  update () {
    this.callbacks.forEach((cb) => {
      cb()
    })
  }
}
```

到这里我们实现了一个简单的订阅-发布器，所以我们需要对`current.route`做数据劫持。一旦`current.route`更新，我们可以及时的更新当前页面：

```
 // 响应式数据劫持
  observer(this.current)

  // 对 current.route 对象进行依赖收集，变化时通过 render 来更新
  new Watcher(this.current, 'route', this.render.bind(this))
```

恩....到这里，我们似乎已经完成了一个简单的响应式数据更新。其实`render`也就是动态的为页面指定区域渲染对应内容，这里只做一个简化版的`render`:

```
render() {
    let i
    if ((i = this.history.current) && (i = i.route) && (i = i.component)) {
      document.getElementById(this.container).innerHTML = i
    }
  }
```

## hash 和 history

接下来是`hash`和`history`模式的实现，这里我们可以沿用`vue-router`的思想，建立不同的处理模型便可。来看一下我实现的核心代码：

```
this.history = this.mode === 'history' ? new HTML5History(this) : new HashHistory(this)
```

当页面变化时，我们只需要监听`hashchange`和`popstate`事件，做路由转换`transitionTo`:

```
 /**
   * 路由转换
   * @param target 目标路径
   * @param cb 成功后的回调
   */
  transitionTo(target, cb) {
    // 通过对比传入的 routes 获取匹配到的 targetRoute 对象
    const targetRoute = match(target, this.router.routes)
    this.confirmTransition(targetRoute, () => {
      // 这里会触发视图更新
      this.current.route = targetRoute
      this.current.name = targetRoute.name
      this.current.path = targetRoute.path
      this.current.query = targetRoute.query || getQuery()
      this.current.fullPath = getFullPath(this.current)
      cb && cb()
    })
  }

  /**
   * 确认跳转
   * @param route
   * @param cb
   */
  confirmTransition (route, cb) {
    // 钩子函数执行队列
    let queue = [].concat(
      this.router.beforeEach,
      this.current.route.beforeLeave,
      route.beforeEnter,
      route.afterEnter
    )
    
    // 通过 step 调度执行
    let i = -1
    const step = () => {
      i ++
      if (i > queue.length) {
        cb()
      } else if (queue[i]) {
        queue[i](step)
      } else {
        step()
      }

    }
    step(i)
  }
}
```

这样我们一方面通过`this.current.route = targetRoute`达到了对之前劫持数据的更新，来达到视图更新。另一方面我们又通过任务队列的调度，实现了基本的钩子函数`beforeEach`、`beforeLeave`、`beforeEnter`、`afterEnter`。 到这里其实也就差不多了，接下来我们顺带着实现几个API吧：

```
/**
   * 跳转，添加历史记录
   * @param location 
   * @example this.push({name: 'home'})
   * @example this.push('/')
   */
  push (location) {
    const targetRoute = match(location, this.router.routes)

    this.transitionTo(targetRoute, () => {
      changeUrl(this.router.base, this.current.fullPath)
    })
  }

  /**
   * 跳转，添加历史记录
   * @param location
   * @example this.replaceState({name: 'home'})
   * @example this.replaceState('/')
   */
  replaceState(location) {
    const targetRoute = match(location, this.router.routes)

    this.transitionTo(targetRoute, () => {
      changeUrl(this.router.base, this.current.fullPath, true)
    })
  }

  go (n) {
    window.history.go(n)
  }

  function changeUrl(path, replace) {
    const href = window.location.href
    const i = href.indexOf('#')
    const base = i >= 0 ? href.slice(0, i) : href
    if (replace) {
      window.history.replaceState({}, '', `${base}#/${path}`)
    } else {
      window.history.pushState({}, '', `${base}#/${path}`)
    }
  }
```

源码地址：https://github.com/muwoo/blogs/tree/master/src/router