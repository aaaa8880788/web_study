# Element Plus组件二次封装

# 前言

直至今天，看了一下别人写的代码，才发现以前自己写的代码太垃圾，所以在这次做的一个后台项目中，采用他的代码风格，怎么说呢，复用性特别好，封装的很好，学到很多，所以记录一下思路，我认为这个封装思路是真的很棒，写第一个页面的时候可能会麻烦一些，但是后面只要是相似的页面，事半功倍，直接CV改配置项就好了，是真的顶，记录一下，学习一下，我这里用的是vue3+ts

# Form表单的封装

## 简述

这里是Form表单部分，下面是完整的思路，最后有附上完整的代码，大佬可以直接看完整的代码就能看懂了，小白们跟着我的思路估计能看懂....

## 正常的使用

如果我们正常使用组件库里面的组件会是这样的

![image-20220506153055850](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Element%20Plus%E7%BB%84%E4%BB%B6%E4%BA%8C%E6%AC%A1%E5%B0%81%E8%A3%85/image-20220506153055850.png)

代码如下

role.vue页面组件

```vue
<template>
  <div class="role">
    <el-form>
      <el-form-item label="用户id">
        <el-input placeholder="请输入用户id"></el-input>
      </el-form-item>
      <el-form-item label="用户名">
        <el-input placeholder="请输入用户名"></el-input>
      </el-form-item>
      <el-form-item label="真实姓名">
        <el-input placeholder="请输入真实姓名"></el-input>
      </el-form-item>
      <el-form-item label="用户名">
        <el-input placeholder="请输入用户名"></el-input>
      </el-form-item>
      <el-form-item label="电话号码">
        <el-input placeholder="请输入电话号码"></el-input>
      </el-form-item>
      <el-form-item label="用户状态">
        <el-select placeholder="请选择用户状态">
          <el-option label="禁用" value="0"></el-option>
          <el-option label="启用" value="1"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="创建时间">
        <el-date-picker
          startPlaceholder="开始时间"
          endPlaceholder="结束时间"
          type="daterange"
        ></el-date-picker>
      </el-form-item>
    </el-form>
  </div>
</template>

<script setup lang="ts"></script>

<style scoped lang="less"></style>

```

这时我们可以加点样式让他变得好看，并且布局也变一变就可以变成这样,当然样式布局可以自定义

![image-20220506153107353](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Element%20Plus%E7%BB%84%E4%BB%B6%E4%BA%8C%E6%AC%A1%E5%B0%81%E8%A3%85/image-20220506153107353.png)



代码如下

role.vue页面组件

```vue
<template>
  <div class="role">
    <el-form labelWidth="120px">
      <el-row>
        <el-col :span="8">
          <el-form-item
            label="用户id"
            :style="{
              padding: '10px 20px'
            }"
          >
            <el-input placeholder="请输入用户id"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item
            label="用户名"
            :style="{
              padding: '10px 20px'
            }"
          >
            <el-input placeholder="请输入用户名"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item
            label="真实姓名"
            :style="{
              padding: '10px 20px'
            }"
          >
            <el-input placeholder="请输入真实姓名"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item
            label="电话号码"
            :style="{
              padding: '10px 20px'
            }"
          >
            <el-input placeholder="请输入电话号码"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item
            label="用户状态"
            :style="{
              padding: '10px 20px'
            }"
          >
            <el-select placeholder="请选择用户状态">
              <el-option label="禁用" value="0"></el-option>
              <el-option label="启用" value="1"></el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item
            label="创建时间"
            :style="{
              padding: '10px 20px'
            }"
          >
            <el-date-picker
              startPlaceholder="开始时间"
              endPlaceholder="结束时间"
              type="daterange"
            ></el-date-picker>
          </el-form-item>
        </el-col>
      </el-row>
    </el-form>
  </div>
</template>

<script setup lang="ts"></script>

<style scoped lang="less">
.el-form-item {
  margin-top: 18px;
}
.el-select {
  width: 100%;
}
</style>

```

## 开始封装①

这时我们就可以开始封装了，如果我们可以通过传配置项的方法来控制样式和form表单项的类型和个数的话，是不是变得很方便，下次直接传配置项用就好了？话不多说直接上图上代码

可以看到效果一样，代码却简洁了，模板里面不会出现大量重复的代码了

![image-20220506153117544](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Element%20Plus%E7%BB%84%E4%BB%B6%E4%BA%8C%E6%AC%A1%E5%B0%81%E8%A3%85/image-20220506153117544.png)

role.vue页面组件

```vue
<template>
  <div class="role">
    <el-form :labelWidth="searchFormConfig.labelWidth">
      <el-row>
        <template v-for="item in searchFormConfig.formItems" :key="item.label">
          <el-col :span="8">
            <el-form-item
              :label="item.label"
              :style="searchFormConfig.itemStyle"
            >
              <template
                v-if="item.type === 'input' || item.type === 'password'"
              >
                <el-input 
                    :placeholder="item.placeholder" 
                    :show-password="item.type === 'password'"
                ></el-input>
              </template>
              <template v-else-if="item.type === 'select'">
                <el-select :placeholder="item.placeholder">
                  <el-option
                    v-for="option in item.options"
                    :key="option.value"
                    :label="option.label"
                    :value="option.value"
                  ></el-option>
                </el-select>
              </template>
              <template v-else>
                <el-date-picker v-bind="item.otherOptions"></el-date-picker>
              </template>
            </el-form-item>
          </el-col>
        </template>
      </el-row>
    </el-form>
  </div>
</template>

<script setup lang="ts">
// 定义表单配置项
const searchFormConfig = {
  formItems: [
    {
      type: 'input',
      label: '用户id',
      placeholder: '请输入用户id'
    },
    {
      type: 'input',
      label: '用户名',
      placeholder: '请输入用户名'
    },
    {
      type: 'input',
      label: '真实姓名',
      placeholder: '请输入真实姓名'
    },
    {
      type: 'input',
      label: '电话号码',
      placeholder: '请输入电话号码'
    },
    {
      type: 'select',
      label: '用户状态',
      placeholder: '请选择用户状态',
      options: [
        {
          label: '禁用',
          value: 0
        },
        {
          label: '启用',
          value: 1
        }
      ]
    },
    {
      type: 'datepicker',
      label: '创建时间',
      otherOptions: {
        startPlaceholder: '开始时间',
        endPlaceholder: '结束时间',
        type: 'daterange',
        'unlink-panels': true
      }
    }
  ],
  labelWidth: '120px',
  itemStyle: {
    padding: '10px 20px'
  },
  itemColLayout: {
    span: 8
  }
}
</script>

<style scoped lang="less">
.el-form-item {
  margin-top: 18px;
}
.el-select {
  width: 100%;
}
</style>

```

## 开始封装②

这时它复用的锥形已经有了，我们可以将配置项抽出去，并给它一些类型限制，把这部分使用表单的代码抽出去，封装成form组件，这样之后我们在用的时候，直接用这个组件然后给它传配置项就可以了

1.配置项类型限制文件（不用ts的话就没有，不想限制全部给any类型随意，我这里是为了让代码严谨一丢丢哈哈）

```typescript
type IFormType = 'input' | 'password' | 'select' | 'datepicker'

interface IFormOption {
  label: string
  value: string | number
}

export interface IFormItem {
  type: IFormType //输入框类型
  label: string //输入框标题
  placeholder?: any //输入框默认显示内容
  // 针对select
  options?: IFormOption[] //选择器的可选子选项
  // 针对特殊属性
  otherOptions?: any

}

export interface IForm {
  formItems: IFormItem[]
  labelWidth?: string
  itemStyle?: any
  itemColLayout?: any
}

```

2.配置项文件

