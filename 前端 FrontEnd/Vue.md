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

---

*2020.01.10*

### 获取窗口的尺寸从而做到组件样式响应式变化

当Vue页面需要显示在不同尺寸的移动端屏幕上时，一些固定尺寸的组件就会有不一致的表现效果。在使用element样式库时，有些组件还是贴心的给出了`:width`的参数，可以使用`:width:200`直接修改宽度；但有些组件就没有提供这种方法，比如文件拖动上传组件的尺寸是写死的360×180，并且无法通过改变父元素`upload-demo`的样式来修改内部样式：

<img src="Vue.assets/image-20210110141023836.png" alt="image-20210110141023836" style="zoom:80%;" />

而现在的一个需求就是想让这个组件的宽度**永远**填满页面。

#### 修改组件内部class的样式

> [修改element的内部样式的两种方式](https://www.cnblogs.com/sunshenggang/p/13343621.html)

- 首先在组件上添加自己的一个样式类

  ```vue
  <template>
  	<el-upload
          class="upload-demo my-upload-dragger"
          drag
     >
  </template>
  ```

- 然后用**组件穿透式**的方式去修改内部的class样式

  ```vue
  <style scoped>
  .my-upload-dragger >>> .el-upload-dragger{
    width:400px !important;
  }
  </style>
  ```

- 观察到宽度已经发生改变

  <img src="Vue.assets/image-20210110141611480.png" alt="image-20210110141611480" style="zoom:80%;" />

#### 动态获取页面窗口宽度

> [基于element的网站自适应方案（移动端适配）](https://blog.csdn.net/qq_34295211/article/details/105513620)

```vue
<script>
	export default{
        methods:{
            getWidth(){
                return window.innerWidth; // 例：540
            }
        }
    }
</script>
```

#### 动态改变尺寸

> [vue动态样式绑定改变伪元素等特殊样式(css var函数)](https://blog.csdn.net/qq_43363884/article/details/104059654)
>
> [vue中动态style(如何动态修改伪元素样式)](https://blog.csdn.net/qq_44827891/article/details/106413564)

- 在css文件中可以使用变量

  - 使用`--`两根短划线开头来命名，大小写敏感：`--myVar: 100px`
  - 获取值时使用`var()`：`width: var(--myVar)`

- 动态在父元素（组件本身）的style中使用**方法**动态赋值**css变量**：

  ```vue
  <template>
  	<el-upload
          class="upload-demo my-upload-dragger"
          drag
          :style="{'--autoWidth':getWidth()}"
     >
  </template>
  <script>
  	export default{
          methods:{
              getWidth(){
                  var myWidth=window.innerWidth-20*2; //减去外部大面板的padding
                  return myWidth+'px'; // 例：500px
              }
          }
      }
  </script>
  ```

- 最后在样式中结合上面改变内部标签样式的方式使用变量：

  ```vue
  <style scoped>
  .my-upload-dragger >>> .el-upload-dragger{
    width:var(--autoWidth) !important;
  }
  </style>
  ```

- 得到的最终效果：

  <img src="Vue.assets/image-20210110145201610.png" alt="image-20210110145201610" style="zoom:80%;" />

