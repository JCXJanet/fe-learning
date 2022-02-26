## Vue

### 生命周期

1. Vue生命周期
2. vue操作dom在哪个生命周期

### 基础使用

1. v-if和v-show
2. vm.$set
3. mixin, slot
4. vue template里有哪些语法（v-for，v-if，v-html，v-once等等等等）
5. 你说到了v-html，你说说怎么用的，会有什么风险，怎么解决
6. 为什么用vue，vue的优点
7. v-for中，将key设置为index时，会有什么问题？
8. watch和computed区别
9. v-for为什么不用key 为什么data是函数 双向数据绑定
10. data/watch/computed/method的区别，实现原理
11. v-for和v-if的渲染优先级，vue v-if和v-show 双向绑定原理
12. vue的钩子函数
13. v-if怎么实现的，跟v-show的区别，什么场景选用
14. vue生命周期，如果发一个异步请求放在哪里
15. slot的作用，何时会用到它
16. vue中的v-for的key的作用
17. v-for 里面key的作用，如果没有一个唯一的值，那么要选什么值来作为key
18. v-for 中的key
19. v-if为什么不用key
20. v-model的实现原理

### 通信

1. vue组件通信用过哪些
2. 数据通信 用到那些 API ，数组怎么进行通信
3. 实现EventBus（on、off、emit、once4个方法）
4. vue组件传值
5. vue bus的发布订阅和取消订阅分别都是怎么实现的
6. vue父子组件的通信
7. vue 兄弟组件的通信方式?EventBus用的什么模式
8. 手写EventBus 的原理

### diff

1. Vue3 diff算法的优化
2. 为什么要用Virtul Dom，Virtual Dom的效率一定比直接操作Dom高吗？你说Virtual Dom减少不必要的Dom操作，让你来设计如何实现这个效果
3. diff算法中key的作用，为什么一般不用index作为它的key
4. 那为什么vue2.0要引入虚拟dom呢
5. vue的diff算法，问的比较详细，具体是如何比较新旧vnode的，以及节点的比较是深度优先还是广度优先
6. vue dom-diff[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)
7. 

### 核心原理

1. vue数据双向绑定，发布订阅者模式
2. vue数据双向绑定原理，2.0和3.0的区别
3. Vue是怎样编译的
4. Vue的响应式原理？
5. nextTick原理
6. vue模板编译原理
7. 详细说一下vue3 composition api
8. vue单向数据流的特点
9. vue数据劫持
10. vue依赖收集原理
11. defineProperty如何监听数组变化（拦截改变数组的7个方法）
12. proxy怎么实现监听，相较于vue2的实现有哪些优点
13. keep-alive原理
14. vue是如何获取到DOM节点变化的
15. 服务器端是如何解析vue组件进行渲染的，与客户端渲染有何不同
16. vue模板指令解析的原理和步骤，如v-model
17. vue中key的作用
18. 使用vue相对于使用原生js有什么好处（操作dom、js对象，vue组件）
19. vue3如何实现数据劫持
20. vue2 对于增加或者修改的属性是怎么再进行监听的
21. vue2 和vue3 响应式的变化?
22. vue双绑原理，dep订阅器数据结构，有哪些方法
23. vue怎么变成前端产物、每个阶段做了什么、解析阶段做了什么
24. 说说vue对数组的监听
25. vue中为什么循环要有key的概念，dom中diff还有什么优化？vue 数组支持响应式渲染吗？
26. vue实现双向绑定的三个类 observer watcher dep
27. data不发生更新时，conputed还会进行缓存操作吗？还会执行吗？
28. 数据劫持的方法有哪些？除了defineproperty和proxy还有啥
29. vue文件中的data为什么要写成函数，而不能是对象

### 状态管理

1. vuex 的工作原理
2. vueX使用方式
3. vuex的mutation能实现异步吗
4. mutation是同步的吗，可不可以直接改state
5. vuex和bus的区别

### Router

1. 说说 vueRouter 原理
2. vue-router 有几种路由方式？hash 和 history 的区别？
3. vue-router实现登录流程，vue-cli打包流程，vue created mounted的区别，vue nextTick
4. 说说vue路由有哪些模式和区别
5. vue-router懒加载实现原理
1
### 其他

1. 用 betterScroll 用到了里面的什么功能？为什么会选择 betterScroll？
2. vue和react对比
3. vue性能优化方案
4. vue-loader是干啥的
