## 微前端实施方式

### nginx 反向代理实现分发式微前端

通过路由将不同的业务分发到不同的、独立的前端应用上。其通常可以通过 HTTP 服务器的反向代理来实现。但是切换应用时会导致浏览器刷新影响用户体验

### iframe ，通过 iframe 加载子应用

通信可以通过 postMessage 进行通信

优点：

1. 简单
2. 隔离
3. 安全

我的项目是怎么通过 iframe 实现子应用接入的。先抽离出一个单独的 vue 文件。打包上传到后台系统。然后在主应用中通过接口请求到这个子应用并加载到页面上。

缺点：

1. 性能开销大
2. 破坏了语义化
3. url 不同步。浏览器刷新iframe url 状态丢失、后退前进按钮无法使用
4. 全局上下文完全隔离，内存变量不共享
5. 慢，每次子应用进入都是一次浏览器的上下文重建、资源重新加载的过程

### web component

将前端应用程序分解为自定义 HTML 元素。基于 CustomEvent 实现通信 Shadow Dom 天生的作用域隔离

## 个人日历模块的开发

## webworker 实现大文件上传的流程

首先定义一个 分割文件 的函数

```js
// cutFile.js
// 定义文件切片的大小
const CHUNK_SIZE = 1024 * 1024 * 5
const THREAD_COUNT = 4

export async function cutFile(file) {
    return new Promise((resolve) => {
        const result = []
        // 计算分片的数量
        const chunkCount = Math.ceil(file.size / CHUNK_SIZE)
        // 计算每个线程分到多少个分片
        const workerChunkCount = Math.ceil(chunkCount / THREAD_COUNT)
        let finishCount = 0
        for(let i = 0;i < THREAD_COUNT;i++){
            // 创建一个新的 Worker 线程
            const worker = new Worker('./worker.js',{
                type:'module'
            })
            // 计算每个线程的开始索引和结束索引
            const startIndex = i * workerChunkCount
            const endIndex = startIndex + workerChunkCount
            worker.postMessage({
                file,
                CHUNK_SIZE,
                startIndex,
                endIndex
            })
            worker.onmessage = (e) => {
                for(let i = startIndex;i < endIndex;i++){
                    result[i] = e.data[i - startIndex]
                }
                worker.terminate()
                finishCount++
                if(finishCount === THREAD_COUNT){
                    resolve(result)
                }
            }
        }
    })
}
```

```js
// worker.js
import { createChunk }from "./createChunk.js"

self.onmessage = async (e) => {
    const proms = []
    const { file,CHUNK_SIZE,startIndex,endIndex } = e.data
    console.log(file,CHUNK_SIZE,startIndex,endIndex)
    for(let i = startIndex;i<endIndex;i++){
        proms.push(createChunk(file,i,CHUNK_SIZE))
    }
    const chunks = await Promise.all(proms)
    self.postMessage(chunks)
}
```

面试问 webworker 开启多线程实现大文件分片上传是如何实现的？

首先就是要定义一个分割函数，可以分割文件中特定序列长度的二进制数据并进行 md5 加密发送。由于加密发送比较耗费时间，再加上文件可能会很大，因此把该高耗能的操作放到 webworker 里面执行。定义线程的数量，将 文件、文件分片大小、分片开始序列、分片结束序列 传入每个线程当中，在线程当中进行文件分割上传，并使用 Promise.all ，当该线程所有分片分割上传完毕后，返回结果给主脚本。主线程收到返回的结果后将结果存在 result 数组中，并将线程关闭，单线程关闭的数量等于定义的数量时，大文件上传操作结束，结果返回

## 路由权限是怎么做的

### RBAC（Role-Based Access Control）

通俗来讲，当不同用户登录系统时，即使是同一个系统，由于身份的不同，他们所能看到的页面和所能进行的操作都是不尽相同的，而这些区别则是所谓的权限。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d49cf621286741c5b8a9ead0092bef1f~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=2716&h=902&s=91809&e=png&b=f3f3f3)

在用户和权限中间添加了一层角色，大幅度提高了权限管理的安全性和效率性

### 实现过程

**后端接口返回所有字段，需要前端自己筛选和组合数据**

前端先筛选出用户的所有角色 id 去找到对应的菜单 id，接口将该角色下拥有的所有菜单返回给前端

