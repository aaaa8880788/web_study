# Vue3项目中开发阶段遇到跨域

可以在vue.config.json中配置

```
devServer: {
  proxy:{
    '^/api':{
      target:'http://XX.XX.XX.XX:XXXX',
      pathRewrite:{
        '^api':''
      },
      changeOrigin:true
    }
  }
}
```

```
module.exports = defineConfig({
  transpileDependencies: true,
  outputDir: './build',
  // 位置放这里,配置好后需要去把baseURL设置成 BASE_URL = '/api',原本是BASE_URL = 'http://XX.XX.XX.XX:XXXX'
  devServer: {
    proxy:{
     '^/api':{
      target:'http://XX.XX.XX.XX:XXXX',
      pathRewrite:{
        '^api':''
      },
      changeOrigin:true
    }
  }
}
})

```