```typescript
import { IForm } from '@/base-ui/form/type'
export const searchFormConfig: IForm = {
  formItems: [
    {
      type: 'input',
      label: '用户id',
      placeholder: '请输入用户id'
    },
    {
      type: 'input',
      label: '用户名',
      placeholder: '请输入用户名'
    },
    {
      type: 'input',
      label: '真实姓名',
      placeholder: '请输入真实姓名'
    },
    {
      type: 'input',
      label: '电话号码',
      placeholder: '请输入电话号码'
    },
    {
      type: 'select',
      label: '用户状态',
      placeholder: '请选择用户状态',
      options: [
        { label: '启用', value: 1 },
        { label: '禁用', value: 0 }
      ]
    },
    {
      type: 'datepicker',
      label: '创建时间',
      otherOptions: {
        startPlaceholder: '开始时间',
        endPlaceholder: '结束时间',
        type: 'daterange'
      }
    }
  ],
  labelWidth: '120px',
  itemColLayout: {
    span: 8
  },
  itemStyle: {
    padding: '10px 20px'
  }
}

```

3.form表单文件

注意:在这里，我将labelWidth，itemColLayout，itemStyle设置了默认值，所以我上面的那些样式配置项可以不传，默认就是我设置的那些值，如果需要别的样式可以传入修改，不要样式可以传个空进去，这里我还加了两个插槽，增加可扩展性

```vue
<template>
  <div class="header">
    <slot name="header"> </slot>
  </div>
  <el-form ref="ruleFormRef" :labelWidth="labelWidth">
    <el-row>
      <template v-for="item in formItems" :key="item.label">
        <el-col v-bind="itemColLayout">
          <el-form-item
            v-if="!item.isHidden"
            :label="item.label"
            :style="itemStyle"
            :prop="item.field"
          >
            <template v-if="item.type === 'input' || item.type === 'password'">
              <el-input
                :placeholder="item.placeholder"
                :show-password="item.type === 'password'"
              ></el-input>
            </template>
            <template v-else-if="item.type === 'select'">
              <el-select :placeholder="item.placeholder">
                <el-option
                  v-for="option in item.options"
                  :key="option.label"
                  :label="option.label"
                  :value="option.value"
                ></el-option>
              </el-select>
            </template>
            <template v-if="item.type === 'datepicker'">
              <el-date-picker v-bind="item.otherOptions"></el-date-picker>
            </template>
          </el-form-item>
        </el-col>
      </template>
    </el-row>
  </el-form>
  <div class="footer">
    <slot name="footer"></slot>
  </div>
</template>

<script setup lang="ts">
import { defineProps, withDefaults } from 'vue'
import { IFormItem } from './type'
interface Prop {
  formItems: IFormItem[] // 表单配置项
  labelWidth?: string // 每个表单标题宽度
  itemStyle?: object // 每个表单样式
  itemColLayout?: object // 表单布局
  isHidden?: boolean // 该输入框是否隐藏
}
const props = withDefaults(defineProps<Prop>(), {
  labelWidth: '120px',
  itemColLayout: () => ({
    xl: 6, // >=1920px
    lg: 8, // >=1200px
    md: 12, // >=992px
    sm: 24, // >=768px
    xs: 24 // <768px
  }),
  itemStyle: () => ({
    padding: '10px 20px'
  })
})
</script>

<style scoped>
.el-form-item {
  margin-top: 18px;
}
.el-select {
  width: 100%;
}
</style>

```

4.role.vue页面组件

```vue
<template>
  <div class="role">
    <form-test v-bind="searchFormConfig"></form-test>
  </div>
</template>

<script setup lang="ts">
import formTest from '@/base-ui/form/form-test.vue'
import { searchFormConfig } from './config/search-config-test'
</script>

<style scoped lang="less"></style>

```

这时已经初步封装好了，我们可以使用一下看效果,我们可以看到样式跟之前完全一样，但是页面的代码量就那么点，要用的话直接用我们封装好的form组件然后传入配置项就出来了

![image-20220506153130565](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Element%20Plus%E7%BB%84%E4%BB%B6%E4%BA%8C%E6%AC%A1%E5%B0%81%E8%A3%85/image-20220506153130565.png)



它的可扩展性也是很强的，比如：

这里我们把样式配置项全部传空值，然后配置项也传一个，它又变成原来最丑的样子了，证明我们是可以随意更改它的样式和布局，只需要通过传入配置项更改就可以了，方便

配置项文件

```
import { IForm } from '@/base-ui/form/type'
export const searchFormConfig: IForm = {
  formItems: [
    {
      field: 'id',
      type: 'input',
      label: '用户id',
      placeholder: '请输入用户id'
    }
  ],
  labelWidth: '',
  itemColLayout: {},
  itemStyle: {}
}

```

![image-20220506153141995](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Element%20Plus%E7%BB%84%E4%BB%B6%E4%BA%8C%E6%AC%A1%E5%B0%81%E8%A3%85/image-20220506153141995.png)

其实到这里还没结束，因为这时的表单还输入不了东西，因为我们根本就没给它的输入框绑定值，所以我们要在配置项传入多一个field字段，它可以作为输入框绑定的值

## 开始封装③

这里仅仅是给配置项中增加field字段（注意如果用了ts的还要去type文件里面给我们定义的IFormItem接口添加一个field字段）

配置项文件

```typescript
import { IForm } from '@/base-ui/form/type'
export const searchFormConfig: IForm = {
  formItems: [
    {
      field: 'id',
      type: 'input',
      label: '用户id',
      placeholder: '请输入用户id'
    },
    {
      field: 'name',
      type: 'input',
      label: '用户名',
      placeholder: '请输入用户名'
    },
    {
      field: 'realname',
      type: 'input',
      label: '真实姓名',
      placeholder: '请输入真实姓名'
    },
    {
      field: 'cellphone',
      type: 'input',
      label: '电话号码',
      placeholder: '请输入电话号码'
    },
    {
      field: 'enable',
      type: 'select',
      label: '用户状态',
      placeholder: '请选择用户状态',
      options: [
        { label: '启用', value: 1 },
        { label: '禁用', value: 0 }
      ]
    },
    {
      field: 'createAt',
      type: 'datepicker',
      label: '创建时间',
      otherOptions: {
        startPlaceholder: '开始时间',
        endPlaceholder: '结束时间',
        type: 'daterange'
      }
    }
  ],
  labelWidth: '120px',
  itemColLayout: {
    span: 8
  },
  itemStyle: {
    padding: '10px 20px'
  }
}
```

因为传入了fied字段，所以我们要收集所有的field字段，组成formData数据，传入表单组件，formData里面的每个子项分别作为每个输入框绑定的值

注意：这里有两个难点

难点一：

我们传进去的数据在里面是要做修改传出来的，而vue的原则是单项数据流传输，我们不能直接将数据传进去（其实事实可以这样做，但是违背了单向数据流传输原则，我们尽量不违背哈），所以我们采用v-model的方式将formData传入form组件，这样做的话就是双向判定了，不算违背嘿嘿

难点二：因为我们传进去的formData的数据，并不是在form组件里面用的，而是要绑定到form组件里面的element puls的输入框里面的，所以我们在form组件里面接收到formData数据，然后在把formData它的各个子项v-model绑定到输入框里面，但是这样会报错，不能直接用v-model,这里就需要知道v-model是怎么实现的了，我们在这里是直接把接收到的formData数据绑定到输入框里面的，在form组件并没有定义formData这个变量，所以不能直接用v-model的方法，这了可能有点懵，举个例子

（比如你将一个值test用v-model传入一个input的框，你输入框输入数据，你的test是会同步改变，也就是说，v-model会把你修改后的值传出来赋值给你的test，而在这里，我们将formData用v-model绑定到输入框，输入框值改变，正常来说它会将修改后的值赋值给我们传进去的formData，但是我们不能让它直接赋值给我们的formData，因为我们的formData也是从别的组件传进来的，所以我们要把修改后的值再次传出去到传进来formData数据的那个组件中，而不是直接就赋值，这时我们就要用到v-model的原始写法了，其实v-model是个语法糖来的）

form.vue组件

