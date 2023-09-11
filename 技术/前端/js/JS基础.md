# JS基础

## 变量类型和计算

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

