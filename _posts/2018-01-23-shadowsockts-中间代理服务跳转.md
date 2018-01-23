---
layout: default
title: shadowsockts-中间代理服务跳转
---

![image.png](http://upload-images.jianshu.io/upload_images/5967024-4fddecc5947d1f8c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#使用场景
A(本地ss客户端) -> B(国内腾讯云,centos7) -> C([境外ss服务端,centos7](https://www.vultr.com/?ref=7131690)) 

# 如何跳转
1. 使用sslocal实现跳转
无法进行密码验证

2. 使用防火墙转发流量
阿里云和腾讯云无法使用防火墙nat模式

3. 使用软件路由([rinetd](https://boutell.com/rinetd/))

4. [使用haproxy实现](https://shadowsocks.be/10.html)
没有实践,觉得方案3暂时满足需求了


# 方案3具体步骤
```
wget http://www.boutell.com/rinetd/http/rinetd.tar.gz # 官网下载

tar zxvf rinetd.tar.gz # 解压

cd rinetd 

sed -i 's/65536/65535/g' rinetd.c # 需要替换下,否则报错

mkdir -p /usr/man/man8 # 需要手动创建

make && make install # 编译安装

vi /etc/rinetd.conf # 创建配置文件
[Source Address] [Source Port] [Destination Address] [Destination Port]
0.0.0.0 本地监听端口 ss服务端ip 远程ss服务端口  
例如: 0.0.0.0 18011 10.10.10.10 8011

rinetd #启动程序

netstat -tanulp|grep rinetd #查看是否启动成功,看到上面的18011端口监听成功即可

加入开机启动：
    vi /etc/rc.d/rc.local
    /usr/sbin/rinetd  #添加

配置妥当,修改A客户端地址为B地址和端口,其他配置不用变,依然生效
```
# 为什么要跳转
本人的vps在美国东海岸,家庭联通网络 
A --ping-->  C丢包比较严重(40%左右)
但是B --ping--> C一直很稳定,基本不丢包,加之 A --ping--> B也很稳定

所有想到了跳转来实现稳定的国际网环境,成本有点高哈~
不想这么麻烦的同学可以直接购买日本或者美国西海岸节点,也是延迟比较低,也比较稳定.


#Q&A
Q: 编译rinetd报错?
A: 编译需要linux gcc
```
 yum  install -y  gcc # 安装编译器
```

Any question?
