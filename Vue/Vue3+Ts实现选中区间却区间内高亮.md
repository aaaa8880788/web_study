# 该案例的实现思路

每个盒子都有自己的索引值，在你选中一个盒子之后，会在数组a中记录这个索引值，而这时我们需要定义一个盒子移入就触发的事件，在选中一个盒子的条件下（也就是数组a的length为1时）将你移入的那个盒子的索引值记录到数组b中，这时数组a和数组b中都有一个索引值，我们只需要将在那些索引值在这两个索引值之间的盒子高亮一个颜色就可以了，而数组a里面的索引值所对应的盒子设置为另一个高亮颜色，就可实现

# 案例演示

![image-20220506155720417](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Vue3+Ts%E5%AE%9E%E7%8E%B0%E9%80%89%E4%B8%AD%E5%8C%BA%E9%97%B4%E5%8D%B4%E5%8C%BA%E9%97%B4%E5%86%85%E9%AB%98%E4%BA%AE/image-20220506155720417.png)

在点击某个格子后会高亮一个盒子，然后可以在盒子范围内选中第二个盒子即可获取选中区域

![image-20220506155733632](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Vue3+Ts%E5%AE%9E%E7%8E%B0%E9%80%89%E4%B8%AD%E5%8C%BA%E9%97%B4%E5%8D%B4%E5%8C%BA%E9%97%B4%E5%86%85%E9%AB%98%E4%BA%AE/image-20220506155733632.png)

![image-20220506155742316](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Vue3+Ts%E5%AE%9E%E7%8E%B0%E9%80%89%E4%B8%AD%E5%8C%BA%E9%97%B4%E5%8D%B4%E5%8C%BA%E9%97%B4%E5%86%85%E9%AB%98%E4%BA%AE/image-20220506155742316.png)

# 实现代码

```vue
<template>
  <div>
    <div class="VueTimeSlot">
      <div class="VueTimeSlotList">
        <div
          class="VueTimeSlotItems"
          v-for="(item, index) in times"
          :key="index"
        >
          {{ getItemsSection(index) }}
          <div
            class="VueTimeSlotBox"
            :class="{
              VueTimeSlotBoxSelect: BlueBox.includes(index),
              VueTimeSlotBoxItems: getItemsSection(index)
            }"
            @mouseover="ItmeListOnMouseover(index)"
            @click="ItmeListOnclick(index)"
          ></div>
          {{ index }}
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
const times = ref<number[]>([0, 0, 0, 0, 0, 0, 0, 0, 0, 0])

const BlueBox = ref<number[]>([])
const TimeItems = ref<number[]>([])
const ItmeListOnclick = (id: number) => {
  BlueBox.value.push(id)
  if (BlueBox.value.length > 2) {
    BlueBox.value = []
    TimeItems.value = []
    BlueBox.value.push(id)
  }
}

const ItmeListOnMouseover = (id: number) => {
  if (BlueBox.value.length == 1) {
    TimeItems.value[0] = id
  }
}

const getItemsSection = (id: number) => {
  let flag = false
  if (id > BlueBox.value[0]) {
    flag = id > BlueBox.value[0] && id < TimeItems.value[0]
  } else {
    flag = id < BlueBox.value[0] && id > TimeItems.value[0]
  }
  return flag
}
</script>

<style lang="scss" scoped>
.VueTimeSlot {
  .VueTimeSlotList {
    display: flex;
    .VueTimeSlotItems {
      // background-color: red;
      display: inline-block;
      padding: 30px 0px;
      &:last-child {
        .VueTimeSlotBox {
          border-right: 1px #ccc solid;
        }
      }
      .VueTimeSlotBox {
        width: 50px;
        height: 20px;
        border: 1px #ccc solid;
        border-right: none;
        cursor: pointer;
      }
      .VueTimeSlotBoxSelect {
        background-color: rgb(50, 150, 250) !important;
      }
      .VueTimeSlotBoxItems {
        background-color: rgba(50, 150, 250, 0.3);
      }
    }
  }
}
</style>
```