```
<template>
  <div class="header">
    <slot name="header"> </slot>
  </div>
  <el-form ref="ruleFormRef" :labelWidth="labelWidth">
    <el-row>
      <template v-for="item in formItems" :key="item.label">
        <el-col v-bind="itemColLayout">
          <el-form-item
            v-if="!item.isHidden"
            :label="item.label"
            :style="itemStyle"
          >
            <template v-if="item.type === 'input' || item.type === 'password'">
              <el-input
                :placeholder="item.placeholder"
                :show-password="item.type === 'password'"
                :model-value="modelValue[`${item.field}`]"
                @update:modelValue="valueChange($event, item.field)"
              ></el-input>
            </template>
            <template v-else-if="item.type === 'select'">
              <el-select
                :placeholder="item.placeholder"
                :model-value="modelValue[`${item.field}`]"
                @update:modelValue="valueChange($event, item.field)"
              >
                <el-option
                  v-for="option in item.options"
                  :key="option.label"
                  :label="option.label"
                  :value="option.value"
                ></el-option>
              </el-select>
            </template>
            <template v-if="item.type === 'datepicker'">
              <el-date-picker
                v-bind="item.otherOptions"
                :model-value="modelValue[`${item.field}`]"
                @update:modelValue="valueChange($event, item.field)"
              ></el-date-picker>
            </template>
          </el-form-item>
        </el-col>
      </template>
    </el-row>
  </el-form>
  <div class="footer">
    <slot name="footer"></slot>
  </div>
</template>

<script setup lang="ts">
import { defineProps, withDefaults, defineEmits } from 'vue'
import { IFormItem } from './type'
interface Prop {
  formItems: IFormItem[] // 表单配置项
  labelWidth?: string // 每个表单标题宽度
  itemStyle?: object // 每个表单样式
  itemColLayout?: object // 表单布局
  isHidden?: boolean // 该输入框是否隐藏
  modelValue: object //绑定表单的每个数据
}
const props = withDefaults(defineProps<Prop>(), {
  labelWidth: '120px',
  itemColLayout: () => ({
    xl: 6, // >=1920px
    lg: 8, // >=1200px
    md: 12, // >=992px
    sm: 24, // >=768px
    xs: 24 // <768px
  }),
  itemStyle: () => ({
    padding: '10px 20px'
  })
})
const emit = defineEmits<{
  (e: 'update:modelValue', value: any): void
}>()

// 输入框值改变该函数都会触发，将改变后的值传出去
const valueChange = (value: any, field: string) => {
  emit('update:modelValue', { ...props.modelValue, [field]: value })
}
</script>

<style scoped>
.el-form-item {
  margin-top: 18px;
}
.el-select {
  width: 100%;
}
</style>

```

role.vue页面组件

```vue
<template>
  <div class="role">
    <form-test v-bind="searchFormConfig" v-model="formData"></form-test>
  </div>
</template>

<script setup lang="ts">
import formTest from '@/base-ui/form/form-test.vue'
import { searchFormConfig } from './config/search-config-test'
import { ref } from 'vue'
// 在这里取出所有的field字段组成formData数据
const formItems = searchFormConfig.formItems ?? []

let formDataInit = {}
formItems.map((item) => {
  formDataInit[item.field] = ''
})
let formData = ref(formDataInit)
</script>

<style scoped lang="less"></style>

```



![image-20220506153156166](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Element%20Plus%E7%BB%84%E4%BB%B6%E4%BA%8C%E6%AC%A1%E5%B0%81%E8%A3%85/image-20220506153156166.png)

这时我们发现它可以拿到数据了，很nice，其实这差不多已经算封装好了，可以通过配置项修改里面的东西了，同时也可以拿到数据，但是我这个项目不止于此，我这其实要做表单的查询的，所以我要改装一下变成这样

![image-20220506153210281](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Element%20Plus%E7%BB%84%E4%BB%B6%E4%BA%8C%E6%AC%A1%E5%B0%81%E8%A3%85/image-20220506153210281.png)

其实就是加了两个插槽和两个方法，我这里要实现功能就是点击重置按钮，它会重置表单数据，点击搜索按钮就可以拿到表单数据，这样我们就可以用我们拿到的表单数据去进行我们的操作拉，所以上代码

role.vue组件

该部分我们传入了两个template，一个是标题：高级检索，一个是两个按钮

这里要重置按钮重置表单数据，取到表单的ref调用resetFields方法就好了，然后点击搜索按钮可以打印出formData数据，然后我们就可以利用该数据去做我们想要做的操作了，例如查询列表数据等

```vue
<template>
  <div class="role">
    <form-test v-bind="searchFormConfig" v-model="formData" ref="formTestRef">
      <template #header>
        <div class="header">
          <h1>高级检索</h1>
        </div>
      </template>
      <template #footer>
        <div class="footer">
          <el-button type="primary" :icon="Refresh" @click="resetBtnClick"
            >重置</el-button
          >
          <el-button type="primary" :icon="Search" @click="searchBtnClick"
            >搜索</el-button
          >
        </div>
      </template>
    </form-test>
  </div>
</template>

<script setup lang="ts">
import formTest from '@/base-ui/form/form-test.vue'
import { searchFormConfig } from './config/search-config-test'
import { ref } from 'vue'
import { Search, Refresh } from '@element-plus/icons-vue'
// 在这里取出所有的field字段组成formData数据
const formItems = searchFormConfig.formItems ?? []

let formDataInit = {}
formItems.map((item) => {
  formDataInit[item.field] = ''
})
let formData = ref(formDataInit)

const formTestRef = ref<InstanceType<typeof formTest>>()

// 重置点击
const resetBtnClick = () => {
  formTestRef.value?.resetForm()
}
// 搜索点击
const searchBtnClick = () => {
  // 这里需要遍历搜索配置项，配置项里可以传dataType，要求数据返回什么类型的数据
  let queryInfo = { ...formData.value }
  searchFormConfig.formItems.map((item: any) => {
    if (item.dataType === 'number' && queryInfo[item.field] !== '') {
      queryInfo[item.field] = Number(queryInfo[item.field])
    }
  })
  // 清空queryInfo中没有值的属性
  for (const i in queryInfo) {
    if (queryInfo[i] === '') {
      delete queryInfo[i]
    }
  }
  console.log(queryInfo)
}
</script>

<style scoped lang="less">
.header {
  padding-top: 20px;
}
.footer {
  text-align: right;
  padding: 0 50px 20px 0;
}
</style>

```

form.vue

```vue
<template>
  <div class="header">
    <slot name="header"> </slot>
  </div>
  <el-form ref="ruleFormRef" :labelWidth="labelWidth" :model="modelValue">
    <el-row>
      <template v-for="item in formItems" :key="item.label">
        <el-col v-bind="itemColLayout">
          <el-form-item
            v-if="!item.isHidden"
            :label="item.label"
            :style="itemStyle"
            :prop="item.field"
          >
            <template v-if="item.type === 'input' || item.type === 'password'">
              <el-input
                :placeholder="item.placeholder"
                :show-password="item.type === 'password'"
                :model-value="modelValue[`${item.field}`]"
                @update:modelValue="valueChange($event, item.field)"
              ></el-input>
            </template>
            <template v-else-if="item.type === 'select'">
              <el-select
                :placeholder="item.placeholder"
                :model-value="modelValue[`${item.field}`]"
                @update:modelValue="valueChange($event, item.field)"
              >
                <el-option
                  v-for="option in item.options"
                  :key="option.label"
                  :label="option.label"
                  :value="option.value"
                ></el-option>
              </el-select>
            </template>
            <template v-if="item.type === 'datepicker'">
              <el-date-picker
                v-bind="item.otherOptions"
                :model-value="modelValue[`${item.field}`]"
                @update:modelValue="valueChange($event, item.field)"
              ></el-date-picker>
            </template>
          </el-form-item>
        </el-col>
      </template>
    </el-row>
  </el-form>
  <div class="footer">
    <slot name="footer"></slot>
  </div>
</template>

<script setup lang="ts">
import { defineProps, withDefaults, defineEmits, ref, defineExpose } from 'vue'
import { IFormItem } from './type'
import type { FormInstance } from 'element-plus'

interface Prop {
  formItems: IFormItem[] // 表单配置项
  labelWidth?: string // 每个表单标题宽度
  itemStyle?: object // 每个表单样式
  itemColLayout?: object // 表单布局
  isHidden?: boolean // 该输入框是否隐藏
  modelValue: object //绑定表单的每个数据
}
const props = withDefaults(defineProps<Prop>(), {
  labelWidth: '120px',
  itemColLayout: () => ({
    xl: 6, // >=1920px
    lg: 8, // >=1200px
    md: 12, // >=992px
    sm: 24, // >=768px
    xs: 24 // <768px
  }),
  itemStyle: () => ({
    padding: '10px 20px'
  })
})
const emit = defineEmits<{
  (e: 'update:modelValue', value: any): void
}>()
const ruleFormRef = ref<FormInstance>()

// 输入框值改变该函数都会触发，将改变后的值传出去
const valueChange = (value: any, field: string) => {
  emit('update:modelValue', { ...props.modelValue, [field]: value })
}

// 表单重置方法
const resetForm = () => {
  ruleFormRef.value?.resetFields()
}
defineExpose({
  resetForm
})
</script>

<style scoped>
.el-form-item {
  margin-top: 18px;
}
.el-select {
  width: 100%;
}
</style>
```

