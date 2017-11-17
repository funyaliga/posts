# vue + koa  + axios 的坑记录 #



### 跨域访问 ###

* koa：
  `npm install @koa/cors`
  在app.js `app.use(cors())`

  默认获取的response headers 只有 `content-type:`

  如果需要拿到其他header信息，需要添加`Access-Control-Expose-Headers`

  在@koa/cors 设置：

  ```javascript
  app.use(cors({
      exposeHeaders: 'x-auth-token'
  }))
  ```

  ​

* ​



