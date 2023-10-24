# Vue

## 基本使用

1.插值、表达式

2.指令、动态属性

3.v-html：会有XSS风险，会覆盖子组件

4.computed和watch

- computed有缓存，data不变则不会重新计算
- watch监听引用类型，拿不到oldVal

5.class和style

- 使用动态属性
- 使用驼峰式写法

6.v-if v-else用法，可使用变量，也可以用 === 表达式

- v-if 与 v-show的区别
- v-if 与 v-show的使用场景

7.循环渲染

- 如何遍历对象 v-for
- key的重要性 key不能乱写（如random或者index）
- v-for和v-if不能一起使用

8.事件

- event参数，自定义参数

- 事件修饰符

  ```html
  <!-- 阻止单击事件继续传播 -->
  <a v-on:click.stop="doThis"></a>
  <!-- 提交事件不再重载页面 -->
  <form v-on:submit.prevent="onSubmit"></form>
  <!-- 修饰符可以串联 -->
  <a v-on:click.stop.prevent="doThat"></a>
  <!-- 只有修饰符 -->
  <a v-on:submit.prevent></a>
  <!-- 添加事件监听器时使用事件捕获模式 -->
  <!-- 即内部元素触发的事件先在此处理，然后才交由内部元素进行处理 -->
  <div v-on:click.capture="doThis"></div>
  <!-- 只当在event.target是当前元素自身时触发处理函数 -->
  <!-- 即事件不是从内部元素触发的 -->
  <div v-on:click.self="doThat"></div>
  ```

- 按键修饰符

  ```html
  <!-- 即使alt或shift被一同按下时也会触发 -->
  <button @click.ctrl="onClick">A</button>
  
  <!-- 有且只有Ctrl被按下的时候才会触发 -->
  <button @click.ctrl.exact="onCtrlClick">A</button>
  
  <!-- 没有任何系统修饰符被按下的时候才会触发 -->
  <button @click.exact="onClick">A</button>
  ```

  

- 事件被绑定到哪里：event是原生的，事件被挂载到当前元素

9.表单

- v-model
- 常见表单项 textarea checkbox radio select
- 修饰符 lazy number trim

## Vue组件使用

1.props和$emit

- props父组件向子组件传递属性
- $emit 子组件触发父组件的一个事件

2.组件间通信 - 自定义事件

3.组件生命周期

- 挂载阶段
  - beforeCreate
  - Created
  - beforeMount
  - Mounted
- 更新阶段
  - beforeUpdate
  - updated
- 销毁阶段
  - beforeDestroy
  - destroyed
- 父子组件生命周期
  - 挂载阶段
    1. 父组件 created
    2. 子组件 created
    3. 子组件 mounted
    4. 父组件 mounted
  - 更新阶段
    1. 父组件 beforeUpdate
    2. 子组件 beforeUpdate
    3. 子组件 updated
    4. 父组件 updated
  - 销毁阶段
    1. 父组件 beforeDestroy
    2. 子组件 beforeDestroy
    3. 子组件 destroyed
    4. 父组件 destroyed

## Vue高级特性

1.自定义v-model

```vue
父组件
<template>
  <div>
    <p>vue 高级特性</p>
  <hr/>
  <!-- 自定义v-model -->
    <p>{{ name }}</p>
    <CustomVModelUse v-model="name" />
  </div>
  

</template>

<script>
import CustomVModelUse from './CustomVModelUse.vue';
export default {
    components:{
        CustomVModelUse
    },
    data(){
        return {
            name:'sola'
        }
    }
}
</script>

<style>

</style>
子组件
<template>
  <input type="text" :value="text1" @input="$emit('change',$event.target.value)" />
  <!--
    1.上面的input使用了 :value 而不是 v-model
    2.上面的change 和 model.event 要对应起来
    3.text1 属性对应起来
  -->
</template>

<script>
export default {
  model: {
    prop: "text1",
    event: "change",
  },
  props: {
    text1: String,
    default() {
      return "";
    },
  },
};
</script>

<style>
</style>
```



2.$nextTick

- Vue是异步渲染，data改变之后，DOM不会立刻渲染

- $nextTick会在DOM渲染之后被触发，以获取最新DOM节点

  ```vue
  <template>
    <div>
      <ul ref="ul1">
        <li :key="index" v-for="(item, index) in list">
          {{ item }}
        </li>
      </ul>
      <button @click="addItem">add</button>
    </div>
  </template>
  
  <script>
  export default {
    name: "app",
    data() {
      return {
        list: ["a", "b", "c"],
      };
    },
    methods: {
      addItem() {
        this.list.push(`${Date.now()}`);
        this.list.push(`${Date.now()}`);
        this.list.push(`${Date.now()}`);
  
        // 1.异步渲染，$nextTick 待 DOM渲染完再回调
        // 2. 页面渲染时会将data的修改做整合，多次data修改只会渲染一次
        this.$nextTick(() => {
          // 获取DOM元素
          const ulElem = this.$refs.ul1;
          console.log(ulElem.childNodes.length);
        })
  
        
      },
    },
  };
  </script>
  
  <style>
  </style>
  ```

