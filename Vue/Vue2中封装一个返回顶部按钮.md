# 在Vue中封装一个返回顶部按钮

## 效果图：

![image-20220506191711218](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Vue2%E4%B8%AD%E5%B0%81%E8%A3%9D%E4%B8%80%E5%80%8B%E8%BF%94%E5%9B%9E%E9%A0%82%E9%83%A8%E6%8C%89%E9%88%95/image-20220506191711218.png)

## 第一步：封装返回顶部按钮BackTop.vue

我这里因为用了vant，为了方便直接用vant的图标了，可以自行更换

```
<template>
  <div class="back-top">
    <!-- 返回顶部按钮 -->
    <van-icon
      v-if="btnFlag"
      name="back-top"
      @click="backTop"
      class="back-btn"
      ref="box"
    />
  </div>
</template>

<script>
export default {
  name: "BackTop",
  data() {
    return {
      btnFlag: false, //按钮的显示隐藏状态
      scrollTop: 0, //距离顶部的位置
    };
  },
  methods: {
    // 点击图片回到顶部方法，加计时器是为了过渡顺滑
    backTop() {
      let timer = setInterval(() => {
        let ispeed = Math.floor(-this.scrollTop / 5);
        document.documentElement.scrollTop = document.body.scrollTop =
          this.scrollTop + ispeed;
        if (this.scrollTop === 0) {
          clearInterval(timer);
        }
      }, 16);
    },

    // 为了计算距离顶部的高度，当高度大于60显示回顶部图标，小于60则隐藏
    scrollToTop() {
      let scrollTop =
        window.pageYOffset ||
        document.documentElement.scrollTop ||
        document.body.scrollTop;
      this.scrollTop = scrollTop;
      if (this.scrollTop > 200) {
        this.btnFlag = true;
      } else {
        this.btnFlag = false;
      }
    },
  },
  mounted() {
    window.addEventListener("scroll", this.scrollToTop, true);
  },
  destroyed() {
    window.removeEventListener("scroll", this.scrollToTop);
  },
};
</script>

<style lang="less" scoped>
.back-btn {
  background-color: rgba(178, 178, 178, 0.7);
  border-radius: 100%;
  padding: 0.3846rem;
  color: #fff;
  position: fixed;
  top: 60%;
  right: 5%;
  font-size: 2.3077rem;
}
</style>
```

## 第二步，直接在其他组件中引入注册使用

```
<template>
  <div>
    <BackTop> </BackTop>
  </div>
</template>

<script>
import BackTop from "@/components/BackTop";
export default {
  components: {
    BackTop,
  },
};
</script>

<style>
</style>
```

## 注意

在引用的页面，不能有overflow：auto

因为这样就计算不到距离顶部的高度了，最好在全局样式给body和html加上overflow：auto，然后引用的页面不加，就可以了

