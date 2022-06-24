# Vue2.0中路由切换页面动画

## 在App.vue中页面中

### 第一步：

用transition标签将router-view标签包裹住

```
<transition :name="transitionName">
      <router-view></router-view>
</transition>
```

### 第二步

在script标签中的data中定义变量transitionName，并且加上watch监听，这里的watch可以自己魔改

```
<script>
export default {
  name: "App",
  data() {
    return {
      transitionName: "",
    };
  },
  //监听路由的路径，可以通过不同的路径去选择不同的切换效果
  watch: {
    //使用watch 监听$router的变化
    $route(to, from) {
      //如果to索引大于from索引,判断为前进状态,反之则为后退状态
      console.log(to, "to");
      console.log(from, "from");
      if (to.meta.index > from.meta.index) {
        //设置动画名称
        this.transitionName = "slide-left";
      } else {
        this.transitionName = "slide-right";
      }
    },
  },
};
</script>
```

### 第三步

在css中添加代码，目前测试fiexd的效果比较好

```
.slide-right-enter-active,
.slide-right-leave-active,
.slide-left-enter-active,
.slide-left-leave-active {
  will-change: transform;
  transition: all 500ms;
  position: fixed;
}

.slide-right-enter {
  opacity: 0;
  transform: translate3d(-100%, 0, 0);
}

.slide-right-leave-active {
  opacity: 0;
  transform: translate3d(100%, 0, 0);
}

.slide-left-enter {
  opacity: 0;
  transform: translate3d(100%, 0, 0);
}

.slide-left-leave-active {
  opacity: 0;
  transform: translate3d(-100%, 0, 0);
}
```

### 第四步

在路由中给meta加上index熟悉，值自己给

```
// 首页
  {
    path: '/home',
    name: 'home',
    component: () => import('../views/Home/Home.vue'),
    meta: {
      index: 1
    }
  },
  // 个人中心页
  {
    path: '/mine',
    name: 'mine',
    component: () => import('../views/Mine/Mine.vue'),
    meta: {
      index:2
    },
  },
```

