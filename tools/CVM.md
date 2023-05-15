# CVM

腾讯云服务器

## 安装宝塔Linux面板

[官网教程](https://cloud.tencent.com/document/product/1207/54078)

基本步骤:

1. 先在防火墙中添加规则-放行8888端口
2. 然后复制 ``sudo /etc/init.d/bt default``,登录服务器,执行这个命令,来获取系统默认的username和password

```
username: elqpkdds
password: f2da18fd6b98
```

3. 获取到面板首页地址:http://1.14.102.11:8888/tencentcloud
4. 登录后,绑定宝塔账号,会弹出关联腾讯云API密钥

关联之后就完成了宝塔Linux面板的配置

username: elqpkdds
password: C3ulct9g1CqA

密码不是固定的

安装redis要注意gcc的版本

更新gcc版本

```
yum -y install centos-release-scl

yum -y install devtoolset-9-gcc devtoolset-9-gcc-c++ devtoolset-9-binutils

scl enable devtoolset-9 bash

echo "source /opt/rh/devtoolset-9/enable" >> /etc/profile

gcc -v
```

## Redis

开启redis:
先在根目录(cd ../)
打开redis-6.0.6
打开src,执行./redis-server ../redis.conf
服务就开启了


## Chrome

centos安装Chrome

https://blog.csdn.net/ling1998/article/details/123951726
