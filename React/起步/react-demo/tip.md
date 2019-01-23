### JSX语法规则

1.遇到 HTML 标签（以 < 开头），就用 HTML 规则解析；遇到代码块（以 { 开头），就用 JavaScript 规则解析。

2.允许在模版中插入变量，如果变量是数组的话，就自动展开数组。

### DOM diff

组件并不是真实的 DOM 节点，而是存在于内存之中的一种数据结构，叫做虚拟 DOM （virtual DOM）。只有当它插入文档以后，才会变成真实的 DOM 。根据 React 的设计，所有的 DOM 变动，都先在虚拟 DOM 上发生，然后再将实际发生变动的部分，反映在真实 DOM上，这种算法叫做 DOM diff ，它可以极大提高网页的性能表现。

### 组件规则

1.组件类的第一个字母必须大写，否则会报错，比如HelloMessage不能写成helloMessage。

2.组件类只能包含一个顶层标签，否则也会报错。

### 创建组件的三种方式和区别
[点击阅读](https://www.cnblogs.com/wonyun/p/5930333.html)
- 1.函数定义的 无状态组件

```
function HelloComponent(props, /* context */) {
  return <div>Hello {props.name}</div>
}
ReactDOM.render(<HelloComponent name="Sebastian" />, mountNode) 
```

- 2.es5原生方式React.createClass [要点](https://blog.csdn.net/qq_39207948/article/details/79402287)


- 3.es6定义的额extends React.Component


### 组件的生命周期
- Mounting：已插入真实 DOM
- Updating：正在被重新渲染
- Unmounting：已移出真实 DOM

### 组件每个状态的两种处理函数 

will 函数在进入状态之前调用，did 函数在进入状态之后调用

- componentWillMount()
- componentDidMount()
- componentWillUpdate(object nextProps, object nextState)
- componentDidUpdate(object prevProps, object prevState)
- componentWillUnmount()

React 另外提供两种特殊状态的处理函数。

- componentWillReceiveProps(object nextProps)：已加载组件收到新的参数时调用
- shouldComponentUpdate(object nextProps, object nextState)：组件判断是否重新渲染时调用









