# Vue3实现动态路由（权限菜单）

## 步骤一：分析数据

假设后端发来的数据是这样的

![image-20220506153724675](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Vue3%E5%AE%9E%E7%8E%B0%E5%8A%A8%E6%80%81%E8%B7%AF%E7%94%B1/image-20220506153724675.png)

type ===1 时是二级菜单（也就是可以展开的菜单，里面有各个子菜单）

type === 2时是一级菜单（不可展开）

![image-20220506153736724](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Vue3%E5%AE%9E%E7%8E%B0%E5%8A%A8%E6%80%81%E8%B7%AF%E7%94%B1/image-20220506153736724.png)

## 步骤二：创建组件和路由

先将需要的所有路由都创建出来（后续再根据什么用户权限对应注册哪些路由，菜单也是根据后端数据动态生成的）

只有一级菜单才创建，创建的路径跟传过来的url一致，因为url对应的其实就是path

![image-20220506153747335](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Vue3%E5%AE%9E%E7%8E%B0%E5%8A%A8%E6%80%81%E8%B7%AF%E7%94%B1/image-20220506153747335.png)

例如这里的url是/main/system/role  所以我就在views/main/system/role 下面创建role.vue

![image-20220506153759070](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Vue3%E5%AE%9E%E7%8E%B0%E5%8A%A8%E6%80%81%E8%B7%AF%E7%94%B1/image-20220506153759070.png)

同时在router/main/system/role下面创建role.ts

![image-20220506153809838](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Vue3%E5%AE%9E%E7%8E%B0%E5%8A%A8%E6%80%81%E8%B7%AF%E7%94%B1/image-20220506153809838.png)

其内容为路由的注册信息

![image-20220506153821382](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Vue3%E5%AE%9E%E7%8E%B0%E5%8A%A8%E6%80%81%E8%B7%AF%E7%94%B1/image-20220506153821382.png)



这里因为要创建太多的组件和路由，所以用一个工具来快速创建(coderwhy提供的)

```shell
npm i coderwhy -g 
```

安装成功后敲命令,它就会创建出相应路径的role.vue和role.ts

```shell
// coderwhy add3page(如果是vue2就用addpage) 组件名 -d 组件存放路径
// 例如
coderwhy add3page role -d src/views/main/system/role
coderwhy add3page user -d src/views/main/system/user
...
```

注意：自己一个个创建组件和路由都可以，路径也自己可以顶，弄这么长的路径其实只是为了利用这个工具而已

命令分析：它会在创建完组件后，会根据-d后面的路径替换views为router创建它的路由规则，而且路径都是一一对应的，相当方便

## 步骤三：根据菜单权限获取路由规则数组

创建完所有组件和路由规则后

在src/utils下创建map-menus.ts文件，该文件是根据用户菜单权限拿到对应的路由规则

```typescript
import { RouteRecordRaw } from 'vue-router'

export function mapMenusToRoutes(userMenus: any[]): RouteRecordRaw[] {
  const routes: RouteRecordRaw[] = []

  // 1.先去加载默认所有的routes
  const allRoutes: RouteRecordRaw[] = []
  // 这里的三个参数分别为①加载文件路径②是否递归加载，如果为false，只会加载路径下文件，如果true
  // 路径里面文件夹的文件也会加载③正则表达式（加载什么文件）
  const routeFiles = require.context('@/router/main', true, /\.ts/)
  routeFiles.keys().forEach((key) => {
    const route = require('@/router/main' + key.slice(1))
    allRoutes.push(route.default)
  })
  // 2.根据菜单获取需要添加的routes
  // userMenus:
  // type === 1 -> children -> type === 1
  // type === 2 -> route
  const _recurseGetRoute = (menus: any[]) => {
    for (const menu of menus) {
      if (menu.type === 2) {
        const route = allRoutes.find((route) => route.path === menu.url)
        if (route) routes.push(route)
      } else {
        _recurseGetRoute(menu.children)
      }
    }
  }

  _recurseGetRoute(userMenus)

  return routes
}

```

## 步骤四：注册路由

大概实现思路就是这样，不同的情况可以适当修改，思路是一致的，

最后使用的话只需要导入该文件（map-menus.ts），在获取到用户菜单权限后调用该文件导出的函数mapMenusToRoutes（传入的菜单数据）就可以得到相应的路由规则，然后遍历注册就可以了

我这里是在登录成功后获取数据并将数据存入vuex里面，所以我在存的时候顺便注册路由（vuex存的操作就不说了，直接调用就好了）

![image-20220506153842905](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Vue3%E5%AE%9E%E7%8E%B0%E5%8A%A8%E6%80%81%E8%B7%AF%E7%94%B1/image-20220506153842905.png)

当然，vuex的特点就是刷新页面丢失，因为我是在登录成功才会进行存vuex的操作，而注册动态路由也放在这个操作里面，所以刷新vuex没了，动态路由的注册也没了，所以我们在登录成功后保存用户菜单信息到vuex之前，先将它存到本地，这样的话在登录成功后，vuex和localStorage里面都有我们的菜单信息了，然后再定义一个函数（该函数作用是获取localStorage的菜单信息，然后将他存入vuex，也就是再次调用存vuex的操作，这样也就间接的又注册了动态路由），在每次页面刷新都调用该函数

```typescript
// 这里是vuex里面的actions函数，
actions: {
    // 从本地缓存取数据保存入vuex
    loadLocalLogin(context) {
      const token = localStorage.getItem('token')
      if (token) {
        context.commit('changeToken', token)
      }
      const userInfo = localStorage.getItem('userInfo')
      if (userInfo) {
        context.commit('changeUserInfo', userInfo)
      }
      const userMenus = localStorage.getItem('userMenus')
      if (userMenus) {
        context.commit('changeUserMenusInfo', userMenus)
      }
    },
}
```

该函数定义在哪里随意，最后在main.js里面调用就好了

```
// 在每次页面重新加载时(刷新或第一次打开时),将本地缓存中vuex需要的数据存入vuex
export function setupStore() {
  // 从本地缓存获取Login模块需要的vuex数据
  store.dispatch('login/loadLocalLogin')
}
```

这里注意该函数的调用一定要在app.use(router)之前，因为在app.use(router)后，页面路径就会去匹配路由，这时动态路由还没注册，这时页面路径是router里面的还好，可以正常显示，如果是动态路由的路径，那就gg，找不到页面...

可以放上面试一试，刷新一下页面，会发现页面空白~

```
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'
import { setupStore } from './store'

const app = createApp(App)
// 在每次页面重新加载时(刷新或第一次打开时),将本地缓存中vuex需要的数据存入vuex
// 该函数要放在app.use(router)之前,因为在函数里面注册了动态路由，如果放后面
// 路径是动态路由的路径的话，刷新会页面丢失，因为这时动态路由还没注册，匹配不到路径
setupStore()
app.use(router)
app.use(store)
app.mount('#app')

```

