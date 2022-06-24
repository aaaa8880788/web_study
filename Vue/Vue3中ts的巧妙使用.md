

# 在vue3中ts的巧妙使用

# 技巧一(取ref对象)

## 说明:

在获取组件的ref对象时，我们可以通过<InstanceType<typeof 组件ref>>来给ref对象限制类型

## 作用：

在取到ref对象后，如果调用其组件内的方法，如果方法不存在会报错，增强代码安全性

## 注意：

子组件部分的setup写法如果用script标签方式，需要用defineExpose将方法暴露出去，否则父组件取不到子组件的方法(官方文档有说，如果用setup()函数的方法就不用考虑)

## 用法

```vue
// 父组件
<template>
  <div id="login">
    <login-account ref="loginAccountRef"> </login-account>
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue'
import loginAccount from './cpns/login-account.vue'

// 拿到loginAccount组件的ref对象
const loginAccountRef = ref<InstanceType<typeof loginAccount>>()

</script>

```

```
// 子组件
<template>
  <div>哈哈</div>
</template>

<script lang="ts" setup>
// 定义方法
// 点击登录触发
const loginAction = () => {
 	console.log('哈哈啊哈')
}

defineExpose({
  loginAction
})
</script>

<style scoped></style>

```



# 技巧二（vuex）

## 说明:

直接使用vuex里的store对象时，我们取里面的state的值是any类型，这是我们可以用小技巧给他一些类型限制

## 作用:

在调用vuex里面的state时随意乱写调用名称会报错,只有接口定义过的才能调用,增加代码安全性

## 用法:

### 根模块的接口类型定义

```typescript
// store/type.ts

import type { ILoginState } from './login/type'
// 根state的类型限制
export interface IRootState {
  name: string
  password: string
}
interface IRootWithModule {
  login: ILoginState
}

export type IStoreType = IRootState & IRootWithModule
```

### 根模块

```typescript
// store/index.ts
import { createStore, Store, useStore as useVuexStore } from 'vuex'

import login from './login/login'
import type { IRootState, IStoreType } from './type'

const store = createStore<IRootState>({
  state() {
    return {
      name: '',
      password: ''
    }
  },
  mutations: {},
  getters: {},
  actions: {},
  modules: {
    login
  }
})
// 给useStore出来的实例对象store加ts类型限制
export function useStore(): Store<IStoreType> {
  return useVuexStore()
}
export default store
```

### 子模块的接口类型定义

```typescript
// store/login/type.ts
export interface ILoginState {
  token: string
  userInfo: any
  userMenus: any
}
```

### 子模块

```typescript
// store/login/login.ts
import { Module } from 'vuex'

// 导入类型接口
import type { ILoginState } from './type'
import type { IRootState } from '../type'

const loginModule: Module<ILoginState, IRootState> = {
  namespaced: true,
  state() {
    return {
      token: '',
      userInfo: {},
      userMenus: []
    }
  },
  getters: {},
  mutations: {},
  actions: {}
  }
}
export default loginModule
```

### 在组件中用法

```vue
<template>
  <div>
    哈哈哈
  </div>
</template>

<script setup lang="ts">
// 这里导入的useStore不是vuex的,而是我们自己定义的那个(我们给它加了类型限制)
import { useStore } from '@/store'
const store = useStore()
// 到这里我们就可以发现store的类型不是any了，而是我们定义的Store<IStoreType>类型
// 而且在下面取state的时候我们会发现有代码提示了，如果写了state里面没有的值，会报错
const userMenus = store.state.login.userMenus
console.log(userMenus)
</script>
```

