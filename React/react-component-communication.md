---
title: React中的组件通信
date: 2021-02-16 01:27:08
tags: React
---
![react组件通信](https://segmentfault.com/img/remote/1460000012361466?w=1240&h=667)

## 1. 父组件向子组件通信

```
import React from 'react';
import PropTypes from 'prop-types';

export default function Child({ name }) {
    return <h1>Hello, {name}</h1>;
}

Child.propTypes = {
    name: PropTypes.string.isRequired,
};
```

```
import React, { Component } from 'react';

import Child from './Child';

class Parent extends Component {
    render() {
        return (
            <div>
                <Child name="Sara" />
            </div>
        );
    }
}

export default Parent;
```

## 2. 子组件向父组件通信

- 利用回调函数
- 利用自定义事件机制

### 回调函数

实现在子组件中点击隐藏组件按钮可以将自身隐藏的功能

```
import React, { Component } from 'react';
import PropTypes from 'prop-types';

class List3 extends Component {
    static propTypes = {
        hideConponent: PropTypes.func.isRequired,
    }
    render() {
        return (
            <div>
                哈哈,我是List3
                <button onClick={this.props.hideConponent}>隐藏List3组件</button>
            </div>
        );
    }
}

export default List3;
```

```
import React, { Component } from 'react';

import List3 from './components/List3';
export default class App extends Component {
    constructor(...args) {
        super(...args);
        this.state = {
            isShowList3: false,
        };
    }
    showConponent = () => {
        this.setState({
            isShowList3: true,
        });
    }
    hideConponent = () => {
        this.setState({
            isShowList3: false,
        });
    }
    render() {
        return (
            <div>
                <button onClick={this.showConponent}>显示Lists组件</button>
                {
                    this.state.isShowList3 ?
                        <List3 hideConponent={this.hideConponent} />
                    :
                    null
                }

            </div>
        );
    }
}
```

## 3. 跨级组件通信

1、层层组件传递props

> 例如A组件和B组件之间要进行通信,先找到A和B公共的父组件,A先向C组件通信,C组件通过props和B组件通信,此时C组件起的就是中间件的作用

2、使用context

```
import React, { Component } from 'react';
import PropTypes from 'prop-types';

class ListItem extends Component {
    // 子组件声明自己要使用context
    static contextTypes = {
        color: PropTypes.string,
    }
    static propTypes = {
        value: PropTypes.string,
    }
    render() {
        const { value } = this.props;
        return (
            <li style={{ background: this.context.color }}>
                <span>{value}</span>
            </li>
        );
    }
}

export default ListItem;
```

```
import ListItem from './ListItem';

class List extends Component {
    // 父组件声明自己支持context
    static childContextTypes = {
        color: PropTypes.string,
    }
    static propTypes = {
        list: PropTypes.array,
    }
    // 提供一个函数,用来返回相应的context对象
    getChildContext() {
        return {
            color: 'red',
        };
    }
    render() {
        const { list } = this.props;
        return (
            <div>
                <ul>
                    {
                        list.map((entry, index) =>
                            <ListItem key={`list-${index}`} value={entry.text} />,
                       )
                    }
                </ul>
            </div>
        );
    }
}

export default List;
```

```
import React, { Component } from 'react';
import List from './components/List';

const list = [
    {
        text: '题目一',
    },
    {
        text: '题目二',
    },
];
export default class App extends Component {
    render() {
        return (
            <div>
                <List
                    list={list}
                />
            </div>
        );
    }
}
```

## 4. 没有嵌套关系的组件通信

### 使用自定义事件的方式

下面例子中的组件关系: List1和List2没有任何嵌套关系,App是他们的父组件;

实现这样一个功能: 点击List2中的一个按钮,改变List1中的信息显示
首先需要项目中安装events 包：

```
npm install events --save
```

在src下新建一个util目录里面建一个events.js

```
import { EventEmitter } from 'events';

export default new EventEmitter();
```

list1.jsx

```
import React, { Component } from 'react';
import emitter from '../util/events';

class List extends Component {
    constructor(props) {
        super(props);
        this.state = {
            message: 'List1',
        };
    }
    componentDidMount() {
        // 组件装载完成以后声明一个自定义事件
        this.eventEmitter = emitter.addListener('changeMessage', (message) => {
            this.setState({
                message,
            });
        });
    }
    componentWillUnmount() {
        emitter.removeListener(this.eventEmitter);
    }
    render() {
        return (
            <div>
                {this.state.message}
            </div>
        );
    }
}

export default List;
```

List2.jsx

```
import React, { Component } from 'react';
import emitter from '../util/events';

class List2 extends Component {
    handleClick = (message) => {
        emitter.emit('changeMessage', message);
    };
    render() {
        return (
            <div>
                <button onClick={this.handleClick.bind(this, 'List2')}>点击我改变List1组件中显示信息</button>
            </div>
        );
    }
}
```

APP.jsx

```
import React, { Component } from 'react';
import List1 from './components/List1';
import List2 from './components/List2';


export default class App extends Component {
    render() {
        return (
            <div>
                <List1 />
                <List2 />
            </div>
        );
    }
}
```

自定义事件是典型的发布订阅模式,通过向事件对象上添加监听器和触发事件来实现组件之间的通信

