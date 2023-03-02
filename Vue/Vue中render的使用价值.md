## `Vue`中render的使用价值

## 场景

假设说是低代码平台配置好的表单渲染，亦或者说自己封装的一个表单组件，通过传配置项来渲染，一般我们会怎么做呢？

## 常规做法

```vue
<template>
  <div class="wrapper">
    <div  
      v-for="item in item"
      :key="item.id">
      <template v-if="item.type === 'input'">
        <input></input>
      </template>
      <template v-else-if="item.type === 'select'">
        <select></select>
      </template>
    </div>
  </div>
</template>

<script>
  import input from '@/component/input'
  import select from '@/component/select'
  export default {
    data(){
      return {
        item:[
          {
            id:'1'
            type:'input'
          },
          {
            id:'2'
            type:'select'
          }
        ]
      }
    },
    components:{
      input,
      select
    }
  }
</script>
```

## 说明

从以上代码可以看出，如果说，我们有30个`type`，那我们就必须用`import`导入30个组件，在`compoents`里面注册30个组件，在模板里面写30个`template`+`v-if`，越多代码只会越来越冗余，但是我们用render的方式来渲染呢？

所以从中看出，常规做法的可读性极强，但是代码太过冗余，重复代码太多

## render使用

```vue
<template>
  <div class="wrapper">
    <div  
      v-for="item in item"
      :key="item.id">
      <business-component :item="item"/>
    </div>
  </div>
</template>

<script>
  import '@/utils/renderHandle.js'
  export default {
    data(){
      return {
        item:[
          {
            id:'1'
            type:'input'
          },
          {
            id:'2'
            type:'select'
          }
        ]
      }
    }
  }
</script>
```

```js
Vue.component('business-component',{
  render(h,context){
    const item = context.props.item
    if(item && item.type){
      try {
        const component = require(`@/components/${type}.vue`)
        if(component){
          return h(component,context)
        }
      } catch (error) {
        console.log('error----',error);
      }
    }
    return h('div')
  }
})
```

从这两部分代码可以看出，我无论有多少个组件，都不用像常规做法那样疯狂注册，疯狂导入，所以显然这里用render的方法来渲染是很完美的，不过其实这并不完善，因为`vue.componet`方法的第二个参数可并不仅仅只有`render`方法，它还可以用很多其他属性，假设说我们需要在使用的时候去添加一下其他属性呢？总不可能直接在上面的这个`business-component`的注册函数里面加吧，它只是个中介而已，负责转发，所以我们需要一个玄妙的注册函数来反传回去给我们去加东西

```js
Vue.component('business-component',{
  render(h,context){
    const compoentKey = context.props.key
    if(compoentKey){
      return h(compoentKey,context)
    }
    return h('div')
  }
})

export const registerFn = (key,fn) => {
  Vue.component(key,{
    render(h,context){
      const item = context.props.item
      const obj = fn(context)
      if(item && item.type){
        try {
          const component = require(`@/components/${type}.vue`)
          if(component){
            return h(component,obj)
          }
        } catch (error) {
          console.log('error----',error);
        }
      }
      return h('div')
    }
  })
}
```

```vue
<template>
  <div class="wrapper">
    <div  
      v-for="item in item"
      :key="item.id">
      <business-component 
        :item="item"
        :key="key"/>
    </div>
  </div>
</template>

<script>
  import { registerFn } '@/utils/renderHandle.js'
  export default {
    data(){
      return {
        item:[
          {
            id:'1'
            type:'input'
          },
          {
            id:'2'
            type:'select'
          }
        ],
        // 唯一的key
        key:999
      }
    },
    created(){
      this.register
    },
    methods:{
      register(){
        registerFn(this.key,(context) => {
          const {
            item
          } = context.props
          return {
            item,
            on: {
              test1(){
                console.log('我要监听了');
              }
            },
            off: {
              test2(){
                console.log('我不监听了');
              }
            }
          }
        })
      }
    }
  }
</script>

```

## 结语

是不是很简单呢，其实`render`也不是不能在`vue`中用的，有些场景它确实很好用
