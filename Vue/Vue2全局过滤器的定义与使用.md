# Vue2.0全局过滤器的定义与使用

## 第一步：定义过滤器所需函数

> 在`src`目录下创建filter文件夹，在里面创建 `js `文件，里面填写内容是过滤器所要用到的函数

> 这里我用的是日期格式化处理函数，用的是 `dayjs` 插件

```js
import dayjs from "dayjs";

export function date(val, b) {
  let data = dayjs(val).format(b);
  return data;
}
```

## 第二步：在main.js引用函数文件并注册全局过滤器

```js
// 定义全局时间处理过滤器
import { date } from './filter/dateFormate'
Vue.filter('date', date)
```



## 第三步：过滤器的使用

> 1.在template里面,直接在`{{}}`里面用|分隔使用，下面例子中，time为date函数的第一个参数，date里面的字符串为第二个参数，以此类推`{{ a | date(b,c.d) }}` 的话 a b c d 分别为第1234个参数

```vue
<template>
  <div >
  	{{ time | date('YYYY-MM-DD') }}
  </div>
</template>

<script>
export default {
  data() {
    return {
      time: new Date(), //当前日期
    };
  },
};
</script>

```

> 2.在方法中使用，用`this.$options.filters.date()`来调用,这里的参数有点区别，跟正常函数调用一样

```vue
<template>
  <div >
  	{{ time | date('YYYY-MM-DD') }}
  </div>
</template>

<script>
export default {
  data() {
    return {
      time: new Date(), //当前日期
    };
  },
  methods:{
  	handleDate(){
  		const data = this.$options.filters.date(time,'YYYY-MM-DD')
  		console.log(data)
  	},
  }
  created(){
  	this.handleDate()
  }
};
</script>
```

