# React原理

函数式编程

vdom和diff

JSX本质

- React.createElement 即h函数，返回vnode
- 第一个参数，可能是组件，也可能是 html tag
- 组件名，首字母必须大写（React规定）

合成事件

- 所有事件挂载到document上（React 17 开始就不是了，绑定到root上）
- event不是原生的，是SyntheticEvent合成事件对象
- 为什么需要合成事件机制？
  - 更好的兼容性和跨平台
  - 挂载到document，减少内存消耗，避免频繁解绑
  -  方便事件的统一管理（如事务机制）

setState batchUpdate

- 有时异步（普通使用），有时同步（setTimeout、DOM事件）
- 有时合并（对象合并），有时不合并（函数形式）
- setState主流程
  - setState无所谓异步还是同步，看是否能命中batchUpdate机制，主要需要通过判断isBatchingUpdates
- batchUpdate机制
  - 那些能命中batchUpdate机制
    - 生命周期（和调用它的函数）
    - React中注册的事件（和它调用的函数）
    - React可以管理的入口
  - 哪些不能命中batchUpdate机制
    - setTimeout setInterval 等
    - 自定义的DOM事件
    - React管不到的入口
- transaction事务机制

组件渲染与更新

- JSX如何渲染为页面
  - JSX即createElement函数
  - 执行生成vnode
  - patch(elem,vnode)和patch(vnode,newVnode)
- setState之后如何更新页面

组件渲染过程

- props state
- render() 生成vnode
- patch(elem,vnode)

组件更新过程

- setState(newState) ——> dirtyComponents(可能有子组件)
- render()生成newVnode
- patch(vnode,newVnode)
- 更新的两个阶段
  - reconsiliation 阶段 执行 diff算法，纯js计算
  - commit阶段 将diff结果渲染到DOM
- fiber解决JS单线程且和DOM渲染共用同一线程的问题
  - 将 reconciliation阶段进行任务拆分（commit无法拆分）
  - DOM休要渲染的时候暂停，空闲时恢复
  - window.requestIdleCallback