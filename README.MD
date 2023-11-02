
## 使用docker快速搭建typecho博客系统 

### 准备工作
* 安装docker及docker-compose
* 申请域名并解析到主机(可跳过）
* 域名申请ssl证书（可跳过）

### 安装typecho

> 官网下载typecho文件放在typecho文件夹中
>
> 解压typecho文件

### 修改mysql.env

> 根据文件提示修改mysql环境变量


### 进入nginx修改defalut.conf，没域名直接跳到无域名

> 修改文件代码如下所示，需要修改自己申请的域名
>
> ```
> server {
>     listen       80;
>     server_name  xxxxx.com; #绑定的域名
>     rewrite ^(.*) https://type.xxxxx.com$1 permanent;
> }
> 
> server {
>     listen 443  ;
>     http2 on ; 
>     server_name xxxxx.com;  #绑定的域名
> 
>     root /var/www/html;
>     index index.php;
> 
>     access_log /var/log/nginx/typecho_access.log main;
> 
>     #ssl_certificate /etc/nginx/ssl/ssl.cert;
>     #ssl_certificate_key /etc/nginx/ssl/ssl.key;
>     #ssl_session_cache shared:SSL:1m;
>     #ssl_session_timeout 5m;
>     #ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
>     #ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
>     #ssl_prefer_server_ciphers on;
> 
>     if (!-e $request_filename) {
>         rewrite ^(.*)$ /index.php$1 last;
>     }
> 
>     location ~ .*\.php(\/.*)*$ {
>         fastcgi_pass   php:9000;
>         fastcgi_index  index.php;
>         fastcgi_param  PATH_INFO $fastcgi_path_info;
>         fastcgi_param  PATH_TRANSLATED $document_root$fastcgi_path_info;
>         fastcgi_param  SCRIPT_NAME $fastcgi_script_name;
>         fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
>         include        fastcgi_params;
>     }
> 
> }
> ```
>
> 

### 443端口开启ssl服务

> 将域名申请的ssl证书统一修改为ssl.crt和ssl.key放到ssl文件夹中

### 无域名，进入nginx修改defalut.conf

> 将文件代码直接改为
>
> ```
> server {
>     listen 80;
> 
>     root /var/www/html;
>     index index.php;
> 
>     access_log /var/log/nginx/typecho_access.log main;
> 
>     location / {
>         try_files $uri $uri/ /index.php?$query_string;
>     }
> 
>     location ~ \.php$ {
>         fastcgi_pass   php:9000;
>         fastcgi_index  index.php;
>         fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
>         include        fastcgi_params;
>     }
> }
> ```


### docker-compose 安装
进入主目录，输入

```
sudo docker-compose up -d
```

即可安装，安装成功后浏览器输入域名地址（无域名直接输入主机ip地址)即可成功访问，首先会提示输入数据库的信息（**数据库地址一栏填写mysql，而不是localhost**，其他信息都为之前mysql.env修改的）

