# JS异步进阶

## eventloop

- js如何设执行？从前置后，一行一行执行，如果某一行报错，则停止下面代码的执行，先把同步代码执行完，再执行异步
- DOM事件和event loop，DOM事件也使用回调，基于event loop
- 调用栈空闲的时候，尝试DOM渲染，在触发event loop

同步代码，一行一行放在call stack执行，遇到异步，会先“记录”下，等待时机（定时，网络请求等），时机到了，就将其移动到callback queue中，等待call stack为空（即同步代码执行完毕）Event loop开始工作，轮询查找Callback Queue，如有则移动到Call stack执行

## promise进阶

- 三种状态

  - pending 不会触发then和catch
  - resolved 触发then
  - rejected 触发catch

- 状态的表现和变化 pending—> resolved pending—> rejected

- then和catch对状态的影响

  - then正常返回resolved，里面有报错则返回rejected
  - catch正常返回resolved，里面有报错，则返回rejected

  ```js
  // 第一题
  Promise.resolve().then(() => {
      console.log(1) // 1
  }).catch(() => {
      console.log(2)
  }).then(() => {
      console.log(3) // 3
  })
  
  // 第二题
  Promise.resolve().then(() => {
      comsole.log(1) // 1
      throw new Error('error1')
  }).catch(() => {
      console.log(2) // 2
  }).then(() => {
      console.log(3) // 3
  }) // resolved
  
  // 第三题
  Promise.resolve().then(() => {
      console.log(1) // 1
      throw new Error('error1')
  }).catch(() => {
      console.log(2) // 2 resolved
  }).catch(() => {
      console.log(3)
  })
  ```

手写promise

- 初始化 & 异步调用
- then catch 链式调用
- API .resolve .reject .all .race

```js
class MyPromise {
    state = 'pending' // pending fulfilled rejected
    value = undefined // 成功后的值
    reason = undefined // 失败后的值
    resolveCallbacks = [] // pending状态下存储成功的回调
    rejectCallbacks = [] // pending状态下，存储失败的回调
    constructor(fn){
        const resolveHandler = (value) => {
            if(this.state === 'pending'){
                this.state = 'fulfilled'
                this.value = value
                this.resolveCallbacks.forEach(fn => fn(this.value))
            }
        }
        const rejectHandler = (reason) => {
            if(this.state === 'pending'){
                this.state = 'rejected'
                this.reason = reason
                this.rejectCallbacks.forEach(fn => fn(this.reason))
            }
        }
        try {
            fn(resolveHandler,rejectHandler)
        }catch(err) {
            rejectHandler(err)
        }
        
    }
    then(fn1,fn2){
        // 判断传入的两个参数是否为函数类型
        fn1 = typeof fn1 === 'function' ? fn1 : (v) => v
        fn2 = typeof fn2 === 'function' ? fn2 : (e) => e
        if(this.state === 'pending'){
            const p1 = new MyPromise((resolve,reject) => {
                this.resolveCallbacks.push(() => {
                    try{
                        const newValue = fn1(this.value)
                        resolve(newValue) // p1.value
                    }catch(err){
                        reject(err)
                    }
                })
                this.rejectCallbacks.push(() => {
                    tyr{
                        const newReason = fn2(this.reason)
                        reject(newReason)
                    }catch(err){
                        reject(err)
                    }
                })
            })
            return p1
        }
        if(this.state === 'fulfilled'){
            const p1 = new MyPromise((resolve,reject) => {
                try{
                    const newValue = fn1(this.value)
                    resolve(newValue)
                }catch(e){
                    reject(err)
                }
            })
            return p1
        }
        if(this.state === 'rejected'){
            const p1 = new MyPromise((resolve,reject) => {
                try{
                    const newReason = fn2(this.reason)
                    reject(newReason) 
                }catch(err){
                    reject(err)
                }
            })
            return p1
        }
    }
    catch(fn){
        return this.then(null,fn)
    }
}

MyPromise.resolve = function(value){
    return new MyPromise((resolve,reject) => resolve(value))
}
MyPromise.reject = function(reason){
    return new MyPromise((resolve,reject) => reject(reason))
}
MyPromise.all = function(promiseList=[]){
    const p1 = new MyPromise((resolve,reject) => {
        const result = []
        const length = promiseList.length
        let resolvedCount = 0
        promiseList.forEach(p => {
            p.then(data => {
                result.push(data)
                // resolvedCount必须在then里面做++ 不能用index
                resolvedCount++
                if(resolvedCount === length){
                    resolve(result)
                }
            }).catch(err => {
                reject(err)
            })
        })
    })
    return p1
}
MyPromise.race = function(promiseList=[]){
    let resolved = false
    const p1 = new MyPromise((resolve,reject) => {
        promiseList.forEach(p => {
            p.then(data => {
                if(!resolved){
                    resolve(data)
                    resolved = true
                }
            }).catch(err => {
                reject(err)
            })
        })
    })
    return p1
}
```



## async/await

- 执行async函数，返回的是Promise对象
- await相当于Promise的then
- Promise的catch可以用try catch替代

异步的本质

- await后面的代码，都可以看成是异步

```js
// 第一题
async function fn(){
    return 100
}
(async function(){
    const a = fn() // Promise resolved
    const b = await fn() // 100
})()

// 第二题
(async function(){
    console.log('start') // start
    const a = await 100
    console.log('a',a) // a 100
    const b = await Promise.resolve(200)
    console.log('b',b) // b 200
    const c = await Promise.reject(300) // 报错，导致后面代码无法正常执行，需要使用try catch
    console.log('c',c)
    console.log('end')
})()

// 第三题
async function async1(){
    console.log('async start') // 2 async start
    await async2() 
    console.log('async1 end') // async后面的作为微任务 6 async1 end
}

async function async2(){
    console.log('async2') // 3 async2
}

console.log('script start') // 1 script start

setTimeout(function(){ // 宏任务
    console.log('setTimeout') // 9 setTimeout
},0)

async1()

new Promise(function(resolve){
    console.log('promise1') // 4 promise1
    resolve() 
}).then(function(){ // resolve后，微任务 
    console.log('promise2') // 8 promise2
})

console.log('script end') // 5 script end
```



## 微任务、宏任务

宏任务：setTimeout，setInterval，Ajax，DOM事件

微任务：Promise async/await

微任务执行时机要比宏任务要早

event loop和DOM渲染

-  每次call Stack清空（即每次轮询结束），即同步任务执行完
- 都是DOM重新渲染的机会，DOM结构如有改变则重新渲染
- 然后触发下一次event loop

宏任务与微任务的区别

- 宏任务：DOM渲染后触发，如setTimeout，宏任务都是由浏览器规定的
- 微任务：DOM渲染前触发，如Promise，微任务是ES6语法规定的

从eventloop角度解释，为什么微任务的执行时机要早于宏任务