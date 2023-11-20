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