1.该组件要添加表单重置的方法

2.把formData的值绑定到form表单上：model=‘formData’

3.给el-form-item加上prop属性

2，3如果不加上的话，表单内置的重置表单方法会失效

```

```

![image-20220506153221867](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Element%20Plus%E7%BB%84%E4%BB%B6%E4%BA%8C%E6%AC%A1%E5%B0%81%E8%A3%85/image-20220506153221867.png)

![image-20220506153231986](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Element%20Plus%E7%BB%84%E4%BB%B6%E4%BA%8C%E6%AC%A1%E5%B0%81%E8%A3%85/image-20220506153231986.png)

这时我们已经封装完成了，但是我们可以发现，我们的role组件东西有点多了，如果我们其他组件比如，user组件等，都要用这样类似的布局，我们这时就又要把这一堆代码给cv一遍，所以我们有可以把role里面这堆东西再封装一次

## 开始封装④

page-search.vue组件

```vue
<template>
  <Bu-form v-bind="searchFormConfig" v-model="formData" ref="BuFormRef">
    <template #header>
      <div class="header">
        <h1>高级检索</h1>
      </div>
    </template>
    <template #footer>
      <div class="footer">
        <el-button type="primary" :icon="Refresh" @click="resetBtnClick"
          >重置</el-button
        >
        <el-button type="primary" :icon="Search" @click="searchBtnClick"
          >搜索</el-button
        >
      </div>
    </template>
  </Bu-form>
</template>

<script setup lang="ts">
import { Search, Refresh } from '@element-plus/icons-vue'
import BuForm from '@/base-ui/form/form-test.vue'
import { defineProps, ref, defineEmits } from 'vue'
import { useStore } from '@/store'
interface Prop {
  searchFormConfig: any
}
const props = defineProps<Prop>()
const emit = defineEmits<{
  (e: 'resetBtnClick'): void
  (e: 'searchBtnClick', formData: object): void
}>()
const store = useStore()
const BuFormRef = ref<InstanceType<typeof BuForm>>()

const formItems = props.searchFormConfig?.formItems ?? []

let formDataInit = {}
formItems.map((item: any) => {
  formDataInit[item.field] = ''
})
let formData = ref(formDataInit)

// 重置点击
const resetBtnClick = () => {
  BuFormRef.value?.resetForm()
  emit('resetBtnClick')
}
// 搜索点击
const searchBtnClick = () => {
  // 这里需要遍历搜索配置项，配置项里可以传dataType，要求数据返回什么类型的数据
  let queryInfo = { ...formData.value }
  props.searchFormConfig.formItems.map((item: any) => {
    if (item.dataType === 'number' && queryInfo[item.field] !== '') {
      queryInfo[item.field] = Number(queryInfo[item.field])
    }
  })
  // 清空queryInfo中没有值的属性
  for (const i in queryInfo) {
    if (queryInfo[i] === '') {
      delete queryInfo[i]
    }
  }
  emit('searchBtnClick', queryInfo)
}
</script>

<style scoped>
.header {
  padding-top: 20px;
}
.footer {
  text-align: right;
  padding: 0 50px 20px 0;
}
</style>

```

role.vue组件

```vue
<template>
  <div class="role">
    <page-search-test
      :searchFormConfig="searchFormConfig"
      @resetBtnClick="handlerReset"
      @searchBtnClick="handlerSearch"
    ></page-search-test>
  </div>
</template>

<script setup lang="ts">
import { searchFormConfig } from './config/search-config-test'
import pageSearchTest from '@/components/page-search/page-search-test.vue'
const handlerReset = () => {
  console.log('点击了重置按钮')
}
const handlerSearch = (queryInfo: any) => {
  console.log('点击了搜索按钮，值为:', queryInfo)
}
</script>

<style scoped lang="less"></style>

```

效果图

![image-20220506153242943](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Element%20Plus%E7%BB%84%E4%BB%B6%E4%BA%8C%E6%AC%A1%E5%B0%81%E8%A3%85/image-20220506153242943.png)

这里我们就可以体会到了，一样的效果，role里面的代码是这么的少，只需要传入配置项就可以搞出这个表单，而且还能拿到表单数据，而且重点是，下个页面再用这个布局，直接用page-search组件就可以了，只需要传入不同的配置项，如果不同布局，再封装另一个page-search就是了，但是这是后台耶？搞那么华丽呼哨？不就是搜索表单，展示表单么

下面附上完整全部封装代码

## 完整封装代码⑤

### 配置项类型文件

```typescript
// type.ts

type IFormType = 'input' | 'password' | 'select' | 'datepicker'

interface IFormOption {
  label: string
  value: string | number
}

export interface IFormItem {
  field: string //字段名
  type: IFormType //输入框类型
  dataType?: string //输入框返回数据类型
  label: string //输入框标题
  rules?: any[] //输入框验证规则
  placeholder?: any //输入框默认显示内容
  // 针对select
  options?: IFormOption[] //选择器的可选子选项
  // 针对特殊属性
  otherOptions?: any
  // 该行是否隐藏
  isHidden?: boolean
}

export interface IForm {
  formItems: IFormItem[]
  labelWidth?: string
  itemStyle?: any
  itemColLayout?: any
}

```

### 配置项文件

```
import { IForm } from '@/base-ui/form/type'
export const searchFormConfig: IForm = {
  formItems: [
    {
      field: 'id',
      type: 'input',
      label: '用户id',
      placeholder: '请输入用户id'
    },
    {
      field: 'name',
      type: 'input',
      label: '用户名',
      placeholder: '请输入用户名'
    },
    {
      field: 'realname',
      type: 'input',
      label: '真实姓名',
      placeholder: '请输入真实姓名'
    },
    {
      field: 'cellphone',
      type: 'input',
      label: '电话号码',
      placeholder: '请输入电话号码'
    },
    {
      field: 'enable',
      type: 'select',
      label: '用户状态',
      placeholder: '请选择用户状态',
      options: [
        { label: '启用', value: 1 },
        { label: '禁用', value: 0 }
      ]
    },
    {
      field: 'createAt',
      type: 'datepicker',
      label: '创建时间',
      otherOptions: {
        startPlaceholder: '开始时间',
        endPlaceholder: '结束时间',
        type: 'daterange'
      }
    }
  ]
}

```

### form表单组件文件

