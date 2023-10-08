# JS WebAPI

JS基础知识，规定语法（ECMA262标准）

JS Web API，网页操作的API（W3C标准）

前者是后者的基础，两者相结合才能真正实际应用

## DOM（Document Object Model）

### 题目

- DOM是那种数据结构
  - 树结构
- DOM操作的常用API
  - 节点操作
  - 结构操作
- attr和property的区别
  - Property：修改对象属性，不会体现到html结构中
  - attribute：修改html属性，会改变html结构
  - 两者都有可能引起DOM重新渲染
- 一次性插入多个DOM节点如何操作
  - 先创建一个文档片段，将多个节点放入到文档片段中后，最后进行统一的一次插入操作

### 知识点

- DOM本质

- DOM节点操作

  ```js
  document.getElementById('div1')
  document.getElementsByTagName('div') // 集合
  document.getElementByClassName('container') // 集合
  document.querySelectorAll('p') // 集合
  
  // DOM节点的property
  const pList = document.querySelectorAll('p')
  const p1 = pList[0]
  p1.style.width = '100px'
  console.log(p1.style.width) // 100px
  console.log(p1.className) // container
  console.log(p1.nodeName) // p
  console.log(p1.nodeType) // 1
  
  // DOM节点的attribute
  p1.setAttribute('data-name','sola')
  console.log(p1.getAttribute('data-name'))
  p1.setAttribute('style','font-size:50px;')
  console.log(p1.getAttribute('style'))
  ```

- DOM结构操作

  - 新增/插入节点

    ```js
    const div1 = document.getElementById('div1')
    const div2 = document.getElementById('div2')
    
    // 新建节点
    const p1 = document.createElement('p')
    p1.innerHTML = 'hello sola'
    // 插入节点
    div1.appendChild(p1)
    
    // 移动节点
    const p2 = document.getElementById('p2')
    div2.appendChild(p2)
    
    // 获取父元素列表
    console.log(p1.parentNode)
    
    // 获取子元素列表 nodeType为1是正常的P标签节点，nodeType是3为text文本
    console.log(div1.childNodes)
    const div1ChildNodesP = Array.prototype.slice.call(div1.childNodes).filter(child => {
      if(child.nodeType === 1){
        return true
      }
      return false
    })
    concole.log('div1ChildNodesP',div1ChildNodesP)
    
    // 删除子元素
    div1.removeChild(div1ChildNodesP[0])
    ```

- DOM性能

  - 对DOM查询做缓存

    ```js
    // 不缓存DOM查询结果
    for(let 1 = 0;i<docuemnt.getElmentsByTagName('p').length;i++){
      // 每次循环，都会计算length，频繁进行DOM查询
    }
    
    // 缓存DOM查询结果
    const pList = docuemnt.getElmentsByTagName('p')
    const length = pList.length
    for(let i = 0;i<length;i++){
      // 缓存length，只进行一次DOM查询
    }
    ```

  - 讲频繁操作，改为一次性操作

    ```js
    // 错误操作
    const list = document.getElementById('list')
    for(let i = 0;i<10;i++){
      const li = docment.createElement('li')
      li.innerHTML = `li ${i}`
      list.appendChild(li)
    }
    
    // 正确操作 创建一个文档片段，此时还没有插入到DOM结构中
    const frag = document.createDocumentFragment()
    for(let i = 0;i<10;i++){
      const li = docment.createElement('li')
      li.innerHTML = `li ${i}`
      frag.appendChild(li)
    }
    
    // 都完成之后，再插入到DOM树中
    listNode.appendChild(frag)
    ```

    

## BOM（Browser Object Model）

面试题

- 如何识别浏览器的类型
- 分析拆解url各部分

知识点

- navigator

  ```js
  const ua = navigator.userAgent
  const isChrome = ua.indexOf('Chrome')
  console.log(isChrome)
  ```

- screen

  ```js
  // 浏览器宽高
  screen.width
  screen.height
  ```

- location

  ```js
  console.log(location.href) // 当前url地址
  console.log(location.protocol) // https
  console.log(location.pathname) // 浏览器路径 /class/chapter
  console.log(location.search) // 取 url中的search参数 ?name=sola
  console.log(location.hash) // 取#后面的哈希内容
  ```

- history

  ```js
  history.back() // 后退
  history.forward() // 前进
  ```

  

## 事件绑定

面试题目

- 编写一个通用的事件监听函数

- 描述事件冒泡的流程

  - 基于DOM树形结构
  - 事件会顺着触发元素向上冒泡
  - 应用场景：代理

