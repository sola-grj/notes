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