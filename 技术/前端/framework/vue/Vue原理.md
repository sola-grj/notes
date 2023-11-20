# Vue原理

1.组件化

- 数据驱动试图（MVVM，setState）

2.响应式

- 组件data的数据一旦发生变化，立刻触发视图的更新

- 核心API ：Object.defineProperty

  - 基本使用 

  ```js
  const data = {}
  const name = 'sola'
  Object.defineProperty(data,"name",{
      get:function(){
          consoole.log('get')
          return name
      },
      set:function(newVal){
          console.log(set)
          name = newVal
      }
  })
  // 测试
  console.log(data.name)
  data.name = 'piggy'
  ```

  - 监听对象，监听数组
  - 复杂对象，深度监听
  - 几个缺点
    - 深度监听，需要递归到底，一次性计算量大
    - 无法监听新增/删除属性（Vue.set Vue.delete）
    - 无法原生监听数组，需要特殊处理

  ```js
  function updateView(){
      console.log('视图更新')
  }
  
  // 重新定义数组原型
  const oldArrayProperty = Array.prototype
  // 创建新对象，原型指向 oldArrayProperty，在扩展新的方法不会影响原型
  const arrProto = Object.create(oldArrayProperty)
  ['push','pop','shift','unshift','splice'].forEacjh(methodName => {
      arrProto[methodName] = function(){
          updateView() // 触发试图更新
          oldArrayProperty[methodName].call(this,...arguments)
      }
  })
  
  function defineReactive(target,key,value){
      // 深度监听
      Observer(value)
      
      // 核心API
      Object.defineProperty(target,key,{
          get(){
              return value
          },
          set(newVal){
              if(newVal !== value){
                  // 深度监听
                  Observer(newVal)
                  
                  value = newVal
                  
                  // 触发更新视图
                  updateView()
              }
          }
      })
  }
  
  function Observer(target){
      if(typeof target !== 'object' || target === null){
          // 不是对象或者数组
          return target
      }
      
      if(Array.isArray(target)){
          target.__proto__ = arrProto
      }
      // 重新定义各个属性（for in 也可以遍历数组）
      for(let key in target){
          defineReactive(target,key,target[key])
      }
  }
  
  const data = {
      name:'sola',
      age:20,
      info:{
          address:'beijing'
      },
      nums:[10,20,30]
  }
  ```

  

- Object.defineProperty的一些缺点（Vue3.0启用Proxy）

- Proxy兼容性不好，且无法polyfill

3.vdom和diff

- vdom是实现vue和react的重要基石
- diff优化时间复杂度
  - 只比较同一层级，不跨级比较
  - tag不相同，则直接删除重建，不再做深度比较 
  - tag和key，两者都相同，则认为是相同节点，不再深度比较

4.模板编译 vue-template-compiler

- 模板不是HTML，有指令、插值、JS表达式，能实现判断、循环
- HTML是标签语言，只有js才能实现判断、循环（图灵完备的）
- 模板编译为render函数，执行render函数返回vnode
- 基于vnode在执行patch和diff
- 使用webpack vue-loader，会在开发环境下编译模板

- with语法

  - 改变{}内自由变量的查找规则，当作obj属性来查找
  - 如果找不到匹配的obj属性，就会报错
  - with要慎用，它打破了作用域规则，易读性变差

  ```js
  const obj = {a:100,b:200}
  // 使用with，能改变{}内自由变量的查找方式
  // 将{}内自由变量，当作obj的属性来查找
  with(obj){
      console.log(a)
      console.log(b)
      console.log(c) // 会报错！！
  }
  ```

- vue组件可以用render代替template

5.渲染过程

- 初次渲染过程
  1. 解析模板为render函数（或在开发环境已完成，vue-loader）
  2. 触发响应式，监听data属性getter setter
  3. 执行render函数，生成vnode，patch（elem，vnode）
- 更新过程
  1. 修改data，触发setter（此前在getter中已被监听）
  2. 重新执行render函数，生成newVnode
  3. patch（vnode，newVnode）
- 异步渲染
  - 汇总data的修改，一次性更新视图
  - 减少DOM操作次数，提高性能

6.前端路由

- vue-router

- hash

  - hash变化会触发网页跳转，即浏览器的前进后退
  - hash变化不会刷新页面，spa必需的特点
  - hash永远不会提交到server端

  ```js
  window.onhashchange = (event) => {
      console.log('old url',event.oldURL)
      console.log('new url',event.newURL)
      console.log('hash:',location.hash)
  }
  
  // 页面初次加载，获取hash
  document.addEventListener('DOMContentLoaded',() => {
      console.log('hash:',location.hash)
  })
  
  // JS 修改url
  document.getElementById('btn').addEventListener('click',function(){
      location.href = '#/user'
  })
  ```

- H5 history

  - 用url规范的路由，但跳转时不刷新页面
  - history

  ```js
  document.getElementById('btn').addEventListener('click',function(){
      const state = {name:'page1'}
      console.log('切换路由到','page1')
      history.pushState(state,'','page1')
  })
  // 监听浏览器前进后退
  window.onpopstate = (event) => {
      console.log('onpopstate',event.state,location.pathname)
  }
  ```

  