# Nginx学习笔记

2023年6月17日 09点17分

## Nginx准备

下了一个XShell,看看这软件好用不

修改虚拟机网络为静态IP,编辑`vi /etc/sysconfig/network-scripts/ifcfg-ens33`
![20230617094332](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230617094332.png)
*改完之后,发现不能联网了,原来是Vmware设置的网关和虚拟机里面设置的不太一样,把虚拟机的修改成`192.168.168.2`就好了.

---

>Nginx常用版本
1. [Nginx开源版](http://nginx.org/)
2. [Nginx plus商业版](https://www.nginx.com)
3. [Openresty](http://openresty.org)
4. [Tengine](http://tengine.taobao.org)

## Nginx安装

这里我们先使用Nginx开源版

传输到虚拟机:
![20230617102715](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230617102715.png)

1. 然后就是编译安装,打开Nginx的文件夹,然后执行`./configure --prefix=/usr/local/nginx`
2. 执行`make`
3. 执行`make install`

即可安装成功,但是由于此时系统有防火墙,开启Nginx服务后是访问不到的,所以要关闭防火墙

**关闭防火墙**
1. `systemctl stop firewalld.service`
2. 关闭防火墙的开机启动:`systemctl disable firewalld.service`
3. 



访问成功:
![20230617103927](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230617103927.png)

---

常用的Nginx命令,进入安装好的目录`/usr/local/nginx/sbin`
```txt
1. /nginx 启动
2. /nginx -s stop 快速停止
3. /nginx -s quit 优雅关闭,在退出前完成已接收的连接请求
4. /nginx -s reload 重新加载配置
```

---

**将Nginx安装成系统服务**
1. 创建服务脚本`vi /usr/lib/systemd/system/nginx.service`
2. 粘贴内容:
```txt
[Unit]
Description=nginx - web server
After=network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
PIDFile=/usr/local/nginx/logs/nginx.pid
ExecStartPre=/usr/local/nginx/sbin/nginx -t -c /usr/local/nginx/conf/nginx.conf
ExecStart=/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
ExecReload=/usr/local/nginx/sbin/nginx -s reload
ExecStop=/usr/local/nginx/sbin/nginx -s stop
ExecQuit=/usr/local/nginx/sbin/nginx -s quit
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

然后将之前启动的nginx关闭,再执行脚本:
![20230617105157](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230617105157.png)

设置开机启动:`systemctl enable nginx.service`

## Nginx基本使用

### 目录结构与基本运行原理

>**目录**

主要的配置文件:
![20230617105756](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230617105756.png)

### Nginx多进程模型和基本请求流程

![20230617110922](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230617110922.png)

### Nginx基础配置

1. 最小配置文件
2. 核心配置
3. 虚拟主机配置

**现阶段学习Nginx最重要的配置**

删除所有的注释的配置文件(也叫最小配置文件):
```conf

worker_processes  1;  # 开启进程数,和服务器物理配置有关,大概是一个CPU内核对应一个进程数


events {
    worker_connections  1024;  # 每个进程可以创建的连接数
}


http {
    include       mime.types;  # 众多格式,用于指示浏览器文件的类型什么的
    default_type  application/octet-stream;  # 如果mime类型没匹配上，默认使用二进制流的方式传输。

    sendfile        on;   # 数据零拷贝,就是高效网络传输

    keepalive_timeout  65;  # 保持连接-超时时间

# 虚拟主机 vhost
    server {
        listen       80;    # 一个监听端口号
        server_name  localhost;     # 主机名-ip地址/域名

        # 后面会讲-子目录/uri
        location / {
            root   html;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

    }
}
```

>**虚拟主机与域名解析**

- 域名.dns.ip地址的关系
- 浏览器,Nginx与http协议
- 虚拟主机原理
- 域名解析与泛域名解析实战
- 域名解析相关企业项目实战技术架构
    - 多用户二级域名
    - 短网址
    - httpdns
- Nginx中的虚拟主机配置


配置本机上的域名解析(在本机上模拟这个)
首先找到C盘下的host文件`C:\Windows\System32\drivers\etc`
![20230617215057](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230617215057.png)
?该完之后,就能访问了?
![20230617215552](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230617215552.png)

所以是虚拟中的nginx配置了这个域名?还是啥,有点晕

---

>**公网域名配置与泛域名解析**

我的域名已经解析到github pages了,这里先看着吧,多记得了

之前那个type里有A,和CNAME,代表的意思:
- A:将域名指向一个IPV4地址
- CNAME:将域名执行另外一个域名
    - 所以这里使用了A来指向我pages的ip地址,CNAME指向了pages的域名
    - ![20230617220210](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230617220210.png)
- AAAA:将域名指向一个IPV6地址
- NS:将子域名指定其他DNS服务器解析

配置虚拟主机:
```txt
# 演示不同端口号的服务
    server {
        listen       80;
        server_name  localhost;

        location / {
            root   /www/www;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

    }

    server {
        listen       88;
        server_name  localhost;

        location / {
            root   /www/vod;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

    }
```

嗯...还是没学会怎么配二级域名

---

学会了学会了,看那个给pages绑定域名文章的最后面就行了

#### ServerName匹配规则

- 我们可以在同一servername中匹配多个域名
- 完整匹配
- 通配符匹配
- 通配符结束匹配
- 正则匹配

普通方式:
```json
    server {
        listen       80;
        server_name  vod.zzmr.club vod1.zzmr.club;

        location / {
            root   /www/vod;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

    }
```

而通配符就是这样:
```json
    server {
        listen       80;
        server_name  *.zzmr.club;

        location / {
            root   /www/vod;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

    }
```

正则匹配:
```json
    server {
        listen       80;
        server_name  ~^[0-9]+\.zzmr\.club$;

        location / {
            root   /www/vod;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

    }
```

#### 反向代理介绍

- 网关,代理与反向代理
- 反向代理在系统架构中的应用场景
- Nginx的反向地理配置
- 基于反向代理的负载均衡器
- 负载均衡策略

![20230618094716](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230618094716.png)

就是数据透过Nginx进行传递?就是Nginx代理了用户的请求,tomcat服务器是没有与用户直接进行交互的,而是中间过了一手的Nginx,这就叫代理

那什么叫反向?由于nginx是服务商反向提供给用户的,所以叫反向代理?而正向代理,就是用户为了访问外网,主动的提供了一个代理服务器..

而网关实际上就是代理服务器

>配置反向代理

```conf
location / {
            proxy_pass https://blog.zzmr.club;
            # root   html;
            # index  index.html index.htm;
        }
```

这里如果这么配,由于nginx没有配置https模块,所以会报错,此时先改成http,则可以实现反向代理

但是呢,如果域名前没有`www`,那么访问之后,还会显示代理的域名,如果有`www`,则会显示代理之后的域名

什么意思呢,就是如果配置的没有`www`,则实现的只是转发的过程,就是跳转到了该域名

