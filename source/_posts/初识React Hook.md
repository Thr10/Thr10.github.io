---
title: 初识React Hook
date: 2021-01-20 14:59:38
tags:
---
## 前言
这是一篇学习 React hook 的学习笔记，从回顾 React 基础开始，逐渐使用 React hook，留下一些学习过程中的记录，争取在平时的生产环境能够使用这些知识。

## 过程
#### React基础回顾
+ 类组件可以通过 `this.props` 获得父类调用该组件时传入的属性
+ 在构造函数中 `this.state` 中可以创建该组件的属性，且只能通过 `this.setState` 的方式改变其值
+ React组件生命周期
+ 一个使用TS编写的HellowWorld组件
``````
import React from 'react';

interface IHellowProps {
    message: string;
};

const Hellow = (props: IHellowProps) => {
    return <h2>{props.message}</h2>
}

export default Hellow;
``````
+ 使用 React 官方定义的 `FunctionComponent`, 它接受一个泛型p(interface), 这样就可以在其中拿到 children, 还可以获得一些静态属性
`````
const Hellow: React.FunctionComponent<IHellowProps> = (props) => {
    props.children // 可以拿到children
    return <h2>{props.message}</h2>
}
// defaultProps 静态属性
Hellow.defaultProps = {
    message: "Hellow world"
}
`````
+ FunctionComponent 还可以使用 FC 别名（`React.FC`）

#### 为什么要使用 React Hook?
+ 16.8 版本之前，class组件可以有自己的状态，也可以写钩子函数，这些在function(pure)组件中是无法实现的，16.8版本引入hook之后，可以使functicon component可以维护自己的状态，以及可以使用生命周期函数的特性。
+ 目前 React 官方没有移出 class component 的计划，百分之百的向后兼容
+ 例如两个组件，一个 NewList.js ，一个 UserList.js，两个组件状态逻辑一致，只是展示方式有些区别，如果之前，只能使用 HOC 或者 render Props，有了hook之后就更加的便捷了。（表示疑惑）
+ 一些不相关的逻辑要写在同一个生命周期下，当声明周期函数很复杂的时候，就难以维护了
 
#### useState Hook
一个点赞，并累计点赞数的组件: LikeButton.tsx 文件
``````
import React ,{useState} from 'react';
const LikeButton: React.FC = () => {
    const [like, setLike] = useState(0);
    return (
        <button onClick={() => {setLike(like + 1)}}>
            {like}
        </button>
    )
}
export default LikeButton;
``````
增加一个状态
``````
const LikeButton: React.FC = () => {
    const [obj, setObj] = useState({like: 0 on: true});
    return (
        <>
            <button onClick={() => {setObj({like: object.like + 1, on: object.on}}}>
                {obj.like}
            </button>
            <button onClick={() => {setObj({like: object.like, on: !object.on}}}>
                {obj.on ? 'ON' : 'OFF'}
            </button>
        </>

    )
}
``````
将两个状态独立拆分出来
``````
const LikeButton: React.FC = () => {
    const [like, setLike] = useState({like: 0 });
    const [on, setOn] = useState({ on: true});
    return (
        <>
            <button onClick={() => {setLike(like + 1)}>
                {obj.like}
            </button>
            <button onClick={() => {setOn(!on)}>
                {obj.on ? 'ON' : 'OFF'}
            </button>
        </>

    )
}
``````

#### useEffect
pure component 是纯函数组件，即既定输入得到既定输出，给函数添加 网络请求，dom操作，setTimeout等操作，可以理解为给函数组件添加 副作用 negatice effect
##### 不需要清除的 effect
实现一个当组件渲染或更新后，去重新设置document.title的功能，如果在class组件中，需要如下代码实现
``````
componentDidMount() {
    document.title = `Clicked ${this.state.count} times`;
}
componentDidUpdate() {
    document.title = `Clicked ${this.state.count} times`;
}
``````
使用 `useEffect`，传入一个回调函数，会在组件每次渲染后都执行
``````
useEffect(() => {
    document.title = `Clicked ${this.state.count} times`;
})
``````
##### 需要清除的 effect
例如添加dom事件的操作是需要清除的 effect, 需要在组件卸载时清除，来防止内存泄露
实现一个鼠标跟踪器, 在 class component 中实现
``````
componentDidMount() {
    document.addEventListener = ('click', this.updateMouse);
}
componentDidUpdate() {
    document.addEventListener = ('click', this.updateMouse);
}
``````
使用hook实现, 在useEffect的返回函数中做清除的操作
``````
const [positions, setPositions] = useState({x: 0, y: 0})
useEffect(() => {
    const updateMouse = (e: MouseEvent) => {
        console.log('inner')
        setPositions({ x: e.clientX, y: e.clientY})
    }
    document.addEventListener('click', updateMouse)
    return () => {
        document.removeEventListener('click', updateMouse)
    }
})
``````
##### 控制 Effect 的执行
正常情况下，React重新渲染就会执行，会有特定的场景想去给Effect的执行做一些限制, useEffect第二个参数可以传一个数组, 传入需要依赖的状态
+ 只在挂载和卸载组件后执行，传入空数组
``````
useEffect(() => {
    // do something
}, [])
``````
意义在于告诉组件这个Effect不依赖于该组件的 `state` 和 `props`。

+ 依赖与某些状态，还是上面增减点赞次数的例子，依赖于 点赞数 和开关 这两个状态的 Effect
``````
useEffect(() => {
    // do something
}, [like, on])
``````

#### 自定义 hook
#### useRef
#### useContext
#### hook规则和其他hook











 