# React Hooks

函数组件的特点

- 没有组件实例
- 没有生命周期
- 没有state和setState，只能接收props

class组件的问题

- 大型组件很难拆分，很难测试（即class不易拆分）
- 相同业务逻辑，分散到各个方法中，逻辑混乱
- 复用逻辑变的复杂，如Minxins、HOC、RenderProp

State Hook

- useState

Effect Hook

- 模拟componentDidMount useEffect 依赖[]
- 模拟componentDidUpdate useEffect 无依赖，或者依赖[a,b]
- 模拟componentWillunMount useEffect中返回一个函数

其他Hook

- useRef 获取DOM节点

  ```jsx
  import React,{useRef} from 'react'
  
  function App(){
      const btn = useRef(null)
      useEffect(() => {
          console.log(btn.current)
      })
      return <div>
          <button ref={btn}>button</button>
      </div>
  }
  
  export default App
  ```

- useContext

  ```jsx
  import React,{useRef} from 'react'
  
  const themes = {
      light:{
          foreground:'#000',
          background:'#eee'
      },
      dark:{
          foreground:'#fff',
          background:'#222'
      },
  }
  
  // 创建context
  const ThemeContext = React.createContext(themes.light)
  
  function ThemeButton(){
      const theme = useContext(ThemeContext)
      return <button style={{background:theme.background,color:theme.foreground}}> 
          hello world
      </button>
  }
  
  function Toolbar(){
      return <ThemeButton />
  }
  
  function App(){
      
      return <ThemeContext.provider value={themes.dark}>
          <Toolbar/>
      </div>
  }
  
  export default App
  ```

- useReducer

  - useReducer是useState的代替方案，用于state复杂变化
  - useReducer是单个组件状态管理，组件通讯还需要props
  - redux是全局的状态管理，多组件共享数据

  ```jsx
  import React ,{useReducer} from 'react'
  
  const initialState = {count:0}
  
  const reducer = (state,action) => {
      switch(action.type){
          case 'increment':
              return {count:state.count + 1}
          case 'decrement':
              return {count:state.count - 1}
          default:
              return state
      }
  }
  
  function App(){
      const [state,dispatch] = useReducer(reducer,initialState)
      return <div>
          count{ state.count }
          <button onClick={() => dispatch({type:'increment'})}>increase</button>
          <button onClick={() => dispatch({type:'decrement'})}>decrease</button>
          </div>
  }
  ```

- useMemo

  - React默认会更新所有子组件
  - class组件 使用SCU和PureComponent
  - Hooks中使用useMemo，但优化原理是相同的

  ```jsx
  import React,{useState,memo,useMemo} from 'react'
  
  // 子组件
  const Child = memo(({userInfo}) => Child(){
      return <div>
          this is Child {userInfo.name} {userInfo.age}
      </div>
  })  
  
  // 父组件
  function App(){
      const [count,setCount] = useState(0)
      const [name,setName] = useState('sola')
      
      const userInfo = useMemo(() => {
          return {name,age:20}
      },[name])
      return <div>
          <p>
              useMemo demo
              <button onClick={() => setCount(count + 1)}>click</button>
          </p>
          <Child userInfo={userInfo} />
      </div>
  }
  
  export default App
  ```

- useCallback

  - useMemo缓存数据
  - useCallback 缓存函数

  ```jsx
  import React,{useState,memo,useMemo,useCallback} from 'react'
  
  // 子组件
  const Child = memo(({userInfo,onChange}) => Child(){
      return <div>
          this is Child {userInfo.name} {userInfo.age}
          <input onChange={onChange} />
      </div>
  })  
  
  // 父组件
  function App(){
      const [count,setCount] = useState(0)
      const [name,setName] = useState('sola')
      
      const userInfo = useMemo(() => {
          return {name,age:20}
      },[name])
      const onChange = useCallback(e => {
           console.log(e.target.value)
      },[])
      return <div>
          <p>
              useMemo demo
              <button onClick={() => setCount(count + 1)}>click</button>
          </p>
          <Child userInfo={userInfo} onChange={onChange} />
      </div>
  }
  
  export default App
  ```

自定义Hook

```jsx
import {useState,useEffect} from 'react'
import axios from 'axios'

function useAxios(url) {
    const [loading,setLoading] = useState(false)
    const [data,setData] = useState()
    const [error,setError] = useState()
    
    useEffect(() => {
        // 利用axios发送网络请求
        setLoading(true)
        axios.get(url).then(res => setData(res)).catch(err => setError(err)).finally(() => setLoading(false))
    },[url])
    return [loading,data,error]
}

export default useAxios
```

Hooks使用规范

- 只能用于React函数组件和自定义Hook中，其他地方不可以
- 只能用于顶层代码，不能在循环、判断中使用Hooks
- exlint插件 eslint-plugin-react-hooks

组件逻辑复用

- class组件

  - Mixins早已废弃
  - HOC
  - Render Props

- 函数组件

  ```jsx
  import {useState,useEffect} from 'react'
  
  function useMousePosition(){
      const [x,setX] = useState(0)
      const [y,setY] = useState(0)
      useEffect(() => {
          function mousemoveHandler(event){
              setX(event.clientX)
              setY(event.clientY)
          }
          // 绑定事件
          document.body.addEventListener('mousemove',mousemoveHandler)
          return () => {
             // 解绑事件
             document.body.aremoveEventListener('mousemove',mousemoveHandler) 
          } 
      },[])
      return [x,y]
  }
  
  export default useMousePosition
  ```

  

规范和注意事项

- useState初始化值，只有第一次有效
- useEffect内部不能修改state，前提是useEffect第二个参数为[]
  - 依赖为[] re-render不会重新执行effect
  - 没有依赖：re-render会重新执行effect
- useEffect可能出现死循环
  - useEffect第二个参数如果出现引用类型的时候，会造成死循环

React Hooks 面试题

1.为什么会有React Hooks，它解决了哪些问题

- 完善函数组件能力，函数组件更适合React组件
- 组件逻辑复用，Hooks表现更好
- class复杂组件正在变的费解，不易拆解，不易测试，逻辑混乱

2.React Hooks 如何模拟组件生命周期

- 模拟componentDidMount useEffect 依赖[]
- 模拟componentDidUpdate useEffect 无依赖，或者依赖[a,b]
- 模拟componentWillunMount useEffect中返回一个函数

3.如何自定义Hook

4.React Hooks性能优化

- useMemo缓存数据
- useCallback缓存函数

5.使用React Hooks遇到哪些坑

- useState初始化值，只修改一次
- useEffect内部，不能修改state
- useEffect依赖引用类型，会出现死循环

6.Hooks相比HOC和ReaderProp有哪些优点

- 完全符合Hooks原有规则，没有其他要求，易理解记忆
- 变量作用域明确
- 不会产生组件嵌套