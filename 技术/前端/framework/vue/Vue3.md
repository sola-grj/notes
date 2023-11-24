# Vue3

createApp

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

emits属性

```js
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

多事件处理

```html
<button @click="one($event),two($event)">
    Submit
</button>
```

Fragment

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

移除 .sync改为v-model参数

- 父子组件对props进行双向数据绑定的语法糖

```vue
<!-- vue2.x -->
<Component v-bind:title.sync="title" />

<!-- vue3.x -->
<Component v-model:title="title" />
<!-- 等价于下面的写法 -->
<Component :title="title" @update:title="title = $event" />
```

异步组件的引用方式

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

移除filter

```vue
<!-- 以下filter在vue3中不可用了 -->

<!-- 在双花括号中 -->
{{message | capitalize}}

<!-- 在v-bind中 -->
<div v-bind:id="rawId | formatId"></div>
```

Teleport

- 将指定组件传送到对应的DOM下，类似React.Portals

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

Suspense

- 通过插槽，对异步组件做一定处理

```vue
<Suspense>
    <template>
		<Test1 /> <!--是一个异步组件-->
    </template>
    
    <!--#fallback就是一个具名插槽 即Suspense组件内部，有两个slot，其中一个具名为fallback-->
    <template #fallback>Loading...</template>
</Suspense>
```

Composition API

- reactive

- ref toRef toRefs

- readonly

- computed

- watch watchEffect

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

- 钩子函数生命周期

原理

- Proxy实现响应式

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

- 编译优化
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

Vite与ES6 Module

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

CompositionAPI与ReactHooks的对比

- Composition API setup只会被调用一次，而后者函数会被调用多次
- Composition API 不需要 useMemo useCallback，因为setup只会被调用一次
- Composition API无需考虑调用顺序，而后者需要保证hooks的顺序一致
- Composition API 的 reactive+ ref 比 ReactHooks要更难理解

Vue3和JSX

1.Vue3中JSX的基本使用

- 使用 .jsx格式文件 和defineComponent
  - defineComponent可传入的两种形式
    1. setup函数
    2. 组件的配置
- 引用自定义组件，传递属性

```jsx
// 父组件
import { ref,defineComponent } from "vue";
import Child from "./Child";
export default defineComponent(() =>{
    const countRef = ref(120)
        return () => {
            return <>
            <p>demo {countRef.value}</p>
            <Child a={countRef.value + 100} />
            </>
        }
})

// 子组件
import {defineComponent} from 'vue'

export default defineComponent({
    props:['a'],
    setup(props){
        const render = () => {
            return <p>Child {props.a}</p>
        }
        return render
    }
})
```

2.JSX和template的区别

- 语法上有很大的区别，但是本质是相同的
  - JSX本质就是js代码，可以使用js的任何能力
  - template只能嵌入简单的js表达式，其他需要指令，如v-if
  - JSX已经成为ES规范，template还是Vue自家规范

3.JSX和slot

- slot是Vue发明的概念，为了完善template的能力

- vue的作用域插槽

  ```vue
  <!-- 父组件 -->
  <template>
    <child>
      <template v-slot:default="slotProps">
          <div>123123{{ slotProps.msg }}</div>
      </template>
    </child>
  </template>
  
  <script>
  import Child from './Child.vue';
  export default {
      components:{Child}
  }
  </script>
  
  <!-- 子组件 -->
  <template>
    <slot :msg="msg"></slot>
  </template>
  
  <script>
  export default {
  data(){
      return {
          msg:'hello world'
      }
  }
  }
  </script>
  ```

- jsx作用域插槽

  ```jsx
  // 父组件
  import {defineComponent} from 'vue'
  import Child from './Child'
  
  export default defineComponent(() => {
      function render(msg) {
          return <p>msg:{msg} </p>
      }
      return () => {
          return <>
          <p>demo</p>
          <Child render={render} />
          </>
      }
  })
  
  // 子组件
  import {defineComponent,ref} from 'vue'
  
  export default defineComponent({
      props:['render'],
      setup(props){
          const msgRef = ref('作用域插槽 Child——JSX')
          return () => {
              return <p>{props.render(msgRef.value)}</p>
          }
      }
  })
  ```

  

4.Vue3 script setup（Vue3.2以上）

- Vue3引入了composition API

- 基本使用

  - 顶级变量、自定义组件，可以直接用于模板
  - 可正常使用 ref reactive computed 等能力
  - 和其他 script 同时使用

- 属性和事件

  - defineProps
  - defineEmits

  ```vue
  <!--父组件-->
  <template>
    <p @click="addCount">{{ countRef }}</p>
    <p>{{ name }}</p>
    <hr>
    <child-2 :name="name" :age="countRef" @change="onChange" @delete="onDelete" />
  </template>
  
  <script setup>
  import { ref, reactive, toRefs } from "vue";
  import Child2 from "./Child2.vue";
  const countRef = ref(100);
  const state = reactive({
    name: "sola",
  });
  const { name } = toRefs(state);
  
  function addCount() {
    countRef.value++;
  }
  
  function onChange(info) {
      console.log('on change',info);
  }
  function onDelete(info) {
      console.log('on onDelete',info);
  }
  </script>
  <!--子组件-->
  <script setup>
  import {defineProps,defineEmits} from 'vue'
  
  // 定义属性
  const props = defineProps({
      name:String,
      age:Number
  })
  
  // 定义事件
  const emit = defineEmits(['change','delete'])
  function deleteHandler() {
      emit('delete','aaa')
  }
  </script>
  <template>
      <p>Child2- name:{{ props.name }} age:{{ props.age }}</p>
      <button @click="$emit('change','bbb')">change</button>
      <button @click="deleteHandler">delete</button>
  </template>
  ```

- defineExpose

  - 暴露数据给父组件

  ```vue
  <!--父组件-->
  <template>
    <child-2 ref="childRef" />
  </template>
  
  <script setup>
  import { ref, reactive, toRefs,onMounted } from "vue";
  import Child2 from "./Child2.vue";
  const childRef = ref(null)
  onMounted(() => {
      // 获取到子组件的一些数据
      console.log(childRef.value.childName);
      console.log(childRef.value.childAge);
  })
  </script>
  
  <!--子组件-->
  <script setup>
  import {defineProps,defineEmits,ref,defineExpose} from 'vue'
  // 子组件自定义一些属性
  const childName = ref('sola')
  const childAge = ref(18)
  defineExpose({
      childName,
      childAge
  })
  return {
      childName,
      childAge
  }
  </script>
  <template>
      <p>Child2- name:{{ childName }} age:{{ childAge }}</p>
  </template>
  ```

  