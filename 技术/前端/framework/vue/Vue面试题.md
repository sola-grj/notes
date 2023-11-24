# Vue面试题

1.v-show与v-if 的区别

- v-show通过css display控制显示和隐藏
- v-if 组件真正的渲染和销毁，而不是显示和隐藏
- 频繁切换显示状态用v-show，否则用v-if

2.为何在v-for中使用key

- diff算法中通过tag和key来判断，是否是sameNode
- 减少渲染次数，提升渲染性能

3.描述Vue组件生命周期

- 单组件
  - 挂载
    - beforeCreate
    - created
    - beforeMount
    - mounted
  - 更新
    - beforeUpdate
    - updated
  - 卸载
    - boforeDestroy
    - destroyed
- 父子组件
  - 挂载
    - 父组件created
    - 子组件created
    - 子组件mounted
    - 父组件mounted
  - 更新
    - 父组件beforeUpdate
    - 子组件beforeUpdate
    - 子组件updated
    - 父组件updated
  - 卸载
    - 父组件beforeDestroy
    - 子组件beforeDestroy
    - 子组件destroyed
    - 父组件destroyed

4.Vue组件如何通讯

- 父子组件 props 和  this.$emit
- 自定义事件 event.$no event.$off event.$emit
- vuex

5.描述组件渲染和更新的过程

6.双向数据绑定v-model 的实现原理

- input元素的value = this.name
- 绑定input事件this.name = $event.target.value
- data更新触发re-render

7.对MVVM的的理解

8.computed有何特点

- 缓存，data不变不会重新计算，提高性能

9.为何组件data必须是一个函数？

- .vue 文件编译后是一个class，如果data不是函数，那么在不同位置实例化的时候，data中的变量会互相影响。如果data是一个函数，在执行函数的时候，会把变量通过闭包的形式存储起来，而不会互相影响

10.ajax请求应该放在哪个生命周期

- mounted

11.如何将组件所有props传递给子组件

- $props
- `<User v-bind="$props" />`

12.如何实现v-model

```vue
<template>
<input type="text" :value="text" @input="$emit('change',$event.target.value)" />
</template>
<script>
    export default {
        model:{
            prop:text, // 对应到props text
            event:'change'
        },
        props:{
            text:String
        }
    }
</script>
```

13.多个组件有相同的逻辑，如何抽离

- mixin以及它的一些缺点

14.何时要使用异步组件

- 加载大组件
- 加载路由组件

15.何时需要使用keep-alive

- 缓存组件，不需要重复渲染
- 如多个静态tab页的切换
- 优化性能

16.何时使用beforeDestroy

- 接触自定义事件event.$off
- 清除定时器
- 解绑自定义的DOM事件，如window sroll等

17.什么是作用域插槽

子组件

```vue
<template>
<a :href="url">
    <slot :website="website">
        {{website.subtitle}}
    </slot>
    </a>
</template>
<script>
    export default {
        data(){
            return {
                website:{
                    url:'xxx.com',
                    title:'wangEditor',
                    subTitle:'轻量级富文本编辑器'
                }
            }
        },
        props:{
            url:String
        }
    }
</script>
```

使用作用域插槽

```vue
<ScopedSlotDemo :url="website.url">
    <template v-slot="slotProps">
		{{/*slotProps名字可以自定义*/}}
		{{slotProps.website.title}}
    </template>
</ScopedSlotDemo>
```

18.Vuex中action和mutation的区别

- action处理异步，mutation不可以
- mutation做原子操作
-  action可以整合多个mutation

19.Vue-router常用的路由模式

- hash默认
- history
- 两者比较

20.如何配置Vue-router加载

```js
export default new VueRouter({
    routes:[
        {
            path:'/',
            component:() => import(
                /*webpackChunkName: "navigator"*/
                './../components/Navigator'
            )
        },
        {
            path:'/feedback',
            component:() => import(
                /*webpackChunkName: "feedback"*/
                './../components/Feedback'
            )
        }
    ]
})
```

21.请用vnode秒是一个DOM结构

```js
{
    tag:'div',
    props:{
        className:'container',
        id:'div1'
    },
    children:[
        {
            tag:'p',
            children:'vdom'
        },
        {
            tag:'ul',
            props:{style:'font-size:20px'},
            children:[
                {
                    tag:'li',
                    children:'a'
                }
                // ...
            ]
        }
    ]
}
```

