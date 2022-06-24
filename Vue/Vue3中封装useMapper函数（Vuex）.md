# Vue3中封装useMapper函数（Vuex）

# 作用

```vue
<script>
import { useStore } from "vuex";
import { computed } from "@vue/runtime-core";
export default {
  setup() {
   	//1.传统方法在vuex中拿到state
    const oldCounter = computed(() => store.state.counter);
    const oldName = computed(() => store.state.name);
    const oldAge = computed(() => store.state.age); 
    return {
      oldCounter,
      oldName,
      oldAge,
    };
  },
};
</script>
```

```vue
<script>
import { useState } from "../hooks/useState";
export default {
  setup() {
    // 2.使用mapState封装的函数在vuex中拿到state
    const storeState1 = useState(["counter", "name", "age"]);
    return {
      ...storeState1,
    };
  },
};
</script>
```



# 实现代码

在src目录下创建hooks文件夹，在里面创建useMapper.js

```javascript
import {computed} from 'vue'
import { useStore } from 'vuex'

export function useMapper(mapper,mapFn){
    // 拿到store对象
    const store = useStore()

    // 获取到相应的对象的functions:{name:function,age:function}
    const storeMapperFns = mapFn(mapper)

    // 对数据进行转换
    const storeMapper = {}
    Object.keys(storeMapperFns).forEach(fnKey=>{
        const fn = storeMapperFns[fnKey].bind({$store:store})
        storeMapper[fnKey] = computed(fn)
    })
    
    return storeMapper
}
```

在src目录下创建hooks文件夹，在里面创建useState.js

```javascript
//  src/hooks/useState
import {useMapper} from './useMapper'
import { mapState , createNamespacedHelpers} from 'vuex'
export function useState(mapper,moduleName){
   let mapperFn = mapState
   if(typeof moduleName === 'string' && moduleName.length > 0){
      mapperFn = createNamespacedHelpers(moduleName).mapState
   }
   return useMapper(mapper,mapperFn)
}
```

## 测试使用

```vue
//		src/app.vue

<template>
  <div id="app">
    <h1>使用useState方法通过数组拿到state数据</h1>
    <h2>counter:{{counter}}</h2>
    <h2>name:{{name}}</h2>
    <h2>age:{{age}}</h2>
    <hr>
    <h1>使用useState方法通过对象拿到state数据</h1>
    <h2>sCounter:{{sCounter}}</h2>
    <h2>sName:{{sName}}</h2>
    <h2>sAge:{{sAge}}</h2>
    <hr>
    <h1>使用useState方法通过对象拿到state数据</h1>
    <h2>aCounter:{{aCounter}}</h2>
    <h2>aName:{{aName}}</h2>
    <h2>aAge:{{aAge}}</h2>
    <hr>
    <h1>使用传统方法拿到state数据</h1>
    <h2>oldCounter:{{oldCounter}}</h2>
    <h2>oldName:{{oldName}}</h2>
    <h2>oldAge:{{oldAge}}</h2>
    <hr>
    <button @click="sub">-1</button>
    <span>{{counter}}</span>
    <button @click="add">+1</button>
  </div>
</template>

<script>
import { useState } from "../hooks/useState";
import { useStore } from "vuex";
import { computed } from "@vue/runtime-core";
export default {
  setup() {
    // 使用mapState封装的函数拿到state
    // 使用方法一：
    const storeState1 = useState(["counter", "name", "age"]);
    // 使用方法二：
    const storeState2 = useState({
      sCounter: (state) => state.counter,
      sName: (state) => state.name,
      sAge: (state) => state.age,
    });
    // 使用方法三：
    const storeState3 = useState({
      aCounter: "counter",
      aName: "name",
      aAge: "age",
    });
    // 拿到store对象
    const store = useStore();
    // 传统方法拿到state
    const oldCounter = computed(() => store.state.counter);
    const oldName = computed(() => store.state.name);
    const oldAge = computed(() => store.state.age);

    // 点击+1
    const add = () => {
      store.commit("increment");
    };
    // 点击-1
    const sub = () => {
      store.commit("decrement");
    };

    return {
      ...storeState1,
      ...storeState2,
      ...storeState3,
      oldCounter,
      oldName,
      oldAge,
      add,
      sub,
    };
  },
};
</script>

<style lang="less">
#app {
  text-align: center;
}
</style>

```