```vue
<template>
  <div class="header">
    <slot name="header"> </slot>
  </div>
  <el-form
    :label-width="labelWidth"
    ref="ruleFormRef"
    status-icon
    :model="modelValue"
  >
    <el-row>
      <template v-for="item in formItems" :key="item.label">
        <el-col v-bind="itemColLayout">
          <el-form-item
            v-if="!item.isHidden"
            :label="item.label"
            :rules="item.rules"
            :style="itemStyle"
            :prop="item.field"
          >
            <template v-if="item.type === 'input' || item.type === 'password'">
              <el-input
                :placeholder="item.placeholder"
                :show-password="item.type === 'password'"
                :model-value="modelValue[`${item.field}`]"
                @update:modelValue="valueChange($event, item.field)"
                clearable
              />
            </template>
            <template v-else-if="item.type === 'select'">
              <el-select
                :placeholder="item.placeholder"
                :model-value="modelValue[`${item.field}`]"
                @update:modelValue="valueChange($event, item.field)"
                style="width: 100%"
                clearable
              >
                <el-option
                  v-for="option in item.options"
                  :key="option.value"
                  :value="option.value"
                  :label="option.label"
                >
                </el-option>
              </el-select>
            </template>
            <template v-else-if="item.type === 'datepicker'">
              <el-date-picker
                unlink-panels
                v-bind="item.otherOptions"
                :model-value="modelValue[`${item.field}`]"
                @update:modelValue="valueChange($event, item.field)"
              ></el-date-picker>
            </template>
          </el-form-item>
        </el-col>
      </template>
    </el-row>
  </el-form>
  <div class="footer">
    <slot name="footer"></slot>
  </div>
</template>

<script setup lang="ts">
import { IFormItem } from './type'
import { defineProps, withDefaults, ref, defineEmits, defineExpose } from 'vue'
import type { FormInstance } from 'element-plus'

// 定义属性
interface Props {
  formItems: IFormItem[] // 表单配置项
  labelWidth?: string // 每个表单标题宽度
  itemStyle?: object // 每个表单样式
  itemColLayout?: object // 表单布局
  modelValue: object //绑定表单的每个数据
  isHidden?: boolean
}
const props = withDefaults(defineProps<Props>(), {
  formItems: () => [],
  labelWidth: '120px',
  itemStyle: () => ({ padding: '10px 20px' }),
  itemColLayout: () => ({
    xl: 6, // >=1920px
    lg: 8, // >=1200px
    md: 12, // >=992px
    sm: 24, // >=768px
    xs: 24 // <768px
  })
})
const emit = defineEmits<{
  (e: 'update:modelValue', value: any): void
}>()

const ruleFormRef = ref<FormInstance>()

// 定义方法
const valueChange = (value: any, field: string) => {
  emit('update:modelValue', { ...props.modelValue, [field]: value })
}

// 表单重置方法
const resetForm = () => {
  ruleFormRef.value?.resetFields()
}
defineExpose({
  resetForm
})
</script>

<style scoped lang="less">
.el-form-item {
  margin-top: 18px;
}
</style>

```

### page-search组件文件

```
<template>
  <div class="page-search">
    <Bu-form v-bind="searchFormConfig" v-model="formData" ref="BuFormRef">
      <template #header>
        <div class="header">
          <h1>高级检索</h1>
        </div>
      </template>
      <template #footer>
        <div class="footer">
          <el-button type="primary" :icon="Refresh" @click="resetClick"
            >重置</el-button
          >
          <el-button type="primary" :icon="Search" @click="searchClick"
            >搜索</el-button
          >
        </div>
      </template>
    </Bu-form>
  </div>
</template>

<script setup lang="ts">
import { useStore } from '@/store'
import BuForm from '@/base-ui/form/form.vue'
import { Search, Refresh } from '@element-plus/icons-vue'
import { ref, defineProps, defineEmits } from 'vue'
import { IForm } from '@/base-ui/form/type'

// 定义属性
interface Props {
  searchFormConfig: IForm
}
const props = defineProps<Props>()
const emit = defineEmits<{
  (e: 'resetBtnClick'): void
  (e: 'searchBtnClick', formData: object): void
}>()
const store = useStore()
const BuFormRef = ref<InstanceType<typeof BuForm>>()

// 1.从接收到的搜索配置中取出各个field,组成表单的数据formData
const formItems = props.searchFormConfig?.formItems ?? []
let formDataInit = {}
formItems.map((item) => {
  formDataInit[item.field] = ''
})
let formData = ref(formDataInit)

// 2.重置与搜索功能
// 重置按钮触发
const resetClick = () => {
  BuFormRef.value?.resetForm()
  emit('resetBtnClick')
}
// 搜索按钮触发
const searchClick = () => {
  // 这里需要遍历搜索配置项，配置项里可以传dataType，要求数据返回什么类型的数据
  let queryInfo = { ...formData.value }
  props.searchFormConfig.formItems.map((item) => {
    if (item.dataType === 'number' && queryInfo[item.field] !== '') {
      queryInfo[item.field] = Number(queryInfo[item.field])
    }
  })
  // 清空queryInfo中没有值的属性
  for (const i in queryInfo) {
    if (queryInfo[i] === '') {
      delete queryInfo[i]
    }
  }
  emit('searchBtnClick', queryInfo)
}
</script>

<style scoped>
.header {
  padding-top: 20px;
}
.footer {
  text-align: right;
  padding: 0 50px 20px 0;
}
</style>

```

### role页面组件文件

```vue
<template>
  <div class="role">
    <page-search
      :searchFormConfig="searchFormConfig"
      @resetBtnClick="handlerReset"
      @searchBtnClick="handlerSearch"
    ></page-search>
  </div>
</template>

<script setup lang="ts">
import { searchFormConfig } from './config/search-config-test'
import pageSearch from '@/components/page-search/page-search.vue'
const handlerReset = () => {
  console.log('点击了重置按钮')
}
const handlerSearch = (queryInfo: any) => {
  console.log('点击了搜索按钮，值为:', queryInfo)
}
</script>

<style scoped lang="less"></style>

```

## 结语

写了这么多，终于写完了，这里是属于Form表单部分的封装全部过程，能写到这其实我挺有成就感的哈哈哈哈，因为我刚学会的时候思路有了，但是敲出来有点困难，不过这个过程是我又捋了一遍，然后自己敲出来的，感觉其实也不难，已经掌握了这种封装思路与方法了哈哈，其他组件其实也可以利用这种思路，封装出来，在页面上的使用直接传配置项调用就完事，开发每个相似的页面效率简直是牛逼

# Table表格的封装

## 简述

再来折磨一遍，这里是table表单的封装，其实跟上面的差不多，有点小区别，会用到添加动态插槽，这里就不墨迹了，直接上用配置项封装前的正常使用

## 正常使用

user.vue

```vue
<template>
  <div class="user">
    <el-table style="width: 100%" :data="dataList" border>
      <!-- 1.传入showSelectColumn时展示的全选列 -->
      <template v-if="contentTableConfig.showSelectColumn">
        <el-table-column type="selection" />
      </template>
      <!-- 2.传入showIndexColumn时展示的序号列 -->
      <template v-if="contentTableConfig.showIndexColumn">
        <el-table-column type="index" label="序号" />
      </template>
      <!-- 3.propList里面的所有列 -->
      <template v-for="item in contentTableConfig.propList" :key="item.prop">
        <el-table-column v-bind="item" show-overflow-tooltip>
          <!-- 传有slotName时展示的插槽列 -->
          <template #default="scope" v-if="item.slotName">
            <template v-if="item.slotName === 'handler'">
              <el-button size="small" :icon="Edit" type="text">编辑</el-button>
              <el-button size="small" :icon="Delete" type="text"
                >删除</el-button
              >
            </template>
            <template v-if="item.slotName === 'status'">
              <el-button>{{
                scope.row.status === 0 ? '禁用' : '启用'
              }}</el-button>
            </template>
          </template>
        </el-table-column>
      </template>
    </el-table>
  </div>
</template>

<script setup lang="ts">
import { Delete, Edit } from '@element-plus/icons-vue'
import { useStore } from '@/store'
import { computed } from 'vue'
const store = useStore()
// 这里是网络请求数据
const getPageData = () => {
  store.dispatch(`main/getPageListAction`, {
    queryInfo: {
      offset: 0,
      size: 10
    },
    pageName: 'users'
  })
}
// 页面加载时第一次调用获取表单数据
getPageData()
const dataList = computed(() => store.getters[`main/pageListData`]('users'))

// 表格配置项
const contentTableConfig = {
  // 表格配置
  propList: [
    { prop: 'id', label: '用户id', minWidth: '100', align: 'center' },
    { prop: 'name', label: '用户名', minWidth: '100', align: 'center' },
    { prop: 'realname', label: '真实姓名', minWidth: '100', align: 'center' },
    { prop: 'cellphone', label: '手机号码', minWidth: '100', align: 'center' },
    {
      prop: 'enable',
      label: '状态',
      minWidth: '100',
      slotName: 'status',
      align: 'center'
    },
    {
      label: '操作',
      minWidth: '120',
      slotName: 'handler',
      align: 'center'
    }
  ],
  // 表格具有序号列
  showIndexColumn: true,
  // 表格具有可选列
  showSelectColumn: true
}
</script>

<style scoped lang="less"></style>
```