22.监听data变化的核心API是什么

- Object.defineProperty

23.Vue如何监听数组变化

- 重新定义原型，重写push pop方法，实现监听
- proxy可以监听数组变化

24.请描述响应式原理

- 监听data变化
- 组件渲染和更新的流程

25.diff算法的时间复杂度以及diff算法的过程

- O(n)

26.Vue为何是异步渲染，$nextTick有什么作用

- 异步渲染以及合并data修改，以提高渲染性能
- $nextTick 在DOM更新完之后，触发回调 

27.Vue常见的性能优化

- 合理使用v-show和v-if
- 合理使用computed
- v-for时加key，以及避免和v-if同时使用
- 自定义事件、DOM事件及时销毁
- 合理使用异步组件
- 合理使用keep-alive
- data层级不要太深
- webpack层面的优化

28.Vue3比Vue2有什么优势

- 性能更好
- 体积更小
- 更好的ts支持
- 更好的代码组织
- 更好的逻辑抽离
- 更多的新功能

29.描述Vue3的生命周期

- beforeDestroy改为beforeUnmount
- destroyed改为 unmounted
- 其他沿用Vue2的生命周期

30.如何看待Composition API和Option API

31.如何理解ref toRef 和 toRefs

- ref 
  - 生成值类型的响应数据，可用于模板和reactive
  - 通过 .value 修改值
- toRef
  - 针对一个响应式对象（reactive封装）的prop
  - 创建一个ref，具有响应式
  - 两者保持引用关系
- toRefs
  - 将响应式对象（reactive封装）转换为普通对象，依旧保留对象中属性的响应式
  - 对象的每个prop都是对应的ref
  - 两者保持引用关系
- 最佳使用方式
  - 用reactive做对象的响应式，用ref做值类型的响应式
  - setup中返回toRefs(state),或者toRef(state,'xxx')
  - ref的变量命名都用xxxRef
  - 合成函数返回响应式对象时，使用toRefs
- 为什么要使用这几个API
  - 为何需要ref？
    - 返回值类型，会丢失响应式
    - 如在setup、computed、合成函数，都有可能返回值类型
    - Vue如不定义ref，用户将自造ref，反而混乱
  - 为何需要 .value
    - ref是一个对象（不丢失响应式），value存储值
    - 通过 .value属性的get和set实现响应式
    - 用于模板、reactive时，不需要. value 其他情况都需要
  - 为何需要toRef和toRefs
    - 初衷：不丢失响应式的情况下，把对象数据 分解/扩散
    - 前提：针对的是响应式对象（reactive封装的）非普通对象
    - 不创造响应式，而是延续响应式

32.Vue3升级了哪些重要的功能？

- createApp

  ```js
  // vue2.x
  const app = new Vue({/*选项*/})
  Vue.use(/**/)
  Vue.mixin(/**/)
  Vue.component(/**/)
  Vue.directive(/**/)
  
  // vue3
  const app = Vue.createApp(/**/)
  app.use(/**/)
  app.mixin(/**/)
  app.component(/**/)
  app.directive(/**/)
  ```

- emits属性

  ```vue
  /*父组件*/
  <HelloWorld :msg:"msg" @onSayHello="onSayHello" />
  
  /*子组件*/
  export default {
  	name:'HelloWorld',
  	props:{
  		msg:String
  	},
  	emits:['onSayHello'],
  	setup(props,{emit}){
  		emit('onSayHello','extra params')
  	}
  }
  ```

- 生命周期

- 多事件

  ```vue
  <button @click="one($event),two($event)">
      Submit
  </button>
  ```

- Fragment

  ```vue
  <!-- vue2.x 组件模板 -->
  <template>
  	<div class="xxx">
          <h3>
              <div>
              	xxx    
      		</div>
      	</h3>
      </div>
  </template>
  
  <!-- vue3.x 组件模板 -->
  <template>
  	<h3>
          {{title}}
      </h3>
  	<div>
          xxx
      </div>
  </template>
  ```

- 移除.sync 父子组件对props进行双向数据绑定的语法糖

  ```vue
  <!-- vue2.x -->
  <Component v-bind:title.sync="title" />
  
  <!-- vue3.x -->
  <Component v-model:title="title" />
  <!-- 等价于下面的写法 -->
  <Component :title="title" @update:title="title = $event" />
  ```

