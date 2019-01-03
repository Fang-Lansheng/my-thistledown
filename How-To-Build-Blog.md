# 搭建自己的博客

`2019年1月3日` `Blog`

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

