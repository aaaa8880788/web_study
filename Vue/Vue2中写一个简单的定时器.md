# 在vue中写一个简单的定时器

```
<template>
  <div>
	<span>{{ countDownMM }}:{{ countDownSS }}</span>
  </div>
</template>

<script>
export default {
  data() {
    return {
      countDownM: 14,		//分
      countDownS: 59,		//秒
      timer: "",
    };
  },
  computed: {
  	//返回补零后的分秒
    countDownMM() {
      return this.num(this.countDownM);
    },
    countDownSS() {
      return this.num(this.countDownS);
    },
  },
  methods: {
    // 单数补零操作
    num(n) {
      return n < 10 ? "0" + n : "" + n;
    },
    // 定时操作
    add() {
      this.timer = window.setInterval(() => {
        if (this.countDownS === 0 && this.countDownM !== 0) {
          this.countDownS = 59;
          this.countDownM -= 1;
        } else if (this.countDownS === 0 && this.countDownM === 0) {
          window.clearInterval(this.timer);
          // 这里加定时介绍后要做的事
          this.$router.back();
        } else {
          this.countDownS -= 1;
        }
      }, 1000);
    },
  },
  created() {
    this.timer && clearInterval(this.timer);
    // 开始计时器
    this.add();
  },
  beforeDestroy() {
    // 在页面销毁前清除定时器,因为定时器有两种情况要清除，一种是刚进页面，一种是手动离开页面
    this.timer && clearInterval(this.timer);
  },
};
</script>

```

