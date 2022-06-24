# ①在Vue3+Ts项目中封装axios

# 说明

采用了类封装思想，包含了三种情况的拦截器封装

①公共拦截器（所有实例都有的拦截器）

②特有拦截器（只有在new实例时传入的对象中传入了拦截器）

③独有拦截器（只有在调用请求的时候传入拦截器）

# 实现代码

在用cli脚手架创建完成项目后

在src下面创建network文件夹，在里面创建index.ts

该文件是拿到axios请求封装实例并导出使用（网络请求出口）

```typescript
// src/network/index.ts

// network的统一出口
import NWRequest from './request'
// 这里是有传入特有拦截器的实例
const nwRequest = new NWRequest({
  baseURL: process.env.VUE_APP_BASE_URL,
  timeout: process.env.VUE_APP_TIME_OUT,
  interceptors: {
    requestInterceptor: (config) => {
      console.log('在new实例时有传入拦截器的实例才有的拦截器:请求成功的拦截')
      return config
    },
    requestInterceptorCatch: (err) => {
      console.log('在new实例时有传入拦截器的实例才有的拦截器:请求失败的拦截')
      return err
    },
    responseInterceptor: (config) => {
      console.log('在new实例时有传入拦截器的实例才有的拦截器:响应成功的拦截')
      return config
    },
    responseInterceptorCatch: (err) => {
      console.log('在new实例时有传入拦截器的实例才有的拦截器:响应失败的拦截')
      return err
    }
  }
})
// 这里是没有有传入特有拦截器的实例
const nwRequest1 = new NWRequest({
  baseURL: process.env.VUE_APP_BASE_URL,
  timeout: process.env.VUE_APP_TIME_OUT
})

export { nwRequest, nwRequest1 }

```

在src的network下创建request文件夹，在里面创建type.ts

该文件是为了定义ts的限制类型接口interface

```typescript
import type { AxiosRequestConfig, AxiosResponse } from 'axios'

interface NWRequestInterceptors<T = AxiosResponse> {
  requestInterceptor?: (config: AxiosRequestConfig) => AxiosRequestConfig
  requestInterceptorCatch?: (error: any) => any
  responseInterceptor?: (res: T) => T
  responseInterceptorCatch?: (error: any) => any
}

interface NWRequestConfig<T = AxiosResponse> extends AxiosRequestConfig {
  interceptors?: NWRequestInterceptors<T>
}

export { NWRequestInterceptors, NWRequestConfig }

```

在src的network下的request文件夹，在里面创建index.ts

该文件是axios主要封装类

```typescript
// src/network/request/index.ts

import axios from 'axios'
import type { AxiosInstance } from 'axios'
import type { NWRequestInterceptors, NWRequestConfig } from './type'
// 定义网络请求的封装类
class NWRequest {
  instance: AxiosInstance
  interceptors?: NWRequestInterceptors
  constructor(config: NWRequestConfig) {
    this.instance = axios.create(config)
    this.interceptors = config.interceptors

    // 从config中取出的拦截器是对应的实例的拦截器
    // 只有new实例的时候有传入拦截器才有
    this.instance.interceptors.request.use(
      this.interceptors?.requestInterceptor,
      this.interceptors?.requestInterceptorCatch
    )
    this.instance.interceptors.response.use(
      this.interceptors?.responseInterceptor,
      this.interceptors?.responseInterceptorCatch
    )

    // 添加所有的实例都有的拦截器
    this.instance.interceptors.request.use(
      (config) => {
        console.log('所有的实例都有的拦截器:请求成功拦截')
        return config
      },
      (err) => {
        console.log('所有的实例都有的拦截器:请求失败拦截')
        return err
      }
    )
    this.instance.interceptors.response.use(
      (res) => {
        console.log('所有的实例都有的拦截器:响应成功拦截')
        return res
      },
      (err) => {
        console.log('所有的实例都有的拦截器:响应失败拦截')
        return err
      }
    )
  }

  request<T>(config: NWRequestConfig<T>): Promise<T> {
    return new Promise((resolve, reject) => {
      // 这里的作用是,如果在调用请求的时候想添加额外单独的拦截器
      if (config.interceptors?.requestInterceptor) {
        config = config.interceptors.requestInterceptor(config)
      }
      this.instance
        .request<any, T>(config)
        .then((res) => {
          if (config.interceptors?.responseInterceptor) {
            res = config.interceptors.responseInterceptor(res)
          }
          resolve(res)
        })
        .catch((err) => {
          reject(err)
        })
    })
  }

  get<T>(config: NWRequestConfig<T>): Promise<T> {
    return this.request<T>({
      ...config,
      method: 'GET'
    })
  }

  post<T>(config: NWRequestConfig<T>): Promise<T> {
    return this.request<T>({
      ...config,
      method: 'POST'
    })
  }
}

export default NWRequest


```

# 测试

在src下的main.ts中

