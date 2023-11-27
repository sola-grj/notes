# React面试题

1.组件之间如何通讯

- 父子组件props
- 自定义事件
- Redux和Context

2.JSX本质是什么

- createElement ，执行返回vnode

3.Context是什么，如何应用

- 父组件，向其下所有子孙组件传递信息，如一些简单的公共信息：主题等

4.shouldComponentUpdate

- 性能优化，配合“不可变值”一起使用，否则会出错

5.Redux单向数据流

6.setState场景题目

```js
componentDidmount(){
    // count = 0
    this.setState({count:this.state.count + 1})
    console.log('1',this.state.count) // 0
    this.setState({count:this.state.count + 1})
    console.log('2',this.state.count) // 0
    setTimeout(() => {
        this.setState({count:this.state.count + 1})
    	console.log('3',this.state.count) // 2
    })
    setTimeout(() => {
        this.setState({count:this.state.count + 1})
    	console.log('4',this.state.count) // 3
    })
}
```

7.什么是纯函数？

- 返回一个新值，没有副作用
- 如 arr1 = arr.slice()

8.React组件生命周期

- 单组件生命周期
- 父子组件生命周期

9.React发起ajax应该放在哪个生命周期

- componentDidMount

10.渲染列表，为何使用key

- diff算法中通过tag和key来判断，是否是sameNode
- 减少渲染次数，提升渲染性能

11.函数组件和class组件区别

- 纯函数，输入props，输出JSX
- 没有实例，没有生命周期，没有state
- 不能扩展其他方法

12.什么是受控组件

- 表单的值，受state控制
- 需要自行监听onChange，更新state，对比非受控组件

13.何时使用异步组件

- 加载大组件
- 路由懒加载

14.多个组件有公共逻辑，如何抽离

- 高阶组件
- Render Props

15.redux如何进行异步请求

- 使用异步action
- react-thunk

16.react-router如何配置路由懒加载

```jsx
import {BrowserRouter as Router,Route,Switch} from 'rreact-router-dom'
import React,{Suspense,lazy} from 'react'

const home = lazy(() => import('./routes/Home'))
const About = lazy(() => import('./routes/About'))

const App = () => {
    <Router>
        <Suspense fallback={<div>Loading...</div>}>
            <Switch>
                <Route exact path="/"  component={Home} />
                <Route path="/about"  component={About} />
            </Switch>
        </Suspense>
    </Router>
}
```

17.PureComponent与Component有何区别

- 实现了浅比较的shouleComponentUpdate
- 优化性能，但是要结合不可变值一起使用

18.React事件和DOM事件的区别

- 所有事件挂载到document上（React17开始挂载到root上）
- event不是原生的，是SyntheticEvent合成事件对象
- dispatchEvent

19.React性能优化

- 渲染列表时使用key
- 自定义事件、DOM事件及时销毁
- 合理使用异步组件
- 减少函数bind this次数
- 合理使用SCU PureComponent 和 memo
- 合理使用Immutable.js
- webpack层面的优化
- 前端通用的性能优化，如图片懒加载
- 使用SSR

20.React和Vue的区别

- 都支持组件化
- 都是数据驱动视图
- 都使用vdom操作DOM
- React使用JSX拥抱JS，Vue使用模板拥抱 html
- React函数式编程，Vue声明式编程