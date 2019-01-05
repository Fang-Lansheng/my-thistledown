---
layout:	post
title:	"Hello, my blog!"
subtitle:	"And how to build this blog."
date: 2019-01-03
author:	"Thistledown"
header-img:	"img/post-bg-2015.jpg"
tags:
	- Blog
	- Nginx
	- Jekyll
---



## 前言

上一个基于 WordPress 的博客已经在重装服务器系统之后烟消云散了，因为搭建过程全程百度，自己毫无印象，也就不愿意再重复一遍机械式地抄写。这一会打算参考网上的教程，重新搭建一个自己 blog，并且把过程记录下来，权当做一个学习的过程吧。

搭建教程参考：

> [程序员如何搭建自己的个人博客 - 方志朋的博客](https://www.fangzhipeng.com/life/2018/10/14/how-to-build-blog/)

## 使用自己的服务器部署博客

参考教程，我也是使用 `Jekyll` 搭建博客。服务器系统为 `Centos 6 x86_64 bbr `

### VPS 及域名购买

VPS 是买的搬瓦工，一年不到 $20，相当划算。域名则是来自阿里云。这两者已经是很久之前购买的了，网络上相关教程及介绍也非常详尽，故不在此赘述。

### 连接到服务器

这里我是用的是 Xshell，Putty 也不错。因为不了解原理，所以也不多讲，实际操作中善用搜索引擎基本就能掌握操作。

### 安装 Node

使用 Xshell 连接到服务器之后。安装 Node 环境，执行以下命令：

```bash
wget https://nodejs.org/dist/v8.12.0/node-v8.12.0-linux-x64.tar.xz
xz -d node-v8.12.0-linux-x64.tar.xz
tar -xf node-v8.12.0-linux-x64.tar
ln -s ~/node-v8.12.0-linux-x64/bin/node /usr/bin/node
ln -s ~/node-v8.12.0-linux-x64/bin/npm /usr/bin/npm
node -v
npm
```

PS：使用 `xy` 命令时，提示：

```bash
-bash: xz: command not found
```

据了解，是 xz 这个解压工具尚未安装，解决步骤如下

```bash
# 1. 下载 xz 包
wget https://tukaani.org/xz/xz-5.2.4.tar.bz2

# 2. 解压安装包
tar -jxvf xz-5.2.4.tar.bz2

# 3. 配置 & 安装
cd xz-5.2.4
mkdir /opt/gnu
mkdir /opt/gnu/xz
./configure --prefix=/opt/gnu/xz	# 指定安装目录
make && make install		# 编译并安装
ln -s /opt/gnu/xz/bin/xz		# 建立软链接

# 4. 解压 xz 包
xz -d ***.tar.xz

# 5. 解压 tar 包
tar -xvf ***.tar
```

这中间又出现一个小插曲，`./configure --prefix=/opt/gnu/xz` 一步报错：`configure: error: no acceptable C compiler found in $PATH` ，经过查证，解决方法为安装 GCC 软件套件：

```bash
yum install gcc
```

### 安装 Ruby

Jekyll 依赖于 Ruby 环境，需要安装 Ruby，执行以下命令即可：

```bash
wget https://cache.ruby-lang.org/pub/ruby/2.4/ruby-2.4.4.tar.gz
mkdir  -p /usr/local/ruby
tar -zxvf ruby-2.4.4.tar.gz 
cd ruby-2.4.4
./configure --prefix=/usr/local/ruby
make && make install
```

环境变量设置：

```bash
vi .bash_profile
```

在 `.bash_profile` 文件设置以下内容：

```bash
PATH=/usr/local/ruby/bin:$PATH:$HOME/bin
export PATH
```

保存退出后，使环境变量生效：

```bash
source .bash_profile
```

### 安装 gcc

在前面已经踩坑装过 gcc 了，这里验证一下：

```bash
yum -y update gcc
yum -y install gcc+ gcc-c++
```

然后出现了诡异的报错：`-bash: yum: command not found`

？！刚刚我不是用过这个命令吗，怎么现在就找不到了？没办法，小白只好继续去谷歌，怀疑是刚刚修改环境变量出现的问题。连带着 `ls`、`cd` 等命令都用不了了。感谢万能的 CSDN，解决方法：在命令行下输入：

```bash
export PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin
```

### 安装 Jekyll

最后安装 Jekyll，执行以下命令：

```bash
gem install jekyll
jekyll --version
gem update --system
```

毫不意外地，报错 `-bash: gem: command not found`。找了一下，发现本地根本没有 gem：

```bash
[root@host ~]# which gem
/usr/bin/which: no gem in (/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin)
```

好吧，看来只能手动摘得这块“宝石”了：

```bash
wget https://rubygems.org/rubygems/rubygems-3.0.2.tgz	# 下载源码
tar -zxvf rubygems-3.0.2.tgz	# 解压源码文件
cd rubygems-3.0.2
ruby setup.rb	# 安装
```

行吧，安装完之后，运行 gem 命令：

```bash
[root@host /]# gem install jekyll
ERROR:  Loading command: install (LoadError)
	cannot load such file -- zlib
ERROR:  While executing gem ... (NoMethodError)
    undefined method `invoke_with_build_args' for nil:NilClass
```

这是缺少 zlib 依赖，需要安装 zlib 库，可以通过 yum 安装，但是安装之后要记成到 ruby 中：

```bash
# 安装 zlib 库
yum install zlib-devel
# 集成 zlib 库到 ruby 环境
cd root/ruby-2.4.4
cd ext/zlib
ruby extconf.rb
# 在操作下一步之前需要修改 Makefile 文件中的 
# zlib.o：$(top_srcdir)/include/ruby.h （大概在倒数几行）
# 改为：
# zlib.o: ../../include/ruby.h
# （这一步如果不修改，make 时会爆出另一个问题）
make && make install
```

还有一个错误：

```bash
[root@host /]# gem install jekyll
ERROR:  While executing gem ... (Gem::Exception)
    Unable to require openssl, install OpenSSL and rebuild Ruby (preferred) or use non-HTTPS sources
```

这里提示缺少 openssl 库，安装方法类似上一步：

```bash
# 安装 OpenSSL 库
yum install openssl-devel
# 记成 OpenSSL 库到 Ruby
cd root/ruby-2.4.4
cd ext/openssl
ruby extconf.yb
# 同样修改 Makefile 中的 $(top_srcdir) 为 ../..
# 大概数十处，改到吐血==！不知道 Vim 有没有全局查找/替换功能…
make && make install
```

终于，出现了 `installing default openssl libraries`，感谢博文 [blog.csdn](https://blog.csdn.net/feinifi/article/details/78251486) 的指导，终于可以继续安装 Jekyll了：

```bash
gem install jekyll
jekyll --version
gem update --system
```

安装过程略长，但是一气呵成，舒服！

```bash
[root@host /]# jekyll --version
jekyll 3.8.5
[root@host /]# gem update --system
Latest version already installed. Done.
```

### 编译博客

需要 git 工具，下载在 github 上的代码，执行以下命令：

```bash
git clone https://github.com/Fang-Lansheng/my-thistledown.git
cd my-thistledown
jekyll server
```

### 部署到 Nginx 服务器上

通过 Jekyll 编译后的静态文件需要挂载到 Nginx 服务器，因此需要安装 Nginx 服务器。安装过程参照[官方教程](http://nginx.org/en/linux_packages.html#mainline)：

```bash
cd /etc/yum.repos.d/
# 新建文件
vi nginx.repo
```

在文件中编辑以下内容，并保存：

```
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/mainline/centos/6/$basearch/
gpgcheck=0
enabled=1
```

然后执行安装 Nginx 命令，如下：

```bash
yum install nginx
```

果然不失所望，安装过程中，找不到包了，在网上的教程中转了又转，敲了几行竟然又成了：

```bash
yum clean all
yum makecache
yum -y install nginx
```

大功告成！

Nginx 配置成功后，需要设置 Nginx 的配置，配置路径为 `/etc/nginx/conf.d/default.conf`，配置内容如下：

```bash
server {
    listen       80;
    server_name  localhost my-thistledown.com www.my-thistledown.com;	
	# 非 www 域名的重定向到 www
	if ( $host != "www.my-thistledown.com" ) {
    	rewrite "^/(.*)$" http://www.my-thistledown.com/$1 permanent;
	}

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}
```

安装 Nginx 服务成功后，将 Jekyll 编译的博客静态 html 文件输出到 Nginx 服务器上，执行以下命令：

```bash
jekyll build --destination=/root/blog/html
```

启动 Nginx 服务器，就可以正常访问博客网页。如果需要在浏览器上访问，需要在阿里云 ECS 控制台的安全组件暴露 80 端口。如果想通过域名访问，需要将域名解析设置指向你的服务器地址。

毫无疑问，没有成功……可能是用 Jekyll 输出文件到 Nginx 服务器上出错了吧，没关系，这里修改一下配置文件：

```bash
vi /etc/nginx/nginx.conf
```

打开配置文件，显示：

```bash
user nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
```

`http` 括号下有 `include /etc/nginx/conf.d/*.conf` ，因此我们自定义的配置文件是成功导入的：

```bash
vi /etc/nginx/conf.d/default.conf
```

重新修改配置文件，静态文件目录为我们网页文件所在的目录：

```bash
# location / {} 中修改这两行：
root /root/myblog/my-thistledown;
index index.html;
```

然后将 `nginx.conf` 中第一行 `user nginx` 修改为：

```bash
user root;	# 这里可能存在权限问题
```

重启服务，成功运行。在浏览器中输入域名，出现了 `index.html` 的内容。



### 启动 Nginx 服务器

```bash
nginx -c nginx.conf
```

如果遇到 `nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)` 这样的错误，可能是端口被占用。

```bash
# 方法一
# Linux 通过端口查看进程
netstat -nap | grep 端口号

# 方法二
# 先查看进程 PID
ps -ef | grep 进程名
# 通过 PID 查看占用端口
netstat -nap | grep 进程PID
```



### 停止 Nginx 服务器

```bash
ps -ef | grep nginx

# 从容停止 Nginx
kill -QUIT 主进程号

# 快速停止 Nginx
kill -TERM 主进程号

# 强制停止 Nginx
pkill -9 nginx
```



### 重启 Nginx 服务器

```bash
sudo nginx -s reload
```



### 访问首页时直接显示域名，不显示 ***/index.html

这算是一个小插曲，我发现访问我的首页 http://www.my-thisdown.com 时并不是显示index.html，而访问 http://www.my-thisdown.com/index.html 时才跳转到我的主页。这显然与不太符合预期，在网络上查找一番，发现有以下解决方法：

首先，还是打开配置文件：

```bash
vi /etc/nginx/conf.d/default.conf
```

在其中添加以下几行代码即可：

```bash
location / {
    root /root/myblog/my-thistledown;
    index index.html index.htm;
    # 以下为新添加内容
    # 此 if 为判断是否访问了 root 目录，为是时，跳转到 index 配置项，即访问到 index.html
    if (!-e $request_filename) {
        proxy_pass http://index;
    } 
}
```



### 自动化部署

通过设置 GitHub 的 Webhooks 可以实现自动化构建和部署。

过程为：提交博文或者配置到 GitHub 仓库，仓库会触发你设置的 Webhook，会向你设置的 Webhooks 地址发送一个 post 请求，比如我设置的请求是在服务器跑的一个 Node.js 程序，监听 GitHub Webhooks 的请求，接收到请求后，会执行 shell 命令。

首先设置 GitHub 的 Webhooks，在 GitHub 仓库的项目界面，点击 `Settings`→`Webhooks`→`Add webhook`，添加配置信息，示例如下：

> - Payload URL
>
> http://www.my-thistledown.com/webhook
>
> - Content type
>
> application/json
>
> - Secret
>
> \*\*\*\*\*\*

点击 Add webhook 按钮，就设置成功了。

现在去服务器端监听 GItHub Webhook 发送的请求，我也跟随教程，采用了开源组件 [github-webhook-handler](https://github.com/rvagg/github-webhook-handler) 去监听。首先安装：

```bash
npm i github-webhook-handler
```

安装成功后，在 `/root/node-v8.12.0-linux-x64/lib/node_modules/github-webhook-handler` 文件夹下新建 `deploy.js` 文件：

```js
var http = require('http');
var createHandler = require('github-webhook-handler');
var handler = createHandler({ path: '/webhook', secret: '******' });

function run_cmd(cmd, args, callback) {
  var spawn = require('child_process').spawn;
  var child = spawn(cmd, args);
  var resp = "";
 
  child.stdout.on('data', function(buffer) { resp += buffer.toString(); });
  child.stdout.on('end', function() { callback (resp) });
}

http.createServer(function (req, res) {
  handler(req, res, function (err) {
    res.statusCode = 404
    res.end('no such location')
  })
}).listen(3001)

handler.on('error', function (err) {
  console.error('Error:', err.message)
})

handler.on('push', function (event) {
  console.log('Received a push event for %s to %s',
    event.payload.repository.name,
    event.payload.ref);
  run_cmd('sh', ['./start_blog.sh'], function(text){ console.log(text) });
})
```

上述代码中，制定了 Node.js 服务的端口为 3001，监听了 path/webhook，secret 为 \*\*\*\*\*\*，这和之前在 GitHub Webhook 中的设置需要一致。

代码 `run_cmd('sh', ['./start_blog.sh']，···)`，指定了接收到请求后执行 `./start_blog.sh`，其代码如下：

```bash
echo `date`
cd /root/myblog/my-thistledown
echo start pull from github 
git pull
echo start build..
jekyll build --destination=/usr/share/nginx/html
```

然后需要使用 `forover` 来启动 `deploy.js` 的服务，执行命令如下：

```bash
sudo npm install forever -g   #安装
forever start deploy.js          #启动
forever stop deploy.js           #关闭
forever start -l forever.log -o out.log -e err.log deploy.js   #输出日志和错误
```

最后一步，需要在 Nginx 服务器的配置文件，将监听的 `/webhook` 请求转发到 `Node.js` 服务上，配置代码如下：

```bash
location = /webhook {
    proxy_pass http://127.0.0.1:3001/webhook;
}
```

这样，当你提交了文章或者修改的配置到 GItHub 上，GitHub 通过 webhook 向你所在的服务器发送请求，服务器接收到请求后执行 sh 命令，sh 命令包括了重新 pull 代码和编译代码的过程，这样自动化部署就完成了，你只需提交代码，服务器就触发 pull 代码和重新编译的动作。

### Acme.sh

> 此节和接下来的 `HTTPS` 一节参考文章：[在VPS上通过Jekyll搭建博客 - linkthis blog](https://linkthis.me/2018/03/07/blog-poweredf-by-jekyll-on-vps/)

为了提高网站的安全性和保证访问质量，我们需要让网站使用 HTTPS 连接，而我们首先需要申请一个受信任的证书。大牌提供商的 SSL 证书并不便宜，所以我们选择使用由 [Let’s Encrypt](https://letsencrypt.org/) 提供的免费证书。需要注意的是Let’s Encrypt提供的证书的**有效期只有90天**，所以我们需要使用脚本定期更新。而`acme.sh`实现了`acme`协议，可以从 Let’s Encrypt 生成免费的证书并自动更新。

> ACME的全称为Automated Certificate Management Environment，即自动化证书管理环境，相关内容可以参看此[仓库](https://github.com/ietf-wg-acme/acme/)。

安装 `acme.sh` 十分简单，只需执行如下命令：

```bash
curl  https://get.acme.sh | sh
```

其会安装到所在目录下的 `~/.acme.sh/` 中，并自动创建一个 `bash` 的`alias`：`acme.sh=~/.acme.sh/acme.sh`。注意为了让安装在shell当中即时生效，我们需要执行：`source .bashrc`。安装过程不会污染任何已有的系统功能和文件， 所有的修改都限制在安装目录：`~/.acme.sh/`中。如果需要更高级的安装选项可以参看 `acme.sh` 的 [How to install](https://github.com/Neilpang/acme.sh/wiki/How-to-install)。
在 `acme.sh` 提供的验证方式当中我们选择使用 DNS 验证（不需要任何服务器和任何公网 IP，只需要 DNS 的解析记录即可），不过我们需要同时配置 `Automatic DNS API`，保证 `acme.sh` 自动更新证书。在此仅以 Aliyun domain API 为例，执行如下操作：

```bash
export Ali_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"
export Ali_Secret="jlsdflanljkljlfdsaklkjflsa"

./acme.sh --issue --dns dns_ali -d my-thistledown.com -d www.my-thistledown.com
```

配置当中需要等待 120s （原文为 300s ，我实际操作时已经是120s 了），以便自动添加的 txt 解析记录生效。域名商 API 的用法可以参看 [DNS API](https://github.com/Neilpang/acme.sh/blob/master/dnsapi/README.md)。
由于 `acme` 协议和 Let’s Encrypt CA 都在不定期的更新, 因此 `acme.sh` 也需要更新以保持同步，执行如下命令：

```bash
./acme.sh --upgrade #手动更新
./acme.sh  --upgrade  --auto-upgrade #自动更新
./acme.sh --upgrade  --auto-upgrade  0 #关闭自动更新
```

### HTTPS

在为 Nginx 配置 HTTPS 之前，我们首先需要将刚才申请的证书安装到需要使用的地方，请不要使用 `~/.acme.sh/` 目录下的文件，里面的文件都是内部使用，而且目录结构可能会变化。首先应该创建存放使用证书的文件夹：

```bash
mkdir /etc/nginx/ssl/
```

然后使用 `--installcert` 将证书安装到指定位置：

```bash
acme.sh --install-cert -d my-thistledown.com \
--keypath       /etc/nginx/ssl/mydomain.key  \
--fullchainpath /etc/nginx/ssl/fullchain.cer \
--reloadcmd     "service nginx force-reload"
```

我们指定了 `--installcert` 的 `reloadcmd` 保证证书更新以后自动调用新的证书给 Nginx 使用。同时我们使用的是 **fullchain.cer**，防止 SSL Labs 测试时出现 `Chain issues Incomplete` 的错误。而 Nginx 应该使用 `force-reload` 保证重新加载证书。更加详细的参数可以参考 [Install the cert to Apache/Nginx etc](https://github.com/Neilpang/acme.sh#3-install-the-cert-to-apachenginx-etc)。
如果出现 `Failed to restart nginx.service: Unit nginx.service is masked.`，则需要先执行：

```bash
systemctl unmask nginx # 未报错则忽略
```

然后生成键值以启用 Perfect Forward Security（PFS）：

```bash
openssl dhparam -out dhparam.pem 4096
# This is going to take a long time......
# A really long time...
# ...
# Ten hours later TAT
```

之后进行 HTTPS 配置的环境为**：Nginx 1.13.10，OpenSSL 1.0.2l，支持HSTS**。 你可以使用如下命令查看 Nginx 和 Openssl 的版本：

```bash
nginx -v
openssl version
```

Nginx 配置模板：

```bash
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    # 使用301将HTTP访问重定向到HTTPS
    return 301 https://$host$request_uri;
}
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;

    access_log  /var/log/nginx/www.my-thistledown.com_access.log;
    error_log  /var/log/nginx/www.my-thistledown.com_error.log;

    # Let's Encrypt生成的文件
    ssl_certificate /etc/nginx/ssl/fullchain.cer;
    ssl_certificate_key /etc/nginx/ssl/mydomain.key;
    ssl_session_timeout 1d;
    ssl_session_cache shared:SSL:50m;
    ssl_session_tickets off;

    ssl_dhparam /etc/nginx/ssl/dhparam.pem;

    # OCSP Stapling ---
    # fetch OCSP records from URL in ssl_certificate and cache them
    ssl_stapling on;
    ssl_stapling_verify on;

    ssl_protocols TLSv1.2;
    ssl_ciphers 'ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256';
    ssl_prefer_server_ciphers on;
}
```

### 用阿里云的免费 SSL 证书让网站从 HTTP 换成 HTTPS

> 本文参考：https://ninghao.net/blog/4449

**申请证书**

1. 登录：阿里云控制台→产品与服务→证书服务→购买证书
2. 购买：证书类型选择→免费型DV SSL，然后完成购买
3. 补全：在 `我的证书` 控制台，找到购买的证书，在操作栏里选择补全，填写证书相关信息。
4. 域名验证：可以选择 DNS，如果域名用了阿里云的 DNS 服务，再勾选一下 证书绑定的域名在 阿里云的云解析
5. 上传：系统生成 CSR，点一下创建
6. 提交审核

**下载证书**

证书审核通过后，即可下载。可以根据自己的服务器类型选择证书下载，支持 Tomcat、Apache、Nginx、IIS 等。下载对应的证书，解压后得到两个文件，一个是 `*.key`，一个是 `*.pem`。

**配置 Nginx 的 HTTPS**

有了证书，就可以去服务器使用了。

第一步：上传证书

创建一个存储证书的目录：

```bash
mkdir /etc/nginx/ssl/my-thistledown.com
```

把申请并下载下来的证书，上传到上面创建的目录中，这里我使用的是 `Xftp` 进行的文件传输。证书的实际位置为：

```bash
/etc/nginx/ssl/my-thistledown.com/1692217_my-thistledown.com.key
/etc/nginx/ssl/my-thistledown.com/1692217_my-thistledown.com.pem
```

第二步：Nginx 配置文件

你的网站可以同时支持 HTTP 与 HTTPS，HTTP 的默认端口号是 80，HTTPS 的默认端口号是 443。也就是说，如果你的网站要使用 HTTPS，你需要配置网站服务器，让其监听 443 端口，也就是用户使用 HTTPS 发出的请求。

新增配置文件：

```bash
vi /etc/nginx/conf.d/https.conf
```

增加如下配置：

```bash
server {
  listen       443;	# 默认 443 端口
  server_name  my-thistledown.com;
  ssl          on;
  root /usr/share/nginx/html;
  index index.html; 

  ssl_certificate   /etc/nginx/ssl/my-thistledown.com/1692217_my-thistledown.com.pem;
  ssl_certificate_key  /etc/nginx/ssl/my-thistledown.com/1692217_my-thistledown.com.key;
  ssl_session_timeout 5m;	# 客户端可以重用会话参数的时间
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;	# 使用的协议
  ssl_ciphers AESGCM:ALL:!DH:!EXPORT:!RC4:+HIGH:!MEDIUM:!LOW:!aNULL:!eNULL;	# 配套加密套件
  ssl_prefer_server_ciphers on;
}
```

然后重新加载 Nginx 服务：

```bash
sudo service nginx reload
# 或
sudo systemctl reload nginx
# 或
nginx -s reload
```

**遇到的问题**

配置成功后，无法成功访问，执行 `nginx -t` 检查：

```bash
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

没有出错，看来问题出在其他地方。

Centos 6 的防火墙配置有问题：

```bash
# 配置 iptables 端口
vi /etc/sysconfig/iptables
```

添加下面几行

```bash
-A INPUT -p tcp -m state --state NEW -m tcp --dport 21 -j ACCEPT	
-A INPUT -p tcp -m state --state NEW -m tcp --dport 80 -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 8080 -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 443 -j ACCEPT
# 在 COMMIT 一行之前
```

- 21端口：默认，供 ssh 访问
- 80、8080 端口：HTTP 服务访问
- 445 端口：HTTPS 服务访问（默认 443）

然后重启 iptables 服务：

```bash
service iptables restart	# 重启 iptables 服务
service iptables enable		# 设置 iptables 服务开机启动
service iptables status		# 查询防火墙状态
```

再修改了下配置文件，这下应该是没问题了：

```
server {
    listen       80 ;
    server_name  _ localhost my-thistledown.com www.my-thistledown.com ;
    location = /webhook {
        proxy_pass http://127.0.0.1:3001/webhook;
    }
    
#    return 301 https://www.my-thistledown.com$request_uri;
}
server {
    listen 443;
    server_name my-thistledown.com;
    return 301 https://www.my-thistledown.com$request_uri;
}
server {
    listen 443 default_server ssl;
    server_name www.my-thistledown.com;

    ssl          on;
    ssl_certificate   /etc/nginx/ssl/my-thistledown.com/1692217_my-thistledown.com.pem;
    ssl_certificate_key  /etc/nginx/ssl/my-thistledown.com/1692217_my-thistledown.com.key;
    ssl_session_timeout 5m;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers AESGCM:ALL:!DH:!EXPORT:!RC4:+HIGH:!MEDIUM:!LOW:!aNULL:!eNULL;
    ssl_prefer_server_ciphers on;


    root /usr/share/nginx/html;
    index index.html index.htm;

    location / {
        root /usr/share/nginx/html;
        index index.html;
    }


    error_page  404              /404.html;

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

}
```