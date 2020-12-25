# Vue

*summary*

<img src="Vue.assets/image-20201225112909319.png" alt="image-20201225112909319" style="zoom:80%;" />

**Vue**是一个响应式前端框架。

---

*2020.12.25*

### Vue的开发环境解决跨域请求问题

> [vue 跨域代理devServer的proxy打包后不生效](https://blog.csdn.net/jesion_t/article/details/105360625)
>
> [vue中解决跨域问题](https://www.cnblogs.com/ldlx-mars/p/7816316.html)

开发环境的localhost发起请求很容易产生跨域问题，因此Vue-cli提供一种简便的url代理工具，proxy可以在devServer中配置，以便不影响到生产环境。

- 位于`config/index.js`中，配置proxy：

  ```js
  module.exports = {
    devServer: {
      proxy: {
        '/api': {
          target: 'http://www.baidu.com', // 源地址 或 代理地址，这里设置的地址会代替axios中设置的baseURL
          changeOrigin: true, // 如果接口跨域，需要进行这个参数配置
          ws: true, // proxy websockets
          // pathRewrite方法重写url
          pathRewrite: {
            '^/api': '/'
            // pathRewrite: {'^/api': '/'} 重写之后url为 http://192.168.1.16:8085/xxxx
            // pathRewrite: {'^/api': '/api'} 重写之后url为 http://192.168.1.16:8085/api/xxxx
          }
        }
      }
    }
  }
  ```
  - 原本开发环境的请求是这样的：`http://localhost:8000 -> http://www.baidu.com/getData `
  - 现在可以进行这样的请求：`http://localhost:8000 -> http://localhost:8000/api/getData`，经过代理后变为`http://www.baidu.com -> http://www.baidu.com/getData`。既通过了跨域校验，也顺利请求到了接口