- 无线下拉的图片列表，如何监听每个图片的点击

  - 事件代理
  - 用e.target获取触发元素
  - 用matches来判断是否是触发元素

  ```js
  // 通用事件绑定方法
  function bindEvnet(elem,type,selector,fn){
      if( fn == null){
          fn = selector
          selector = null
      }
      elem.addEventListener(type,event => {
          const target = event.target
          if(selector){
              // 代理
              if(target.matches(selector)){
                 fn.call(target,event) 
              }
          }else{
              // 普通绑定
              fn.call(target,event) 
          }
      })
  }
  
  // 普通绑定
  const btn1 = docuemnt.getElementById('btn1')
  bindEvent(btn1,'click',function(event) {
      // event.target // 当前绑定元素
      // event.preventDefault() // 阻止默认行为
      // event.stopPropagation() // 阻止冒泡
      alert(this.innerHTML)
  })
  
  // 代理绑定
  const fatherDiv = document.getElementById('father')
  // 给父级div做事件绑定，通过事件代理，使得父元素中的所有子元素在触发点击的时候向上冒泡，从而达到所有子元素的绑定点击事件
  bindEvent(fatherFiv,'click','a',event => {
      // 阻止a标签的默认行为
      event.preventDefault()
      alert(this.innerHTML)
  })
  ```

知识点

- 事件绑定
- 事件冒泡
- 事件代理
  - 代码简洁
  - 减少浏览器内存使用
  - 但是，不要滥用

## ajax

面试题目

- 手写一个简易的ajax

  ```js
  function ajax(url){
      const p = new Promise((resolve,reject) => {
          const xhr = new XMLHttpRequest()
          xhr.open('GET',url,true)
          xhr.onreadystatechange = function(){
          if(xhr.readyState === 4){
              if(xhr.status === 200){
                  resolve(JSON.parse(xhr.responseText))
                  }
              }else if(xhr.status === 404) {
                  reject(new Error('404 not found'))
              }
          }
          xhr.send(null)
      })
      return p
  }
  const url = '/data/test.json'
  ajax(url).then(res => console.log(res)).catch(err => {
      console.error(err)
  })
  ```

  

- 跨域的常用实现方式

  - JSONP
  - CORS

知识点

- XMLHttpRequest

  ```js
  // GET 请求
  const xhr = new XMLHttpRequest()
  xhr.open('get','/data/test.json',true) // 异步请求
  xhr.onreadystatechange = function(){
      if(xhr.readyState === 4){
          if(xhr.status === 200){
              alert(xhr.responseText)
          }
      }
  }
  xhr.send(null)
  
  // post请求
  const xhr = new XMLHttpRequest()
  xhr.open('post','/login',true) // 异步请求
  xhr.onreadystatechange = function(){
      if(xhr.readyState === 4){
          if(xhr.status === 200){
              alert(xhr.responseText)
          }
      }
  }
  const postData = {
      username:'sola',
      password:'123'
  }
  xhr.send(JSON.stringfy(postData))
  ```

  - xhr.readyState
    - 0 - UNSET 尚未调用open方法
    - 1 - OPENED open方法已被调用
    - 2 - HEADRES_RECEIVED send 方法已经被调用，header已被接收
    - 3 - LOADING 下载中，responseText已经有部分内容
    - 4 - DONE 下载完成
  - xhr.status
    - 2xx 表示成功处理请求 如200
    - 3xx 需要重定向，浏览器直接跳转 如301（永久重定向），302（临时重定向），304（资源未改变）
    - 4xx 客户端请求错误 如 404 403
    - 5xx 服务端错误

- 状态码

  - 2xx 表示成功处理请求 如200
  - 3xx 需要重定向，浏览器直接跳转 如301（永久重定向），302（临时重定向），304（资源未改变）
  - 4xx 客户端请求错误 如 404 403
  - 5xx 服务端错误

- 跨域：同源策略，跨域解决方案

  - ajax请求时，浏览器要求当前网页和server必须同源

    - 同源：协议、域名、端口，三者必须一致
    - 加载图片 CSS JS可无视同源策略
      - `<img src=跨域图片地址 />` 可用于统计打点，可使用第三方打点服务
      - `<link href=跨域的CSS地址 />`可使用CDN，CDN一般是外域
      - `<script src=跨域的JS地址 />` 可实现JSONP

  - 所有的跨域，都必须经过server端允许和配合，未经server端允许就实现跨域，说明浏览器有漏洞，危险信号

  - jSONP

    ```js
    // jQuery 实现jsonp
    $.ajax({
        url:"http://locallhost:8882/x-origin.json",
        dataType:'jsonp',
        jsonpCallback:'callback',
        success:function(data){
            console.log(data)
        }
    })
    ```

  - CORS

    - 服务端设置http header

      ```js
      // 
      response.setHeader("Acess-Control-Allow-Origin","http://localhost:8011")
      response.setHeader("Acess-Control-Allow-Headers","X-Requestd-With")
      response.setHeader("Acess-Control-Allow-Methods","PUT,POST,GET,DELETE,OPTIONS")
      
      // 接收跨域的cookie
      response.setHeader("Acess-Control-Allow-Credentials","true")
      ```

      

      

## 存储

面试题目

- 描述 cookie local storage session storage区别

cookie

- 缺点
  - 存储大小，最大4kb
  - http请求时需要发送到服务端，增加请求数据量
  - 只能使用document.cookie = '...'来修改，太过简陋

localStorage和sessionStorage 最大可存储5M

API简单易用setItem getItem 