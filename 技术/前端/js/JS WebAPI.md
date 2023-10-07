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

    

## BOM

## 事件绑定

## ajax

## 存储