- 异步组件的写法

  ```vue
  <!-- vue2.x -->
  new Vue({
  	components:{
  		'my-component':() => import('./my-component.vue')
  	}
  })
  
  <!-- vue3.x -->
  import {createApp,defineAsyncComponent} from 'vue'
  createApp({
  	components:{
  		'AsyncComponent':defineAsyncComponent(() => import('./my-component.vue'))
  	}
  })
  ```

- 移除filter

  ```vue
  <!-- 以下filter在vue3中不可用了 -->
  
  <!-- 在双花括号中 -->
  {{message | capitalize}}
  
  <!-- 在v-bind中 -->
  <div v-bind:id="rawId | formatId"></div>
  ```

- Teleport

  ```vue
  <button @click="modalOpen = true">
      open full screen modal with teleport
  </button>
  
  <teleport to="body">
      <div v-if="modalOpen" class="modal">
          <div>
              teltport 弹窗（父元素是body）
              <button @click="modalOpen = false">Close</button>
          </div>
      </div>
  </teleport>
  ```

- Suspense

  ```vue
  <Suspense>
      <template>
  		<Test1 /> <!--是一个异步组件-->
      </template>
      
      <!--#fallback就是一个具名插槽 即Suspense组件内部，有两个slot，其中一个具名为fallback-->
      <template #fallback>Loading...</template>
  </Suspense>
  ```

- Composition API

  - reactive
  - ref相关
  - readonly
  - watch和watchEffect
  - setup
  - 生命周期钩子函数

33.Composition API如何实现代码逻辑复用

- 抽离逻辑代码到一个函数

- 函数命名约定为useXxx格式（React Hooks）

- 在setup中引用该函数

  ```js
  import { ref,reactive,toRefs,onMounted,onUnmounted } from "vue";
  // export default function useMousePosition(params) {
  //     const x = ref(0)
  //     const y = ref(0)
  
  //     function update(e) {
  //         x.value = e.pageX
  //         y.value = e.pageY
  //     }
  
  //     onMounted(() => {
  //         window.addEventListener('mousemove',update)
  //     })
  
  //     onUnmounted(() => {
  //         window.removeEventListener('mousemove',update)
  //     })
  
  //     return {
  //         x,
  //         y
  //     }
  // }
  
  export default function useMousePosition(params) {
  
      const state = reactive({
          x:0,
          y:0
      })
  
      function update(e) {
          state.x = e.pageX
          state.y = e.pageY
      }
  
      onMounted(() => {
          window.addEventListener('mousemove',update)
      })
  
      onUnmounted(() => {
          window.removeEventListener('mousemove',update)
      })
  
      return toRefs(state)
  }
  ```

34.Vue3如何实现响应式

- Object.defineProperty

  - 深度监听需要一次性递归
  - 无法监听新增属性、删除属性（Vue.set Vue.delete）
  - 无法原生监听数组，需要特殊处理

- Proxy

  ```js
  const data = {name:'sola',age:20}
  const proxyData = new Proxy(data,{
      get(target,key,receiver){
          // 只处理本身（非原型的）属性
          const ownKeys = Reflect.ownKeys(target)
          if(ownKeys.includes(key)){
              console.log('get',key)
          }
          const result = Reflect.get(target,key,receiver)
          return result //返回结果
      },
      set(target,key,val,receiver){
          // 不重复修改数据，重复数据不处理
          if(val === target[key]){
              return true
          }
          const result = Reflect.set(target,key,val,receiver)
          console.log('set',key,val)
          console.log('result',result) // true
          return result
      },
      deleteProperty(target,key){
          const result = Reflect.deleteProperty(target,key)
          console.log('delete property',key)
          console.log('result',result) // true
          return result
      }
  })
  
  function reactive(target={}){
      if(typeof  target !== 'object' || target == null){
          return target
      }
      // 代理配置
      const proxyConf = {
          get(target,key,receiver){
          // 只处理本身（非原型的）属性
          const ownKeys = Reflect.ownKeys(target)
          if(ownKeys.includes(key)){
              console.log('get',key)
          }
          const result = Reflect.get(target,key,receiver)
          
          // 深度监听
          // 如何提升性能的？Object.defineProperty是一次性递归监听，而Proxy对象则是 调用到某一层级的时候才对该层级进行响应式监听
          return reactive(result) //返回结果
          },
          set(target,key,val,receiver){
              // 不重复修改数据，重复数据不处理
              if(val === target[key]){
                  return true
              }
              const ownKeys = Reflect.ownKeys(target)
              if(ownKeys.includes(key)){
                  console.log('已有的key',key)
              }else{
                  console.log('新增的key')
              }
              const result = Reflect.set(target,key,val,receiver)
              console.log('set',key,val)
              console.log('result',result) // true
              return result
          },
          deleteProperty(target,key){
              const result = Reflect.deleteProperty(target,key)
              console.log('delete property',key)
              console.log('result',result) // true
              return result
          }
      }
  }
  ```

  - Reflect作用
    - 和Proxy能力一一对应
    - 规范化、标准化、函数式
    - 替代Object上的工具函数
  - Proxy能规避 Object.defineProperty的问题，但是Proxy无法兼容所有浏览器，无法polyfill