![image-20220506153259867](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Element%20Plus%E7%BB%84%E4%BB%B6%E4%BA%8C%E6%AC%A1%E5%B0%81%E8%A3%85/image-20220506153259867.png)

从上面可以看出，主页面的代码有多冗余，看到就头疼+裂开，所以开始一层封装

## 开始封装①

配置项类型文件

```typescript
export interface ITbaleOption {
  prop?: string
  label: string
  minWidth?: string
  slotName?: string
}
export interface ITable {
  propList: ITbaleOption[]
  showIndexColumn?: boolean
  showSelectColumn?: boolean
  childrenProps?: object
}
```

配置项文件

```typescript
import { ITable } from '@/base-ui/table/type'
export const contentTableConfig: ITable = {
  // 表格配置
  propList: [
    { prop: 'id', label: '用户id', minWidth: '100' },
    { prop: 'name', label: '用户名', minWidth: '100' },
    { prop: 'realname', label: '真实姓名', minWidth: '100' },
    { prop: 'cellphone', label: '手机号码', minWidth: '100' },
    { prop: 'enable', label: '状态', minWidth: '100', slotName: 'status' },
    {
      label: '操作',
      minWidth: '120',
      slotName: 'handler'
    }
  ],
  // 表格具有序号列
  showIndexColumn: false,
  // 表格具有可选列
  showSelectColumn: true
}
```

table.vue文件

```vue
<template>
  <el-table style="width: 100%" :data="listData" border>
    <!-- 1.传入showSelectColumn时展示的全选列 -->
    <template v-if="showSelectColumn">
      <el-table-column type="selection" />
    </template>
    <!-- 2.传入showIndexColumn时展示的序号列 -->
    <template v-if="showIndexColumn">
      <el-table-column type="index" label="序号" />
    </template>
    <!-- 3.propList里面的所有列 -->
    <template v-for="item in propList" :key="item.prop">
      <el-table-column v-bind="item" show-overflow-tooltip>
        <!-- 传有slotName时展示的插槽列 -->
        <template #default="scope" v-if="item.slotName">
          <template v-if="item.slotName === 'handler'">
            <el-button size="small" :icon="Edit" type="text">编辑</el-button>
            <el-button size="small" :icon="Delete" type="text">删除</el-button>
          </template>
          <template v-if="item.slotName === 'status'">
            <el-button>{{
              scope.row.status === 0 ? '禁用' : '启用'
            }}</el-button>
          </template>
        </template>
      </el-table-column>
    </template>
  </el-table>
</template>

<script setup lang="ts">
import { Delete, Edit } from '@element-plus/icons-vue'
import { defineProps, withDefaults, defineEmits } from 'vue'
import { ITbaleOption } from './type'
interface Props {
  listData: any[] //表单数据
  propList: ITbaleOption[] //表单配置项
  showIndexColumn?: boolean //是否显示索引列
  showSelectColumn?: boolean //是否显示全选列
  childrenProps?: object // 是否有子数据，树形数据才用得到
}
const props = withDefaults(defineProps<Props>(), {
  showIndexColumn: false,
  showSelectColumn: false,
  childrenProps: () => ({})
})
</script>

<style scoped></style>
```

user.vue

```vue
<template>
  <div class="user">
    <table-test v-bind="contentTableConfig" :listData="dataList"></table-test>
  </div>
</template>

<script setup lang="ts">
// 导入表单配置项
import { contentTableConfig } from './config/table-config'
import tableTest from '@/base-ui/table/table-test.vue'
import { useStore } from '@/store'
import { computed } from 'vue'
const store = useStore()

// 这里是网络请求数据
const getPageData = () => {
  store.dispatch(`main/getPageListAction`, {
    queryInfo: {
      offset: 0,
      size: 10
    },
    pageName: 'users'
  })
}
// 页面加载时第一次调用获取表单数据
getPageData()
const dataList = computed(() => store.getters[`main/pageListData`]('users'))
</script>

<style scoped lang="less"></style>
```

![image-20220506153312738](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Element%20Plus%E7%BB%84%E4%BB%B6%E4%BA%8C%E6%AC%A1%E5%B0%81%E8%A3%85/image-20220506153312738.png)

可以看到，代码抽出去了，但是我们可以发现，里面的插槽其实不能给它写死，应该是动态决定的，所以我们可以这样做

在拥有slotName部分的插槽列部分放入一个具名插槽，再将默认插槽列中的scope.row发回给具名插槽

table.vue（为了可扩展性，我依旧在里面加了两个插槽，一个顶部一个底部）

```vue
<template>
  <div class="header">
    <slot name="header"> </slot>
  </div>
  <el-table style="width: 100%" :data="listData" border>
    <!-- 1.传入showSelectColumn时展示的全选列 -->
    <template v-if="showSelectColumn">
      <el-table-column type="selection" />
    </template>
    <!-- 2.传入showIndexColumn时展示的序号列 -->
    <template v-if="showIndexColumn">
      <el-table-column type="index" label="序号" />
    </template>
    <!-- 3.propList里面的所有列 -->
    <template v-for="item in propList" :key="item.prop">
      <el-table-column v-bind="item" show-overflow-tooltip>
        <!-- 传有slotName时展示的插槽列 -->
        <template #default="scope" v-if="item.slotName">
          <slot :name="item.slotName" :row="scope.row"></slot>
        </template>
      </el-table-column>
    </template>
  </el-table>
  <div class="footer">
    <slot name="footer"> </slot>
  </div>
</template>

<script setup lang="ts">
import { defineProps, withDefaults, defineEmits } from 'vue'
import { ITbaleOption } from './type'
interface Props {
  listData: any[] //表单数据
  propList: ITbaleOption[] //表单配置项
  showIndexColumn?: boolean //是否显示索引列
  showSelectColumn?: boolean //是否显示全选列
  childrenProps?: object // 是否有子数据，树形数据才用得到
}
const props = withDefaults(defineProps<Props>(), {
  showIndexColumn: false,
  showSelectColumn: false,
  childrenProps: () => ({})
})
</script>

<style scoped></style>

```

然后在user.vue中放入具名插槽，传进去table组件里，同时传入一些内容到可扩展插槽里面

user.vue

