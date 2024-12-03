### vite

- Vite，一个基于浏览器原生 ES 模块的开发服务器。利用浏览器去解析模块，在服务器端按需编译返回，完全跳过了打包这个概念，服务器随起随用。同时另有有 Vue 文件支持，还搞定了热更新，而且热更新的速度不会随着模块增加而变慢。
- Vite 要求项目完全由 ES 模块模块组成，common.js 模块不能直接在 Vite 上使用。因此不能直接在生产环境中使用。在打包上依旧还是使用 rollup 等传统打包工具。
- Vite 的基本实现原理，就是启动一个 koa 服务器拦截浏览器请求 ES 模块的请求。通过路径查找目录下对应文件的文件做一定的处理最终以 ES 模块格式返回给客户端 -
- 使用 esbuild 打包工具（go 语言，go 这样的静态语言会比动态语言快很多）

### 步骤

- 首先启动一个 koa 服务器，对首页(index.html)、js 文件、裸模块比如"vue"、vue 文件等进行分别处理
- 先返回 index.html,然后再 index.html 中去加载 main.js,在 main.js 中再去加载其它文件
- 加载 main.js 中的裸模块，比如"vue",vite 会通过预打包，将 vue 模块的内容打包到 node_modules 中，然后替换路径，

```js
import { createApp } from "vue";
import { createApp } from "/@modules/vue";
```

- 通过 /@modules 标识去 node_module 中查找并返回相对地址
- 加载 vue 文件，当 Vite 遇到一个.vue 后缀的文件时。由于.vue 模板文件的特殊性，它被分割成 template，css，脚本模块三个模块进行分别处理。最后放入 script，template，css 发送多个请求获取。
- vite 遇到.vue 后缀时，会使用 vue 中的 compiler 方法进行解析并返回

- 文件的执行顺序：localhost ==》 client(websocket) ==> main.js ==> env.js ==> vue.js(裸模块 vue) ==> app.vue ==> 最后就是执行里面的路由，组件，ui 库等

### 热更新原理

- Vite 的热加载原理，实际上就是在客户端与服务端建立了一个 websocket 链接，当代码被修改时，服务端发送消息通知客户端去请求修改模块的代码，完成热更新。查看 network,在 localhost 后会执行 client 文件，就是在这里建立 webcocket 实现热更新，然后再进入 main.js
- 服务端做的就是监听代码文件的更改，在适当的时机向客户端发送 websocket 信息通知客户端去请求新的模块代码。
- 客户端 Vite 的 websocket 相关代码在处理 html 中时被编写代码中