3.slot

- 普通插槽

- 作用域插槽
- 具名插槽

```vue
<!-- 父组件 -->
<template>
  <div>
    <p>vue 高级特性</p>
  <hr/>
    <!-- slot -->
    <slot-demo :url="webSite.url">
        {{ webSite.title }}
    </slot-demo>

    <scoped-slot-demo :url="webSite.url">
        <template v-slot="slotProps">
            {{ slotProps.slotData.title }}
        </template>
    </scoped-slot-demo>

    <named-slot>
        <template v-slot:header>
            <h1>将插入 header slot 中</h1>
        </template>

        <p>将插入到 main slot 中，即未命名的额slot</p>

        <template v-slot:footer>
            <h1>将插入 footer slot 中</h1>
        </template>
    </named-slot>
  </div>
  

</template>

<script>
import CustomVModelUse from './CustomVModelUse.vue';
import NamedSlot from './NamedSlot.vue';
import NextTickVue from './NextTick.vue';
import ScopedSlotDemo from './ScopedSlotDemo.vue';
import SlotDemo from './SlotDemo.vue';
export default {
    components:{
        SlotDemo,
        ScopedSlotDemo,
        NamedSlot
    },
    data(){
        return {
            name:'sola',
            webSite:{
                url:'http://baidu.com',
                title:'SOLA',
                subTitle:'sola is a boy'
            }
        }
    }
}
</script>

<style>

</style>

<!-- 子组件 slotDemo.vue 普通插槽 -->
<template>
  <a :href="url">
    <slot> 默认内容，即父组件没设置内容的时候，这里显示 </slot>
  </a>
</template>

<script>
export default {
    props:['url'],
    data(){
        return {}
    }
}
</script>

<style>

</style>


<!-- 子组件 ScopedSlotDemo.vue 作用域插槽 -->
<template>
  <a :href="url">
    <slot :slotData="website">
      {{ website.subTitle }}
    </slot>
  </a>
</template>

<script>
export default {
  props: ["url"],
  data() {
    return {
      website: {
        url: "http://wangEditor.com",
        title: "wangEditor",
        subTitle: "富文本",
      },
    };
  },
};
</script>

<style>
</style>

<!-- 子组件 NamedSlot.vue 具名插槽 -->
<template>
  <div>
    <header>
      <slot name="header"></slot>
    </header>
    <main>
      <slot></slot>
    </main>
    <footer>
      <slot name="footer"></slot>
    </footer>
  </div>
</template>

<script>
export default {};
</script>

<style>
</style>
```

4.动态异步组件 

- 动态组件

  - :is="component-name"

  ```vue
  <template>
    <div>
      <p>vue 高级特性</p>
    <hr/>
      <!-- 动态组件 -->
      <component :is="nextTickData" />
      <div v-for="(item,index) in newsData" :key="index">
          <component :is="item" />
      </div>
      
    </div>
    
  
  </template>
  
  <script>
  import NextTickVue from './NextTick.vue';
  export default {
      components:{
          NextTickVue,
      },
      data(){
          return {
              name:'sola',
              webSite:{
                  url:'http://baidu.com',
                  title:'SOLA',
                  subTitle:'sola is a boy'
              },
              nextTickData:'NextTickVue',
              newsData:[
                  'text',
                  'img',
                  'text'
              ]
          }
      }
  }
  </script>
  
  <style>
  
  </style>
  ```

  

- 异步组件

  - import 函数
  - 按需加载，异步加载

  ```vue
  <template>
    <div>
      <p>vue 高级特性</p>
    <hr/>
      <!-- 异步组件 -->
      <AsyncComponent v-if="showAsync" />
      <button @click="changeAsyncStatus">click</button>
    </div>
  </template>
  
  <script>
  export default {
      components:{
          AsyncComponent:() => import('./AsyncComponent.vue')
      },
      data(){
          return {
              showAsync:false
          }
      },
      methods:{
          changeAsyncStatus(){
              this.showAsync = !this.showAsync
          }
      }
  }
  </script>
  
  <style>
  
  </style>
  ```

5.keep-alive

- 缓存组件，频繁切换，不需要重复渲染
- vue常见性能优化

6.mixin