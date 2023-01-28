[toc]

# ngnix安装

资料：https://www.jianshu.com/p/c3294887c6b6

+ 安装Homebrew

+ 安装ngnix

  ```
  brew install nginx 
  ```

+ 验证

  ```
  brew services start nginx
  ```

==> 报错

1.

```
fatal: not in a git directory
Error: Command failed with exit 128: git
```

解决：https://blog.csdn.net/Morris_/article/details/125182905

2.

```
No such file or directory @ rb_sysopen
```

解决

```
export HOMEBREW_BOTTLE_DOMAIN=''

```

# ngnix配置

查看ngnix安装位置

```
ps -ef | grep nginx
```

然后使用搜索就可以找到ngnix的安装位置

ngnix启动与结束

```
brew services start  nginx
brew services stop  nginx
```

+ 项目部署

  ```
  //添加当前登录用户为owner
  user sam owner
  //在结尾大括号之前添加
  include /Users/sam/upload/upload.conf
  /Users/sam/upload/ 资源文件路径
  /Users/sam/upload/upload.conf 额外的配置
  ```

  upload.conf配置

  ```
  //http配置
  server
  {
  charset utf-8;
  listen 8089;
  server_name http_host;
  # server_name 192.168.xx.xx
  root /Users/sam/upload/;   //ngnix的根目录
  autoindex on;   //是否打开索引
  add header Cache-Control "no-cache must revalidate";
  location / { //监听所有请求
    add_header Access-Control-Allow-Origin:*;
  }
  }
  //https配置
  server
  {
  listen 443 default ssl;
  server_name http_host;
  autoindex on;   //是否打开索引
  root /Users/sam/upload/;   //ngnix的根目录
  add header Cache-Control "no-cache must revalidate";
  location / {
    add_header Access-Control-Allow-Origin:*;
  }
  ssl_certificate /Users/sam/xxx.pem;
  ssl_certificate_key /Users/sam/xxx.key;
  ssl_session_timeout 5m;
  ssl_protocols SSLv3 TLSv1;
  ssl_ciphers ALL:!ADH:!xxxx;
  ssl_prefer_server_ciphers on;
  }
  
  ```

  