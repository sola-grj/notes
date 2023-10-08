# JS基础

## 变量类型和计算

### 面试题

1.typeof能判断哪些数据类型

- 值类型 str number boolean undefined Symbol
- 函数 function
- 引用类型 object

2.何时使用===何时使用==

``` js
// 除了 == null 之外，其他一律用 === 例如
const obj = {x:100}
if(obj.a == null) { }
// 相当于：
// if(obj.a === null || obj.a === undefined){}
```

3.值类型和引用类型的区别

- 值类型存储在栈中
- 引用类型，栈中存储引用类型对象的内存地址，内存地址所指向的真正的值存储在堆中

4.手写深拷贝

- 注意判断值类型和引用类型
- 注意判断对象是对象还是数组
- 递归

```js
function DeepClone(obj={}){
  if(typeof obj !== 'object' || obj == null){
    return obj
  }
  // 初始化返回结果
  let result
  if(obj instanceof Array){
    result = []
  }else{
    result = {}
  }
  
  for(let key in obj){
    // 保证key不是原型的属性
    if(obj.hasOwnProperty(key)){
      // 递归调用
      result[key] = DeepClone(obj[key])
    }
  }
  
  return result
}
```

## 原型和原型链

### 面试题

1.如何判断一个变量是不是数组？

- a instanceof Array

2.手写一个简易的jQuery，考虑插件和扩展性

```js
class jQuery {
  constructor(selector){
    const result = document.querySelectorAll(selector)
    const length = result.length
    for(let i = 0;i<length;i++){
      this[i] = result[i]
    }
    this.length = length
    this.selector = selector
  }
  get(index){
    return this[index]
  }
  each(fn){
    for(let i = 0;i<this.length;i++){
      const elem = this[i]
      fn(elem)
    }
  }
  on(type,fn){
    return this.each(elem => {
      elem.addEventListener(type,fn,false)
    })
  }
  
}

// 插件扩展性
jQuery.prototype.dialog = function(info){
  alert(info)
}

// 造轮子
class myJquery extends jQuery {
  constructor(selector){
    super(selector)
  }
  // 实现自己的方法
  addClass(className){
    
  }
}
```



3.class的原型本质，怎么理解？

- 原型和原型链

### 知识点

1.class和继承

2.类型判断instanceof

```js
[] instanceof Array // true
{} instanceof Object // true
{} instanceof object // true
```



3.原型和原型链
- 每个class都有一个显示原型`prototype`
- 每个实例对象都有一个隐式原型`__proto__`
- 实例对象的隐式原型指`__proto__`指向class的显示原型`prototype`

## 作用域与闭包

### 面试题

- this的不同应用场景，如何取值

- 手写bind函数

  ```js
  Function.prototype.bind1 = function(){
      // 将参数拆解为数组
      const args = Array.prototype.slice.call(arguments)
      
      //  获取this（数组第一项）
      const t = args.shift()
      
      // 获取当前对象 fn1.bind1(..) 中的fn1
      const self =  this
      
      // 返回一个函数
      return function(){
          return self.apply(t,args)
      }
  }
  
  function fn1(a,b,c){
      console.log('this',this)
      console.log(a,b,c)
      return 'this is fn1'
  }
  const fn2 = fn1.bind1({x:1},2,3,4)
  fn2()
  ```

  

- 实际开发中必报的应用场景

  - 隐藏数据

    ```js
    function createCache() {
        const data = {} // 闭包中的数据，被隐藏，不被外界访问
        return {
            set:funtion(key,val){
            data[key] = val
        	},
            get:funtion(key){
                return data[key]
            }
        }
    }
    
    const c = createCache()
    c.set('a',100)
    console.log(c.get('a'))
    ```

    

### 知识点

- 作用域和自由变量

  - 作用域

    - 全局作用域

    - 函数作用域

    - 块级作用域 ES6

      ```js
      if(true){
          let x = 100
      }
      console.log(x) // 会报错
      ```

  - 自由变量
    - 一个变量在当前作用域没有定义，但被使用了
    - 向上级作用域，一层一层依次寻找，直至找到为止
    - 如果到全局作用域都没找到，则报错：xxx is not defined

- 闭包

  - 函数作为参数被传递

    ```js
    function pring(fn){
        const a = 200
        fn()
    }
    const a = 100
    function fn(){
        console.log(a)
    }
    print(fn) // 100
    ```

    

  - 函数作为返回值

    ```js
    function create(){
        const a = 100
        return function(){
            console.log(a)
        }
    }
    const fn = create()
    const a = 200
    fn() // 100
    ```

  - 闭包情况下，所有的自由变量的查找规则：是在函数定义的地方，向上级作用域查找，而不是在执行的地方

- this

  - 作为普通函数
  - 使用call apply bind
  - 作为对象方法被调用
  - 在class方法中被调用
  - 箭头函数
  - 注意：this取什么值，是在函数执行的时候确认的，而不是在函数定义的时候确认的，

## 异步和单线程

### 面试题

- 同步和异步的区别是什么？

- 手写Promise加载一张图片

  ```js
  const url = 'xxx'
  
  function loadImg(src){
      return new Promise((resolve,reject) => {
          const img = document.createElement('img')
          img.onLoad = () => {
              resolve(img)
          }
          img.onError = () => {
              const err = new Error('img load error')
              reject(err)
          }
          img.src = src
      })
  }
  ```

  

- 前端使用异步的场景有哪些

### 知识点

- 单线程和异步
  - js是单线程语言，只能做一件事
  - 浏览器和nodejs已经支持js启动进程，如Web Worker
  - JS 和 DOM 渲染共用同一个线程，因为JS可以修改DOM结构
- 应用场景
  - 网络请求 如ajax图片加载
  - 定时任务 如setTimeout
- callback hell 和 Promise

