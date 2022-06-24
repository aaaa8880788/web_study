# 服务器导入数据库文件

# Mongdb的导入

## 步骤一：本地导出数据库文件

在某个地方打开终端，输入以下命令，我这里以项目文件夹为例

![image-20220506152513057](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Server-deploy/%E5%B0%86%E6%9C%AC%E5%9C%B0mongodb%E6%95%B0%E6%8D%AE%E5%BA%93%E5%AF%BC%E5%85%A5%E5%88%B0%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%AD/image-20220506152513057.png)

-d 后面接数据库名，回车后

```shell
mongodump -d node-vue-moba
```

出现该文件夹证明导入成功

![image-20220506152546359](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Server-deploy/%E5%B0%86%E6%9C%AC%E5%9C%B0mongodb%E6%95%B0%E6%8D%AE%E5%BA%93%E5%AF%BC%E5%85%A5%E5%88%B0%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%AD/image-20220506152546359.png)

## 步骤二：服务器导入数据库文件

可以在vscode装该插件连接服务器(百度有教程)

![image-20220506152601570](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Server-deploy/%E5%B0%86%E6%9C%AC%E5%9C%B0mongodb%E6%95%B0%E6%8D%AE%E5%BA%93%E5%AF%BC%E5%85%A5%E5%88%B0%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%AD/image-20220506152601570.png)

连接成功后



将导出的数据库文件丢进去服务器

![image-20220506152613978](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Server-deploy/%E5%B0%86%E6%9C%AC%E5%9C%B0mongodb%E6%95%B0%E6%8D%AE%E5%BA%93%E5%AF%BC%E5%85%A5%E5%88%B0%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%AD/image-20220506152613978.png)



## 步骤三：宝塔安装并配置mongodb

1.在宝塔的软件商店中安装mongodb

2.修改mongodb配置

`bindIp` 由127.0.0.1改为0.0.0.0，放开ip限制\

3.宝塔放开 27017 端口

4.阿里云服务器，网络与安全-安全组-配置规则，放开 27017 端口

5. 配置用户名密码

1. 通过宝塔终端链接mongodb

   ```awk
   cd /www/server/mongodb/bin
   mongo
   ```

2. 切换到admin数据库，设置管理员账号密码

   ```reasonml
   use admin 
   db.createUser({user:'root',pwd:'123456',roles:['root']})
   ```

   验证是否添加成功，db.auth(用户名，用户密码)

   ```1c
   db.auth('root', '123456')
   ```

## 步骤四：导入数据库

在终端直接执行执行命令mongorestore

```
mongorestore
```

# Mysql的导入

直接在宝塔的软件商店安装mysql然后添加数据库本地导入就行了