```vue
<template>
  <div class="user">
    <div class="content">
      <table-test v-bind="contentTableConfig" :listData="dataList">
        <!-- 1.header中的插槽 -->
        <template #header>
          <div class="header-default">
            <!-- 传入标题（位于左侧） -->
            <div class="title">用户列表</div>
            <!-- 传入处理内容（位于右侧） -->
            <div class="handler">
              <el-button type="primary" @click="addUserBtnClick"
                >新建用户</el-button
              >
            </div>
          </div>
        </template>
        <!-- 2.该user页面独有部分 -->
        <template #status="scope">
          <el-button>{{ scope.row.status === 0 ? '禁用' : '启用' }}</el-button>
        </template>
        <!-- 3.每个页面都会有的部分 -->
        <template #handler="scope">
          <el-button
            size="small"
            :icon="Edit"
            type="text"
            @click="handleEditClick(scope.row)"
            >编辑</el-button
          >
          <el-button
            size="small"
            :icon="Delete"
            type="text"
            @click="deleteBtnClick(scope.row)"
            >删除</el-button
          >
        </template>
        <!-- 4.footer插槽 -->
        <template #footer>
          <!-- 只有总条数>0才会有分页器 -->
          <div class="footer-default">
            <el-pagination
              :page-sizes="[100, 200, 300, 400]"
              layout="total, sizes, prev, pager, next, jumper"
              :total="400"
            />
          </div>
        </template>
      </table-test>
    </div>
  </div>
</template>

<script setup lang="ts">
// 导入表单配置项
import { contentTableConfig } from './config/table-config'
import { Delete, Edit } from '@element-plus/icons-vue'
import tableTest from '@/base-ui/table/table-test.vue'
import { useStore } from '@/store'
import { computed } from 'vue'
const store = useStore()

// 这里是网络请求数据
const getPageData = () => {
  store.dispatch(`main/getPageListAction`, {
    queryInfo: {
      offset: 0,
      size: 10
    },
    pageName: 'users'
  })
}
// 页面加载时第一次调用获取表单数据
getPageData()
const dataList = computed(() => store.getters[`main/pageListData`]('users'))

// 点击编辑按钮触发事件
const handleEditClick = (row: any) => {
  console.log('点击了编辑按钮，数据为：', row)
}
// 点击删除按钮触发事件
const deleteBtnClick = (row: any) => {
  console.log('点击了删除按钮，数据为：', row)
}
// 点击新建用户触发事件
const addUserBtnClick = () => {
  console.log('点击了新建用户')
}
</script>

<style scoped lang="less">
.content {
  border-top: 20px solid #dedee1;
  padding: 40px;
}
.header-default {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  .title {
    font-size: 22px;
    font-weight: 700;
  }
}
.footer-default {
  display: flex;
  justify-content: flex-end;
  margin-top: 20px;
}
</style>

```

![image-20220506153326944](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Element%20Plus%E7%BB%84%E4%BB%B6%E4%BA%8C%E6%AC%A1%E5%B0%81%E8%A3%85/image-20220506153326944.png)

显然此时封装已经满足需求了，但是我们发现主页面的代码页还是比较冗余，如果不用到插槽的话还好，要用到插槽的话，就要在主页面写入很多插槽，多个页面都这样的话页裂开，所以我们要像之前一样把这坨代码再封一层

## 开始封装②

page-table.vue

```vue
<template>
  <table-test v-bind="contentTableConfig" :listData="dataList">
    <!-- 1.header中的插槽 -->
    <template #header>
      <div class="header-default">
        <!-- 传入标题（位于左侧） -->
        <div class="title">{{ pageNameInChinese }}列表</div>
        <!-- 传入处理内容（位于右侧） -->
        <div class="handler">
          <el-button type="primary" @click="addUserBtnClick"
            >新建{{ pageNameInChinese }}</el-button
          >
        </div>
      </div>
    </template>
    <!-- 2.该user页面独有部分 -->
    <template #status="scope">
      <el-button>{{ scope.row.status === 0 ? '禁用' : '启用' }}</el-button>
    </template>
    <!-- 3.每个页面都会有的部分 -->
    <template #handler="scope">
      <el-button
        size="small"
        :icon="Edit"
        type="text"
        @click="handleEditClick(scope.row)"
        >编辑</el-button
      >
      <el-button
        size="small"
        :icon="Delete"
        type="text"
        @click="deleteBtnClick(scope.row)"
        >删除</el-button
      >
    </template>
    <!-- 4.footer插槽 -->
    <template #footer>
      <!-- 只有总条数>0才会有分页器 -->
      <div class="footer-default">
        <el-pagination
          :page-sizes="[100, 200, 300, 400]"
          layout="total, sizes, prev, pager, next, jumper"
          :total="400"
        />
      </div>
    </template>
  </table-test>
</template>

<script setup lang="ts">
import { Delete, Edit } from '@element-plus/icons-vue'
import tableTest from '@/base-ui/table/table-test.vue'
import type { ITable } from '@/base-ui/table/type'

import { useStore } from '@/store'
import { defineProps, computed } from 'vue'

// 定义属性
interface Props {
  contentTableConfig: ITable //表单配置接收
  pageName: string //表单名字接收
}
const props = defineProps<Props>()

const store = useStore()

// 这里是网络请求数据
const getPageData = () => {
  store.dispatch(`main/getPageListAction`, {
    queryInfo: {
      offset: 0,
      size: 10
    },
    pageName: props.pageName
  })
}
// 页面加载时第一次调用获取表单数据
getPageData()
const dataList = computed(() =>
  store.getters[`main/pageListData`](props.pageName)
)

// 1.获取页面中文名称
const pageNameTransform = (pageName: string): string => {
  switch (pageName) {
    case 'users':
      return '用户'
    default:
      return ''
  }
}
const pageNameInChinese = pageNameTransform(props.pageName)

// 点击编辑按钮触发事件
const handleEditClick = (row: any) => {
  console.log('点击了编辑按钮，数据为：', row)
}
// 点击删除按钮触发事件
const deleteBtnClick = (row: any) => {
  console.log('点击了删除按钮，数据为：', row)
}
// 点击新建用户触发事件
const addUserBtnClick = () => {
  console.log('点击了新建用户')
}
</script>

<style scoped lang="less">
.header-default {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  .title {
    font-size: 22px;
    font-weight: 700;
  }
}
.footer-default {
  display: flex;
  justify-content: flex-end;
  margin-top: 20px;
}
</style>
```

user.vue

```vue
<template>
  <div class="user">
    <div class="content">
      <page-table
        :contentTableConfig="contentTableConfig"
        pageName="users"
      ></page-table>
    </div>
  </div>
</template>

<script setup lang="ts">
// 导入表单配置项
import { contentTableConfig } from './config/table-config'
import pageTable from '@/components/page-table/page-table-test.vue'
</script>

<style scoped lang="less">
.content {
  border-top: 20px solid #dedee1;
  padding: 40px;
}
</style>

```

![image-20220506153337789](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Element%20Plus%E7%BB%84%E4%BB%B6%E4%BA%8C%E6%AC%A1%E5%B0%81%E8%A3%85/image-20220506153337789.png)

图中可以看出效果其实是一样的，主页面的代码少了，只需要传入配置项和pageName（控制网络请求数据）就可以生成一个表格，但是我们可以发现，如果多个页面复用的话，其实操作列的插槽是公有的，但是状态列却是私有的，别的页面不一定有状态页，所以状态列内容插入的位置应该在主页面user里面而不该在封装的组件里面，而且编辑新建逻辑也是页面独有，应该在主页面执行

## 开始封装③

user.vue

```vue
<template>
  <div class="user">
    <div class="content">
      <page-table
        :contentTableConfig="contentTableConfig"
        pageName="users"
        @editBtnClick="handleEditClick"
        @createBtnClick="handleCreateClick"
      >
        <template #status="scope">
          <el-button>{{ scope.row.status === 0 ? '禁用' : '启用' }}</el-button>
        </template>
      </page-table>
    </div>
  </div>
</template>

<script setup lang="ts">
// 导入表单配置项
import { contentTableConfig } from './config/table-config'
import pageTable from '@/components/page-table/page-table-test.vue'

const handleEditClick = (row: any, pageNameInChinese: any) => {
  console.log('点击了编辑按钮，数据为：', row, pageNameInChinese)
}

const handleCreateClick = (pageNameInChinese: any) => {
  console.log('点击了新建用户，数据为：', pageNameInChinese)
}
</script>

<style scoped lang="less">
.content {
  border-top: 20px solid #dedee1;
  padding: 40px;
}
</style>

```

page-table.vue