```typescript
import { createApp } from 'vue'

import App from './App.vue'
import router from './router'
import store from './store'
import { nwRequest } from './network'

const app = createApp(App)

app.use(router)
app.use(store)

app.mount('#app')

// 该请求包含公共拦截器、特有拦截器
nwRequest.request({
  url: '/login',
  method: 'POST',
  data: {
    name: '哈哈哈',
    password: '123456'
  }
})
nwRequest
  .post({
    url: '/login',
    data: {
      name: '哈哈哈',
      password: '123456'
    }
  })
  .then((res) => {
    console.log(res)
  })
// 该请求包含公共拦截器、独有拦截器
// nwRequest1.request({
//   url: '/login',
//   method: 'POST',
//   data: {
//     name: '哈哈哈',
//     password: '123456'
//   },
//   interceptors: {
//     requestInterceptor: (config) => {
//       console.log('独有拦截器:单独请求的config')
//       return config
//     }
//   }
// })
// 该请求质只包含公共拦截器
// nwRequest1.request({
//   url: '/login',
//   method: 'POST',
//   data: {
//     name: '哈哈哈',
//     password: '123456'
//   }
// })
```



# ②删减版

# 说明

该部分在上面完整的基础上删减去一些类型限制，完整版里面甚至连返回的数据类型都做了限制，比较难懂

①公共拦截器（所有实例都有的拦截器）

②特有拦截器（只有在new实例时传入的对象中传入了拦截器）

# 实现代码

在用cli脚手架创建完成项目后

在src下面创建network文件夹，在里面创建index.ts

该文件是拿到axios请求封装实例并导出使用（网络请求出口）

```typescript
// src/network/index.ts

// network的统一出口
import NWRequest from './request'
// 这里是有传入特有拦截器的实例
const nwRequest = new NWRequest({
  baseURL: process.env.VUE_APP_BASE_URL,
  timeout: process.env.VUE_APP_TIME_OUT,
  interceptors: {
    requestInterceptor: (config: any) => {
      // console.log('在new实例时有传入拦截器的实例才有的拦截器:请求成功的拦截')
      // 携带token的拦截
      const token = localStorage.geiItem('token')
      if (token) {
        config.headers.Authorization = `Bearer ${token}`
      }
      return config
    },
    requestInterceptorCatch: (err) => {
      // console.log('在new实例时有传入拦截器的实例才有的拦截器:请求失败的拦截')
      return err
    },
    responseInterceptor: (config) => {
      // console.log('在new实例时有传入拦截器的实例才有的拦截器:响应成功的拦截')
      return config
    },
    responseInterceptorCatch: (err) => {
      // console.log('在new实例时有传入拦截器的实例才有的拦截器:响应失败的拦截')
      return err
    }
  }
})
export { nwRequest }

```

在src的network下创建request文件夹，在里面创建type.ts

该文件是为了定义ts的限制类型接口interface

```typescript
import type { AxiosRequestConfig, AxiosResponse } from 'axios'

interface NWRequestInterceptors<T = AxiosResponse> {
  requestInterceptor?: (config: AxiosRequestConfig) => AxiosRequestConfig
  requestInterceptorCatch?: (error: any) => any
  responseInterceptor?: (res: T) => T
  responseInterceptorCatch?: (error: any) => any
}

interface NWRequestConfig<T = AxiosResponse> extends AxiosRequestConfig {
  interceptors?: NWRequestInterceptors<T>
}

export { NWRequestInterceptors, NWRequestConfig }

```

在src的network下的request文件夹，在里面创建index.ts

该文件是axios主要封装类

```typescript
import axios from 'axios'
import type { AxiosInstance } from 'axios'
import type { NWRequestInterceptors, NWRequestConfig } from './type'
// 定义网络请求的封装类
class NWRequest {
  instance: AxiosInstance
  interceptors?: NWRequestInterceptors
  constructor(config: NWRequestConfig) {
    this.instance = axios.create(config)
    this.interceptors = config.interceptors

    // 从config中取出的拦截器是对应的实例的拦截器
    // 只有new实例的时候有传入拦截器才有
    this.instance.interceptors.request.use(
      this.interceptors?.requestInterceptor,
      this.interceptors?.requestInterceptorCatch
    )
    this.instance.interceptors.response.use(
      this.interceptors?.responseInterceptor,
      this.interceptors?.responseInterceptorCatch
    )

    // 添加所有的实例都有的拦截器
    this.instance.interceptors.request.use(
      (config) => {
        // console.log('所有的实例都有的拦截器:请求成功拦截')
        return config
      },
      (err) => {
        // console.log('所有的实例都有的拦截器:请求失败拦截')
        return err
      }
    )
    this.instance.interceptors.response.use(
      (res) => {
        // console.log('所有的实例都有的拦截器:响应成功拦截')
        return res.data
      },
      (err) => {
        // console.log('所有的实例都有的拦截器:响应失败拦截')
        return err
      }
    )
  }

  request(config: NWRequestConfig): Promise<any> {
    return new Promise((resolve, reject) => {
      this.instance
        .request(config)
        .then((res) => {
          resolve(res)
        })
        .catch((err) => {
          reject(err)
        })
    })
  }

  get(config: NWRequestConfig) {
    return this.request({
      ...config,
      method: 'GET'
    })
  }

  post(config: NWRequestConfig) {
    return this.request({
      ...config,
      method: 'POST'
    })
  }
}

export default NWRequest
```

# 测试

在src下的main.ts中

```typescript
import { createApp } from 'vue'

import App from './App.vue'
import router from './router'
import store from './store'
import { nwRequest } from './network'

const app = createApp(App)

app.use(router)
app.use(store)

app.mount('#app')
nwRequest
  .post({
    url: '/login',
    data: {
      name: '哈哈哈',
      password: '123456'
    }
  })
  .then((res) => {
    console.log(res)
  })
```

