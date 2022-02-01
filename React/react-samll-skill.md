---
title: 可以了解一下的react小技巧
date: 2021-02-16 01:34:33
tags: React
---

## 路由传参

1.params

```
<Route path='/path/:name' component={Path}/>
<link to="/path/2">xxx</Link>
this.props.history.push({pathname:"/path/" + name});
读取参数用:this.props.match.params.name
```

2.query

```
<Route path='/query' component={Query}/>
<Link to={{ path : '/query' , query : { name : 'sunny' }}}>
this.props.history.push({pathname:"/query",query: { name : 'sunny' }});
读取参数用: this.props.location.query.name
```

3.state

```
<Route path='/sort ' component={Sort}/>
<Link to={{ path : '/sort ' , state : { name : 'sunny' }}}> 
this.props.history.push({pathname:"/sort ",state : { name : 'sunny' }});
读取参数用: this.props.location.query.state 
```

4.search

```
<Route path='/web/search ' component={Search}/>
<link to="web/search?id=12121212">xxx</Link>
this.props.history.push({pathname:`/web/search?id ${row.id}`});
读取参数用: this.props.location.search
```

5.优缺点

```
1.params和 search 只能传字符串,刷新页面参数不会丢
2.query和 state 可以传对象,但是刷新页面参数会丢失
```

## onRef

​	原理:onRef 通讯原理就是通过 props 的事件机制将组件的 this(组件实例)当做参数传到父组件,父组件就可以操作子组件的 state 和方法

EightteenChildFour.jsx

```
export default class EightteenChildFour extends React.Component {
  state={
      name:'这是组件EightteenChildFour的name 值'
  }

  componentDidMount(){
    this.props.onRef(this)
    console.log(this) // ->将EightteenChildFour传递给父组件this.props.onRef()方法
  }

  click = () => {
    this.setState({name:'这是组件click 方法改变EightteenChildFour改变的name 值'})
  };

  render() {
    return (
      <div>
        <div>{this.state.name}</div>
        <Button type="primary" onClick={this.click}>
          点击改变组件EightteenChildFour的name 值
        </Button>
      </div>
    );
  }
}
```

eighteen.jsx

```
<EightteenChildFour onRef={this.eightteenChildFourRef}></EightteenChildFour>

eightteenChildFourRef = (ref)=>{
  console.log('eightteenChildFour的Ref值为')
  // 获取的 ref 里面包括整个组件实例
  console.log(ref)
  // 调用子组件方法
  ref.click()
}
```

## ref

原理:就是通过 React 的 ref 属性获取到整个子组件实例,再进行操作

EightteenChildFive.jsx

```
// 常用的组件定义方法
export default class EightteenChildFive extends React.Component {
  state={
      name:'这是组件EightteenChildFive的name 值'
  }

  click = () => {
    this.setState({name:'这是组件click 方法改变EightteenChildFive改变的name 值'})
  };

  render() {
    return (
      <div>
        <div>{this.state.name}</div>
        <Button type="primary" onClick={this.click}>
          点击改变组件EightteenChildFive的name 值
        </Button>
      </div>
    );
  }
}
```

eighteen.jsx

```
// 钩子获取实例
componentDidMount(){
    console.log('eightteenChildFive的Ref值为')
      // 获取的 ref 里面包括整个组件实例,同样可以拿到子组件的实例
    console.log(this.refs["eightteenChildFiveRef"])
  }

// 组件定义 ref 属性
<EightteenChildFive ref="eightteenChildFiveRef"></EightteenChildFive>
```

## 使用 if...else

场景:有些时候需要根据不同状态值页面显示不同内容

```
import React from "react";

export default class Four extends React.Component {
  state = {
    count: 1
  };
  render() {
    let info
    if(this.state.count===0){
      info=(
        <span>这是数量为 0 显示</span>
      )
    } else if(this.state.count===1){
      info=(
        <span>这是数量为 1 显示</span>
      )
    }
    return (
      <div>
        {info}
      </div>
    );
  }
}
```

## state 值改变的五种方式

方式 1

```
let {count} = this.state
this.setState({count:2})
```

方式 2:callBack

```
this.setState(({count})=>({count:count+2}))
```

方式 3:接收 state 和 props 参数

```
this.setState((state, props) => {
    return { count: state.count + props.step };
});
```

方式 4:hooks

```
const [count, setCount] = useState(0)
// 设置值
setCount(count+2)
```

方式 5:state 值改变后调用

```
this.setState(
    {count:3},()=>{
        //得到结果做某种事
    }
)
```

## 监听states 变化

1.16.x