```vue
<template>
  <table-test v-bind="contentTableConfig" :listData="dataList">
    <!-- 1.header中的插槽 -->
    <template #header>
      <div class="header-default">
        <!-- 传入标题（位于左侧） -->
        <div class="title">{{ pageNameInChinese }}列表</div>
        <!-- 传入处理内容（位于右侧） -->
        <div class="handler">
          <el-button type="primary" @click="addUserBtnClick"
            >新建{{ pageNameInChinese }}</el-button
          >
        </div>
      </div>
    </template>
    <!-- 2.该user页面独有部分 -->
    <template
      v-for="item in otherPropSlots"
      :key="item.prop"
      #[item.slotName]="scope"
    >
      <template v-if="item.slotName">
        <slot :name="item.slotName" :row="scope.row"></slot>
      </template>
    </template>
    <!-- 3.每个页面都会有的部分 -->
    <template #handler="scope">
      <el-button
        size="small"
        :icon="Edit"
        type="text"
        @click="handleEditClick(scope.row)"
        >编辑</el-button
      >
      <el-button
        size="small"
        :icon="Delete"
        type="text"
        @click="deleteBtnClick(scope.row)"
        >删除</el-button
      >
    </template>
    <!-- 4.footer插槽 -->
    <template #footer>
      <!-- 只有总条数>0才会有分页器 -->
      <div class="footer-default">
        <el-pagination
          :page-sizes="[100, 200, 300, 400]"
          layout="total, sizes, prev, pager, next, jumper"
          :total="400"
        />
      </div>
    </template>
  </table-test>
</template>

<script setup lang="ts">
import { Delete, Edit } from '@element-plus/icons-vue'
import tableTest from '@/base-ui/table/table-test.vue'
import type { ITable } from '@/base-ui/table/type'

import { useStore } from '@/store'
import { defineProps, computed, defineEmits } from 'vue'

// 定义属性
interface Props {
  contentTableConfig: ITable //表单配置接收
  pageName: string //表单名字接收
}
const props = defineProps<Props>()
const emit = defineEmits<{
  (e: 'createBtnClick', pageNameInChinese: string): void
  (e: 'editBtnClick', rowData: any, pageNameInChinese: string): void
}>()

const store = useStore()

// 这里是网络请求数据
const getPageData = () => {
  store.dispatch(`main/getPageListAction`, {
    queryInfo: {
      offset: 0,
      size: 10
    },
    pageName: props.pageName
  })
}
// 页面加载时第一次调用获取表单数据
getPageData()
const dataList = computed(() =>
  store.getters[`main/pageListData`](props.pageName)
)

// 1.获取页面中文名称
const pageNameTransform = (pageName: string): string => {
  switch (pageName) {
    case 'users':
      return '用户'
    default:
      return ''
  }
}
const pageNameInChinese = pageNameTransform(props.pageName)

// 2.属于动态插槽的配置项筛选
const otherPropSlots: any = props.contentTableConfig?.propList.filter(
  (item: any) => {
    if (item.slotName === 'handler') return false
    return item.slotName
  }
)

// 点击编辑按钮触发事件
const handleEditClick = (row: any) => {
  emit('editBtnClick', row, pageNameInChinese)
}
// 点击删除按钮触发事件
const deleteBtnClick = (row: any) => {
  console.log('点击了删除按钮，数据为：', row)
}
// 点击新建用户触发事件
const addUserBtnClick = () => {
  emit('createBtnClick', pageNameInChinese)
}
</script>

<style scoped lang="less">
.header-default {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  .title {
    font-size: 22px;
    font-weight: 700;
  }
}
.footer-default {
  display: flex;
  justify-content: flex-end;
  margin-top: 20px;
}
</style>

```

![image-20220506153348571](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Element%20Plus%E7%BB%84%E4%BB%B6%E4%BA%8C%E6%AC%A1%E5%B0%81%E8%A3%85/image-20220506153348571.png)



可以看到，这时我们已经把独有私有的插槽区分开了，而且编辑和新建的逻辑也在主页面中执行，封装完毕，下面附上完整代码

## 完整封装代码④

### 配置项类型文件

```typescript
export interface ITbaleOption {
  prop?: string
  label: string
  minWidth?: string
  slotName?: string
  align?: string
}
export interface ITable {
  propList: ITbaleOption[]
  showIndexColumn?: boolean
  showSelectColumn?: boolean
  childrenProps?: object
}

```

### 配置项文件

```typescript
import { ITable } from '@/base-ui/table/type'
export const contentTableConfig: ITable = {
  // 表格配置
  propList: [
    { prop: 'id', label: '用户id', minWidth: '100', align: 'center' },
    { prop: 'name', label: '用户名', minWidth: '100', align: 'center' },
    { prop: 'realname', label: '真实姓名', minWidth: '100', align: 'center' },
    { prop: 'cellphone', label: '手机号码', minWidth: '100', align: 'center' },
    {
      prop: 'enable',
      label: '状态',
      minWidth: '100',
      slotName: 'status',
      align: 'center'
    },
    {
      label: '操作',
      minWidth: '120',
      slotName: 'handler',
      align: 'center'
    }
  ],
  // 表格具有序号列
  showIndexColumn: false,
  // 表格具有可选列
  showSelectColumn: true
}

```

### table表单组件文件

```vue
<template>
  <div class="header">
    <slot name="header"> </slot>
  </div>
  <el-table style="width: 100%" :data="listData" border>
    <!-- 1.传入showSelectColumn时展示的全选列 -->
    <template v-if="showSelectColumn">
      <el-table-column type="selection" />
    </template>
    <!-- 2.传入showIndexColumn时展示的序号列 -->
    <template v-if="showIndexColumn">
      <el-table-column type="index" label="序号" />
    </template>
    <!-- 3.propList里面的所有列 -->
    <template v-for="item in propList" :key="item.prop">
      <el-table-column v-bind="item" show-overflow-tooltip>
        <!-- 传有slotName时展示的插槽列 -->
        <template #default="scope" v-if="item.slotName">
          <slot :name="item.slotName" :row="scope.row"></slot>
        </template>
      </el-table-column>
    </template>
  </el-table>
  <div class="footer">
    <slot name="footer"> </slot>
  </div>
</template>

<script setup lang="ts">
import { defineProps, withDefaults, defineEmits } from 'vue'
import { ITbaleOption } from './type'
interface Props {
  listData: any[] //表单数据
  propList: ITbaleOption[] //表单配置项
  showIndexColumn?: boolean //是否显示索引列
  showSelectColumn?: boolean //是否显示全选列
  childrenProps?: object // 是否有子数据，树形数据才用得到
}
const props = withDefaults(defineProps<Props>(), {
  showIndexColumn: false,
  showSelectColumn: false,
  childrenProps: () => ({})
})
</script>

<style scoped></style>

```

### page-table组件文件

### user页面组件文件

```vue
<template>
  <div class="user">
    <div class="content">
      <page-table
        :contentTableConfig="contentTableConfig"
        pageName="users"
        @editBtnClick="handleEditClick"
        @createBtnClick="handleCreateClick"
      >
        <template #status="scope">
          <el-button>{{ scope.row.status === 0 ? '禁用' : '启用' }}</el-button>
        </template>
      </page-table>
    </div>
  </div>
</template>

<script setup lang="ts">
// 导入表单配置项
import { contentTableConfig } from './config/table-config'
import pageTable from '@/components/page-table/page-table-test.vue'

const handleEditClick = (row: any, pageNameInChinese: any) => {
  console.log('点击了编辑按钮，数据为：', row, pageNameInChinese)
}

const handleCreateClick = (pageNameInChinese: any) => {
  console.log('点击了新建用户，数据为：', pageNameInChinese)
}
</script>

<style scoped lang="less">
.content {
  border-top: 20px solid #dedee1;
  padding: 40px;
}
</style>

```

# 结语

刚学会的代码思路与写法，花了一天整理，主要还是自己记录一下，颇有收获，感觉大佬分享使我进步~虽然还是很菜....