# 在阿里云服务器上部署Vue项目

# 第一步

先在服务器安装好宝塔（可用于上传文件，也可用于pm2部署接口）

![image-20220506152702639](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Server-deploy/%E5%9C%A8%E9%98%BF%E9%87%8C%E4%BA%91%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E9%83%A8%E7%BD%B2Vue%E9%A1%B9%E7%9B%AE/image-20220506152702639.png)

可以在此处上传打包好的vue项目

# 第二部

上传文件后，因为试过在宝塔的终端改nginx文件会报错，所以还是用阿里云的吧（垃圾宝塔）

![image-20220506152722187](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Server-deploy/%E5%9C%A8%E9%98%BF%E9%87%8C%E4%BA%91%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E9%83%A8%E7%BD%B2Vue%E9%A1%B9%E7%9B%AE/image-20220506152722187.png)

直接用阿里云自带的远程登录就好

# 第三步

安装nginx

```shell
# 切换至root用户
sudo su root
apt-get install nginx
```

```shell
# 查看nginx版本，检查是否安装成功，一般默认安装路径为/etc/nginx
nginx -v
```

# 第四步

配置nginx

![image-20220506152832382](https://cdn.jsdelivr.net/gh/aaaa8880788/duck-img//blog/Server-deploy/%E5%9C%A8%E9%98%BF%E9%87%8C%E4%BA%91%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E9%83%A8%E7%BD%B2Vue%E9%A1%B9%E7%9B%AE/image-20220506152832382.png)

找到文件后打开default，最文件最后面加入代码即可，多个项目以此类推

主要要改三处地方，分别是①②③，其他不用动，多个项目一次类推

```
server {
  listen  80;
  server_name XX.XX.XX.XX;    # ①这里填域名，我没有域名就填了个ip地址，不填好像也可以把，没试过
 
  #charset koi8-r;
 
  #access_log logs/host.access.log main;
 
  location /wzry/web {		#②这里location后面接的是你的访问后缀，结合你上面的访问就是XX.XX.XX.XX/wzry/web
   alias  /project-for-localhost/wzry/web-dist;			#③这里填写你的项目的打包文件夹目录
   index index.html index.htm;
   try_files $uri $uri/ @router;
   index index.html;
  }
  #对应上面的@router，主要原因是路由的路径资源并不是一个真实的路径，所以无法找到具体的文件
  location @router {
  #   #因此需要rewrite到index.html中，然后交给路由再处理请求资源
     rewrite ^.*$ /index.html last;
  }
  
  #下面重复，是第二个项目，以此类推
  location /wzry/admin {
   alias  /project-for-localhost/wzry/admin-dist;
   index index.html index.htm;
   try_files $uri $uri/ @router;
   index index.html;
  }
  #对应上面的@router，主要原因是路由的路径资源并不是一个真实的路径，所以无法找到具体的文件
  location @router {
  #   #因此需要rewrite到index.html中，然后交给路由再处理请求资源
     rewrite ^.*$ /index.html last;
  }
}
```

# 第五步

重新启动nginx

```shell
#如果启动过nginx的就先关闭nginx服务，没有启动过的，改语句不用执行
nginx -s stop   # 要进入的./nginx 目录下运行 如：/etc/nginx
#开启nginx服务
nginx   # 要进入的./nginx 目录下运行 如：/etc/nginx
```