35.watch和watchEffect的区别是什么

- 两者都可监听data属性变化

- watch需要明确监听哪个属性

- watchEffect会根据其中的属性，自动监听其变化

  ```vue
  <template>
    
  </template>
  
  <script>
  import {ref,reactive,watch,watchEffect} from 'vue'
  export default {
      name:'WatchTest',
      setup(){
          const ageRef = ref(20)
          const state = reactive({
              name:'sola',
              age:19
          })
          watch(ageRef,(newVal,oldVal) => {
              console.log('watch=========',newVal,oldVal);
          })
          watch(
              () => state.name,
              (newVal,oldVal) => {
                  console.log('watch reactive state=========',newVal,oldVal)
              },
              {
                  immediate:true
              }
          )
          setTimeout(() => {
              // ageRef.value = '200'
              state.name = 'sola666'
              state.age = 999
          },1000)
  
          watchEffect(() => {
              console.log('watcheffect=====',state.age,state.name);
          })
      }
  }
  </script>
  
  <style>
  
  </style>
  ```

36.setup中如何获取组件实例

- 在setup和其他Composition API中没有this
- 可通过getCurrentInstance获取当前实例
- 若使用Options API可正常使用this

37.Vue3为何比Vue2快

- Proxy 响应式
- PatchFlag https://vue-next-template-explorer.netlify.app/
  - 编译模板时，动态节点做标记
  - 标记 ，分为不同的类型，如TEXT PROPS CLASS等等
  - diff算法时，可以区分静态节点，以及不同类型的动态节点
- hoistStatic
  - 将静态节点的定义，提升到父作用域，缓存起来
  - 多个相邻的静态节点，会被合并起来
  - 典型的拿空间换时间的优化策略

- cacheHandler
  - 缓存事件

- SSR优化
  - 静态节点直接输出，绕过了vdom
  - 动态节点，还是需要动态渲染

- tree-shaking
  - 编译时 ，根据不同的情况引用不同的API

38.Vite是什么

- 一个前端打包工具，开发环境无需打包，启动非常快

- 开发环境使用ES6 Module ，无需打包——非常快

- 生产环境使用rollup，并不会快很多

- ES6 Module

  ```html
  <script type="module">
      // ES6 Module普通引入
      import add from './src/add.js'
      import {add,multi} from './src/math.js'
  </script>
  <!--外链引用-->
  <script type="module" src="./src/math.js"></script>
  <!--远程引用-->
  <script type="module" src="https://unpkg.com/redux@latest/es/redux.mjs"></script>
  <!--动态引用-->
  <script type="module">
      document.getElementById('btn1',async () => {
          const add = await import('./src/add.js')
      })
      document.getElementById('btn2',async () => {
          const {add,multi} = await import('./src/add.js')
      })
  </script>
  ```

39.Comopsition API 和 React Hooks的对比

- Composition API setup只会被调用一次，而后者函数会被调用多次
- Composition API 不需要 useMemo useCallback，因为setup只会被调用一次
- Composition API无需考虑调用顺序，而后者需要保证hooks的顺序一致
- Composition API 的 reactive+ ref 比 ReactHooks要更难理解
