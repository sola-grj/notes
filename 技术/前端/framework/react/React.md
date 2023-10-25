# React

## 基本使用

1.JSX语法

- 变量和表达式
- class和style
- 子元素和组件

2.条件

3.列表和渲染

4.事件

- bind this
- 关于event参数
  - react绑定事件event 是 SyntheticEvent（合成事件），event.nativeEvent是原生事件对象
  - React所有的事件都被挂载到document上（React16），和DOM事件不一样，和vue事件也不一样
- 传递自定义参数
- React16 事件绑定到document上，React17 事件绑定到root组件上，有利于多个React版本并存，例如微前端

5.组件和props（类型检查）

6.state和setState

- 不可变值
  - 不能直接修改state的值，要通过setState
- 可能是异步更新
  - 同步：setState回调函数中，DOM绑定监听事件中，setTimeout中
  - 异步：正常setState代码后面的就是异步的
- 可能会被合并
  - 合并：setState连续调用，并且传入的是对象
  - 不合并：setState传入的是函数不会合并
- React <= 17
  - React组件事件：异步更新 + 合并state
  - DOM事件，setTimeout：同步更新，不合并state
  - 只有React <= 17，React组件事件才做**批处理**
- React 18
  - React组件事件：异步更新 + 合并state
  - DOM事件，setTimeout：异步更新 + 合并state
  - Automatic Batching 自动批处理
  - React18所有事件都自动批处理Automatic Batching
  - React18操作一致，更加简单，降低了用户的心智负担

7.组件生命周期

- 挂载
- 更新
- 卸载

## 高级特性

1.函数组件

- 纯函数，输入 props，输出JSX
- 没有实例，没有生命周期，没有state
- 不能扩展其他方法

2.非受控组件

- ref
- defaultValue defaultChecked
- 手动操作DOM元素
- 使用场景
  - 必须手动操作DOM，setState实现不了
  - 文件上传`<input type="file" />`
  - 某些富文本编辑器，需要传入DOM元素
- 优先使用受控组件，必须操作DOM时，再使用非受控组件

```js
import React, { Component } from "react"

export default class FeiShouKong extends Component {
  constructor(props) {
    super(props)
    this.state = {
      name: "sola",
    }
    this.nameInputRef = React.createRef()
    this.fileInputRef = React.createRef()
  }
  alertName = () => {
    const elem = this.nameInputRef.current
    alert(elem.value)
  }
  alertFile = () => {
    const elem = this.fileInputRef.current
    alert(elem.files[0].name)
  }
  render() {
    return (
      <div>
        <input defaultValue={this.state.name} ref={this.nameInputRef} />
        <span>{this.state.name}</span>
        <br />
        <button onClick={this.alertName}>alert name</button>

        <input type="file" ref={this.fileInputRef} />
        <button onClick={this.alertFile}>alert file</button>
      </div>
    )
  }
}
```

3.Portals

使用场景

- overflow:hidden
- 父组件 z-index值太小
- fixed需要放在body第一层

```js
import React, { Component } from "react"
import { ReactDOM } from "react"
import "./PortalDemo.css"
export default class PortalDemo extends Component {
  constructor(props) {
    super(props)
    this.state = {}
  }
  render() {
    // return <div className="modal">
    //     {this.props.children}
    // </div>
    return ReactDOM.createPortal(
      <div className="modal">{this.props.children}</div>,
      document.body
    )
  }
}
```

4.context

- 公共信息（语言、主题）如何传递给每个组件

