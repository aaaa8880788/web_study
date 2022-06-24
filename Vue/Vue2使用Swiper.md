# Swiper在Vue中的使用

## 第一步，安装

```
npm i swiper@4.5.0
npm i vue-awesome-swiper@3.1.3
```

## 第二部，全局引入

```
// 全局引用并使用轮播图组件
import VueAwesomeSwiper from 'vue-awesome-swiper'
import 'swiper/dist/css/swiper.css'
Vue.use(VueAwesomeSwiper, /* { default options with global component } */)
```

## 第三步，使用

```
// 配置项参考文档4
<swiper ref="mySwiper" class="swiper" :options="options" v-if="Image.length">
    <swiper-slide>1</swiper-slide>
    <swiper-slide>2</swiper-slide>
    <swiper-slide>3</swiper-slide>
    // 分页器，注意一定要加slot
    <div class="swiper-pagination" slot="pagination"></div>
</swiper>

<script>
export default {
  data() {
    return {
      //配置项例子
      options: {
        loop: true,
        autoplay: {
          //swiper手动滑动之后自动轮播失效的解决方法,包括触碰，拖动，点击pagination,重新启动自动播放
          disableOnInteraction: false,
          // 自动播放时间：毫秒
          delay: 2000,
        },
        pagination: {
          el: ".swiper-pagination",
          clickable: true,
          // bulletClass: "my-bullet",
        },
      },
    };
  },
};

```



以上是基本使用步骤，记住版本号不能不同，不同的版本号对应着不同的使用方法，而且连引入方法都不同，这是最坑爹的