```
componentWillReceiveProps (nextProps){
  if(this.props.visible !== nextProps.visible){
      //props 值改变做的事
  }
}
```

2.16.x

```
export default class Six extends React.Component {
  state = {
    countOne:1,
    changeFlag:''
  };
  clickOne(){
    let {countOne} = this.state
    this.setState({countOne:countOne+1})
  };
  static getDerivedStateFromProps (nextProps){
    console.log('变化执行')
    return{
      changeFlag:'state 值变化执行'
    }
  }
  render() {
    const {countOne,changeFlag} = this.state
    return (
      <div>
        <div>
         <Button type="primary" onClick={this.clickOne.bind(this)}>点击加 1</Button><span>countOne 值为{countOne}</span>
        <div>{changeFlag}</div>
        </div>
      </div>
    );
  }
}
```

# 组件定义方法

方式 1:ES5 的Function 定义

```
function FunCom(props){
  return <div>这是Function 定义的组件</div>
}
ReactDOM.render(<FunCom name="Sebastian" />, mountNode)

// 在 hooks 未出来之前,这个是定义无状态组件的方法,现在有了 hooks 也可以处理状态
```

方式 2: ES5的 createClass 定义

```
const CreateClassCom = React.createClass({
  render: function() {
  return <div>这是React.createClass定义的组件</div>
  }
});
```

方式 3:ES6 的 extends

```
class Com extends React.Component {
  render(){
    return(<div>这是React.Component定义的组件</div>)
  }
}
```

调用

```
export default class Seven extends React.Component {
  render() {
    return (
      <div>
        <FunCom></FunCom>
        <Com></Com>
      </div>
    );
  }
}
```

区别: ES5的 createClass是利用function模拟class的写法做出来的es6; 通过es6新增class的属性创建的组件此组件创建简单.

## 通过 ref 属性获取 component

方式 1:也是最早的用法,通过 this.refs[属性名获取] 也可以作用到组件上,从而拿到组件实例

```
class RefOne extends React.Component{
  componentDidMount() {
    this.refs['box'].innerHTML='这是 div 盒子,通过 ref 获取'
  }
  render(){
    return(
      <div ref="box"></div>
    )
  }
}
```

方式 2:回调函数,在dom节点或组件上挂载函数，函数的入参是dom节点或组件实例，达到的效果与字符串形式是一样的，都是获取其引用

```
class RefTwo extends React.Component{
  componentDidMount() {
    this.input.value='这是输入框默认值';
    this.input.focus();
  }
  render(){
    return(
      <input ref={comp => { this.input = comp; }}/>
    )
  }
}
```

方式 3:React.createRef() React 16.3版本后，使用此方法来创建ref。将其赋值给一个变量，通过ref挂载在dom节点或组件上，该ref的current属性,将能拿到dom节点或组件的实例

```
class RefThree extends React.Component{
  constructor(props){
    super(props);
    this.myRef=React.createRef();
  }
  componentDidMount(){
    console.log(this.myRef.current);
  }
  render(){
    return <input ref={this.myRef}/>
  }
}
```

方式 4:React.forwardRef
 React 16.3版本后提供的，可以用来创建子组件，以传递ref

```
class RefFour extends React.Component{
  constructor(props){
    super(props);
    this.myFourRef=React.createRef();
  }
  componentDidMount(){
    console.log(this.myFourRef.current);
  }
  render(){
    return <Child ref={this.myFourRef}/>
  }
}
```

子组件通过React.forwardRef来创建，可以将ref传递到内部的节点或组件，进而实现跨层级的引用。forwardRef在高阶组件中可以获取到原始组件的实例.

## PropTypes

场景:检测传入子组件的数据类型
类型检查PropTypes自React v15.5起已弃用，请使用prop-types

方式 1:旧的写法

```
class PropTypeOne extends React.Component {
  render() {
    return (
      <div>
        <div>{this.props.email}</div>
        <div>{this.props.name}</div>
      </div>
    );
  }
}

PropTypeOne.propTypes = {
  name: PropTypes.string, //值可为array,bool,func,number,object,symbol
  email: function(props, propName, componentName) { //自定义校验
    if (
      !/^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+(.[a-zA-Z0-9_-])+/.test(
        props[propName]
      )
    ) {
      return new Error(
        "组件" + componentName + "里的属性" + propName + "不符合邮箱的格式"
      );
    }
  },
};
复制代码
```

方法 2:利用 ES7 的静态属性关键字 static

```
class PropTypeTwo extends React.Component {
  static propTypes = {
      name:PropTypes.string
  };
  render() {
    return (
      <div>
        <div>{this.props.name}</div>
      </div>
    );
  }
}
```