```js
import React, { Component } from "react"

// 创建Context填入默认值（任何一个js变量）
const ThemeContext = React.createContext("light")

function ThemeLink(props) {
  return (
    <ThemeContext.Consumer>
      {(value) => <p>link's theme is {value}</p>}
    </ThemeContext.Consumer>
  )
}

class ThemedButton extends Component {
  //   static contextType = ThemeContext
  render() {
    const theme = this.context
    return <div>button's theme is {theme}</div>
  }
}
ThemedButton.contextType = ThemeContext // 指定 contextType 读取当前的 context

function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
      <ThemeLink />
    </div>
  )
}

export default class ContextDemo extends Component {
  constructor(props) {
    super(props)
    this.state = {
      theme: "light",
    }
  }
  changeTheme = () => {
    this.setState({
      theme: this.state.theme === "light" ? "dark" : "light",
    })
  }
  render() {
    return (
      <ThemeContext.Provider value={this.state.theme}>
        <Toolbar />
        <hr />
        <button onClick={this.changeTheme}>change theme</button>
      </ThemeContext.Provider>
    )
  }
}
```

5.异步组件

- import()
- React.lazy
- React.Suspense

```js
import React, { Component } from "react"

const ContextDemo = React.lazy(() => import('./ContextDemo'))

export default class AsyncDemo extends Component {
  constructor(props) {
    super(props)
    this.state = {}
  }
  render() {
    return <div>
        <React.Suspense fallback={<div>Loading...</div>}>
            <ContextDemo/>
        </React.Suspense>
    </div>
  }
}
```

6.性能优化

React父组件更新，子组件则无条件更新

- shouldComponentUpdate

- PureComponent和React.memo

  - PureComponent，SCU中实现了浅比较

    ```js
    class List extends React.PureComponent {
      shouldComponentUpdate(){
        /*浅比较*/
      }
    }
    ```

  - memo，函数组件中的PureComponent

    ```js
    function MyComponent(props){
      /*使用props渲染*/
    }
    function areEqual(prevProps,nextProps){
      /*如果把nextProps传入render方法的返回结果与prevProps传入render方法的返回结果一致则返回true，否则返回false*/
    }
    export default React.memo(MyComponent,areEqual)
    ```

  - 浅比较已经适用大部分情况（尽量不要做深度比较）

- 不可变值 immutable.js

  - 彻底拥抱“不可变值”
  - 基于共享数据（不是深拷贝），速度好
  - 有一定学习成本

7.高阶组件(组件公共逻辑的抽离)

```js
import React, { Component } from "react"

const withMouse = (Component) => {
  class withMouseComponent extends React.Component {
    constructor(props) {
      super(props)
      this.state = {
        x: 0,
        y: 0,
      }
    }

    handleMouseMove = (event) => {
      this.setState({
        x: event.clientX,
        y: event.clientY,
      })
    }
    render() {
      return (
        <div style={{ height: "500px" }} onMouseMove={this.handleMouseMove}>
          {/*1.透传所有props 2.增加mouse属性 */}
          <Component {...this.props} mouse={this.state} />
        </div>
      )
    }
  }
  return withMouseComponent
}

const App = (props) => {
  const { x, y } = props.mouse
  return (
    <div style={{ height: "500px" }}>
      <h1>
        the mouse position is ({x},{y})
      </h1>
    </div>
  )
}

export default withMouse(App) // 返回高阶函数
```

8.Render Props(组件公共逻辑的抽离)

```js
import React, { Component } from "react"
import PropTypes from 'prop-types'

class Mouse extends Component {
  constructor(props) {
    super(props)
    this.state = {
      x: 0,
      y: 0,
    }
  }
  handleMouseMove = (event) => {
    this.setState({
      x: event.clientX,
      y: event.clientY,
    })
  }
  render() {
    return (
      <div style={{ height: "500px" }} onMouseMove={this.handleMouseMove}>
        {this.props.render(this.state)}
      </div>
    )
  }
}

Mouse.propTypes = {
  render: PropTypes.func.isRequired,
}

const App = (props) => {
  ;<div style={{ height: "300px" }}>
    <Mouse
      render={({ x, y }) => (
        <h1>
          the mouse position is ({x},{y})
        </h1>
      )}
    />
  </div>
}

export default App
```

