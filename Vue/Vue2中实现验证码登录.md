# 在Vue中实现验证码登录

## 效果展示

![image-20220506180858203](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Vue/Vue2%E4%B8%AD%E5%AE%9E%E7%8E%B0%E9%AA%8C%E8%AF%81%E7%A0%81%E7%99%BB%E5%BD%95/image-20220506180858203.png)

## 第一步：创建验证码组件

这里是组件的代码，可以自行命名文件名，我这里命名为SIdentify.vue

```
<template>
    <div class="s-canvas">
        <canvas id="s-canvas" :width="contentWidth" :height="contentHeight"></canvas>
    </div>
</template>
 
<script>
    export default {
        name: "SIdentify",
        props: {
            identifyCode: {
                type: String,
                default: '1234'
            },
            fontSizeMin: {
                type: Number,
                default: 25
            },
            fontSizeMax: {
                type: Number,
                default: 30
            },
            backgroundColorMin: {
                type: Number,
                default: 255
            },
            backgroundColorMax: {
                type: Number,
                default: 255
            },
            colorMin: {
                type: Number,
                default: 0
            },
            colorMax: {
                type: Number,
                default: 160
            },
            lineColorMin: {
                type: Number,
                default: 100
            },lineColorMax: {
                type: Number,
                default: 255
            },
            dotColorMin: {
                type: Number,
                default: 0
            },
            dotColorMax: {
                type: Number,
                default: 255
            },
            contentWidth: {
                type: Number,
                default: 112
            },
            contentHeight: {
                type: Number,
                default: 31
            }
        },
        methods: {
            // 生成一个随机数
            randomNum(min, max) {
                return Math.floor(Math.random() * (max - min) + min)
            },
            // 生成一个随机的颜色
            randomColor(min, max) {
                let r = this.randomNum(min, max)
                let g = this.randomNum(min, max)
                let b = this.randomNum(min, max)
                return 'rgb(' + r + ',' + g + ',' + b + ')'
            },
            drawPic() {
                let canvas = document.getElementById('s-canvas')
                let ctx = canvas.getContext('2d')
                ctx.textBaseline = 'bottom'
                // 绘制背景
                ctx.fillStyle = this.randomColor(this.backgroundColorMin, this.backgroundColorMax)
                ctx.fillRect(0, 0, this.contentWidth, this.contentHeight)
                // 绘制文字
                for (let i = 0; i < this.identifyCode.length; i++) {
                    this.drawText(ctx, this.identifyCode[i], i)
                }
                this.drawLine(ctx)
                this.drawDot(ctx)
            },
            drawText(ctx, txt, i) {
                ctx.fillStyle = this.randomColor(this.colorMin, this.colorMax)
                ctx.font = this.randomNum(this.fontSizeMin, this.fontSizeMax) + 'px SimHei'
                let x = (i + 1) * (this.contentWidth / (this.identifyCode.length + 1))
                let y = this.randomNum(this.fontSizeMax, this.contentHeight - 5)
                var deg = this.randomNum(-45, 45)
                // 修改坐标原点和旋转角度
                ctx.translate(x, y)
                ctx.rotate(deg * Math.PI / 180)
                ctx.fillText(txt, 0, 0)
                // 恢复坐标原点和旋转角度
                ctx.rotate(-deg * Math.PI / 180)
                ctx.translate(-x, -y)
            },
            drawLine(ctx) {
                // 绘制干扰线
                for (let i = 0; i < 5; i++) {
                    ctx.strokeStyle = this.randomColor(this.lineColorMin, this.lineColorMax)
                    ctx.beginPath()
                    ctx.moveTo(this.randomNum(0, this.contentWidth), this.randomNum(0, this.contentHeight))
                    ctx.lineTo(this.randomNum(0, this.contentWidth), this.randomNum(0, this.contentHeight))
                    ctx.stroke()
                }
            },
            drawDot(ctx) {
                // 绘制干扰点
                for (let i = 0; i < 80; i++) {
                    ctx.fillStyle = this.randomColor(0, 255)
                    ctx.beginPath()
                    ctx.arc(this.randomNum(0, this.contentWidth), this.randomNum(0, this.contentHeight), 1, 0, 2 * Math.PI)
                    ctx.fill()
                }
            }
        },
        watch: {
            identifyCode() {
                this.drawPic()
            }
        },
        mounted() {
            this.drawPic()
        }
    }
</script>
 
<style scoped>
    .s-canvas {
        height: 38px;
 
    }
    .s-canvas canvas{
        margin-top: 1px;
        margin-left: 8px;
    }
</style>
```



## 第二步：引入验证码组件并注册使用

这里data里面的identifyCode的值就是所生成的验证码的值，可以通过该码自行发挥逻辑操作，SIdentify组件其实只是为了显示验证码图片而已，所以不用该组件还可以做成伪手机验证码登录

```
<template>
  <div>
  	<SIdentify:identifyCode="identifyCode" >
    </SIdentify>
  </div>
</template>
// 引入登录验证组件
import SIdentify from "@/components/SIdentify";
<script>
    export default {
        name: "SIdentify",
        components: { SIdentify },
        data(){
        	return {
        		identifyCode: "", //密码登录图形验证码
      			identifyCodes: "1234567890abcdefghizklmnopqrstuvwxyz", //生成图形验证码的依据
        	}
        },
        methods:{
        	// 刷新验证码
    		refreshIdentifyCode() {
      			this.identifyCode = "";
      			this.makeIdentifyCode(4);
    		},
    		// 生成4位数的随机验证码
    		makeIdentifyCode(l) {
      			for (let i = 0; i < l; i++) {
        			this.identifyCode +=
          			this.identifyCodes[this.randomNum(0, this.identifyCodes.length)];
      			}
    		},
    		// 生成单个验证码
    		randomNum(min, max) {
      			return Math.floor(Math.random() * (max - min) + min);
    		},
        },
        mounted() {
    		// 初始化验证码
    		this.identifyCode = "";
    		this.makeIdentifyCode(4);
  		},
    }
</script>
```