#### 路由数据存放在哪

项目中请求放在了 Vuex 中的action 中，因为最后拿到的数据是要保存到 Vuex 中的，**以便左侧菜单组件能够拿到路由信息渲染出对应的菜单**，这样做就方便一点，至于时机，实在 router 的 beforeEach 里等到用户登录之后发起请求的。

```js
file: @/api/user.js
// 获取动态路由表
export function getAsyncRoutes() {
  return request({
    url: '/admin/menus',
    method: 'get'
  })
}

file: @/store/modules/permission.js
import { getAsyncRoutes } from '@/api/user'
import formatRoutes from '@/utils/formatRoutes'
import Layout from '@/layout'
// 有些依赖就没有完全列出来
// actions
const actions = {
  generateRoutes({ commit }) {
    return new Promise(resolve => {
      // 从服务器获取路由表
      getAsyncRoutes().then(routes => {
        // 格式化路由表
        const accessedRoutes = formatRoutes(routes, Layout)  // formatRoutes函数会在后面放出来
        // 将路由保存到Vuex中
        commit('SET_ROUTES', accessedRoutes)
        resolve(accessedRoutes)
      })
    })
  }
}

export default {
  namespaced: true,
  state,
  mutations,
  actions
}

```

接下来需要在 vue-router 的路由钩子里面，dispatch 定义好的 action 就可以拿到格式化好的数据。在获取路由表前，需要先向后端请求用户角色信息

1. 超级管理员
2. 班级管理员
3. 树木管理员

```js
router.beforeEach(async(to, from, next) => {
  // 开启页面进度条
  NProgress.start()

  // 设置标题
  document.title = getPageTitle(to.meta.title)

  const hasToken = getToken()
  if (hasToken) {
    if (to.path === '/login') {
      // 已登录，跳转到: '/'
      next({ path: '/' })
      NProgress.done() // 关闭页面进度条
    } else {
      // 是否通过用户信息拿到角色信息
      const hasRoles = store.getters.roles && store.getters.roles.length > 0
      if (hasRoles) {
        // 登录过并且有角色信息，直接进入下一个路由
        next()
      } else {
        try {
          // 获取用户信息
          await store.dispatch('user/getInfo')

          // 重点在这。。。。
          // 根据角色生成路由表
          const accessRoutes = await store.dispatch('permission/generateRoutes')
          // 动态添加路由
          router.addRoutes(accessRoutes)

          next({ ...to, replace: true })
        } catch (error) {
          console.log(error)
          // 清除token，跳转登录页
          await store.dispatch('user/resetToken')
          Message.error(error || 'Has Error')
          next(`/login?redirect=${to.path}`)
          NProgress.done()
        }
      }
    }
  } else {

    if (whiteList.indexOf(to.path) !== -1) {
      // 访问的路径处于白名单中
      next()
    } else {
      // 没有登录，跳转登录页
      next(`/login?redirect=${to.path}`)
      NProgress.done()
    }
  }
})
```

格式化路由

后端返回的不一定是标准的路由信息，需要做格式化

```js
function loadView(component) {
  return (resolve) => require([`@/views/${component}`], resolve)
}

export default function formatRoutes(routes, Layout) {
  const formatRoutesArr = []
  routes.forEach(route => {
    const router = {
      meta: {}
    }
    const {
      pid,
      title,
      path,
      redirect,
      component,
      keep_alive,
      icon,
      name,
      children
    } = route
    if (component === 'Layout') {
      router['component'] = Layout
    } else {
      router['component'] = loadView(component)
    }
    if (redirect !== null) {
      router['redirect'] = redirect
    }
    if (icon !== null) {
      router['meta']['icon'] = icon
    }
    if (children && children instanceof Array && children.length > 0) {
      router['children'] = formatRoutes(children)
    }
    if (name !== null) {
      router['name'] = name
    }
    router['meta']['title'] = title
    router['path'] = path
    if (pid === 0) {
      router['alwaysShow'] = true
    }
    router['meta']['noCache'] = !keep_alive
    formatRoutesArr.push(router)
  })
  // 将404页面添加到最后面
  formatRoutesArr.push({ path: '*', redirect: '/404', hidden: true })
  return formatRoutesArr
}
```

