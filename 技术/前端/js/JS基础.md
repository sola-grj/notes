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
