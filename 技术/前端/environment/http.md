# http

面试题目

- http常见状态码
  - 1xx 服务器收到请求
  - 2xx 请求成功 如 200 成功
  - 3xx 重定向
    - 301（永久重定向配合location，浏览器自动处理） 
    - 302（临时重定向配合location，浏览器自动处理）
    - 304 资源未被修改
  - 4xx 客户端错误 
    - 404 资源未找到
    - 403 没有权限
  - 5xx 服务端错误 
    - 500 服务器错误
    - 504 网关超时

- http methods
  - 传统methods
    - get获取服务器的数据
    - post向服务器提交数据
  - 现在的methods
    - get 获取数据
    - post 新建数据
    - patch/put 更新数据
    - delete 删除数据

- http常见的header

  - Request Headers
    - Accept 浏览器可接受的数据格式
    - Accept-Encoding 浏览器可接受 的压缩算法 如 gzip
    - Accept Language 浏览器可接受的语言
    - Connection：keep-alive 一次TCP连接重复使用
    - cookie
    - Host
    - User-Agent 浏览器信息
    - Content-type 发送数据的格式 如application/json
  - Response Headers
    - Content-type 返回的数据格式 如application/json
    - Content-length 返回的数据大小，多少字节
    - Content-Encoding 返回数据的压缩算法，如gzip
    - Set-Cookie
  - 缓存相关的headers
    - Cache-Control EXpires
    - Last-Modified If-Modified-Since
    - Etag If-None-Match

- 什么是restful API

  - 传统API设计，把每个url当作一个功能
  - Restful API设计：把每个url当做一个唯一的资源
    - 传统API设计
      - /api/list?pageIndex=2
      - post请求 /api/create-blog
      - post请求 /api/update-blog
      - get请求 /api/get-blog
    - Restful API设计：/api/list/2
      - post请求 /api/blog
      - patch请求 /api/blog/100
      - get请求 /api/blog

- 描述一下http的缓存机制

  - 什么是缓存？

  - 强制缓存 Cache-Control
    - Response Headers中出现，由服务端进行控制，例如，Cache-Control: max-age=31536000(单位是秒)
    - cache-control
      - max-age 最大缓存事件
      - no-cache 不用本地强制缓存
      - no-store 不强制缓存，也不需要服务端来处理
      - private 只允许最终用户做缓存
      - public 允许中间路由或者代理做缓存
    - 关于Expires，同在Response Headers中，同为控制缓存过期，已被Cache-Control代替了
  - 协商缓存（对比缓存）服务端缓存策略，服务端判断客户端资源，是否和服务端资源一样，一致则返回304，否则返回200和最新的资源
    - 资源标识：在Response Headers中有两种
      - Last-Modified 资源的最后修改时间
      - Etag 资源的唯一标识（一个字符串，类似人的指纹）
      - Last-Modified和Etag会优先使用Etag，Last-Modified只能精确到秒级，如果资源被重复生成，而内容不变，则Etag更精确
  - 刷新操作方式对缓存的影响
    - 三种刷新操作
      - 正常操作：地址栏输入url，跳转链接，前进后退等
      - 手动刷新：F5，点击刷新按钮，右击菜单刷新
      - 强制刷新：ctrl + F5
    - 三种刷新操作对缓存的影响
      - 正常操作：强制缓存有效，协商缓存有效
      - 手动刷新：强制缓存失效，协商缓存有效
      - 强制刷新：强制缓存失效，协商缓存失效

https和http

- http是明文传输，敏感信息容易被中间劫持
- https = http + 加密，劫持了也无法解密

加密方式

- 对称加密，同一个key进行加密和解密
- 非对称加密：一对key，A加密后，只能用B来解密
- https同时使用了这两种方式进行了加密

https证书
