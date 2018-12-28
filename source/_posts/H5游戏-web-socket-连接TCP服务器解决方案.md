---
title: H5游戏(web-socket)连接TCP服务器解决方案
date: 2018-12-28 14:04:08
tags: 
    - web-socket
    - tcp
    - html5
comments: true
---
伴随着H5技术越来越成熟，微信小程序以及小游戏的兴起，有越来越多的需求将现有的应用程序轻量化。此前公司需求将游戏APP端移植到H5，就产生了如下问题：**H5无法与原有的TCP服务器直接连接，只能采用web-socket**。</br>
将原有的TCP服务器改写也不太现实。除了耗时耗力以外，我们还希望客户端与H5的玩家可以同服游戏。所以需要一个中转工具来实现web-socket与tcp的互连。由于移植工作只有我一人在做，而我又是客户端程序，对服务器代码不甚了解。于是在网上苦苦寻找之后，找到了这么一个项目。</br>

https://github.com/andrewchambers/ws-tcp-bridge


# 使用说明
我们采用在npm中安装的方式，省去配置环境变量的麻烦。此文中服务器环境为Windows。
<!-- more -->
## 1. 安装nodejs
既然用到了npm，就要先安装[nodejs](https://nodejs.org/zh-cn/)。安装过程不再阐述，需要注意的就是配置环境变量要勾选，省去不必要的麻烦。可以参考这篇[安装教学](http://www.runoob.com/nodejs/nodejs-install-setup.html)。</br>

装好nodejs以后，在命令行中键入
```
npm
```
如果看到了帮助信息，说明安装成功。
非常简单直接! 按照说明中配置即可。每个TCP通信端口需要准备一个空闲的端口来做中转。每个端口需要开启两个中转程序，用于收和发。
## 2. 安装ws-tcp-bridge
命令行中键入
```
npm install -g ws-tcp-bridge ws-tcp-bridge
```
安装完成后键入
```
ws-tcp-bridge
```
看到如下内容即为安装成功

![](/images/h5yxws/01.png)

## 3. 编辑启动批处理文件

在桌面上创建一个`wstcpbridge.bat`文件，编辑其内容。列如：
```
start ws-tcp-bridge --method=ws2tcp --lport=27007 --rhost=127.0.0.1:17007
start ws-tcp-bridge --method=tcp2ws --lport=17007 --rhost=ws://127.0.0.1:27007
```
运行成功后如图

![](/images/h5yxws/02.png)

此时我的目标TCP端口为17007，我将27007作为中转端口（要保证该端口未占用），第一个程序用于接收来自H5的web-socket协议转发给tcp服务器，第二个程序将tcp服务器的消息转为web-socket后发送给客户端。

至此转发就配置完成了。

# 注意事项

## 异常崩溃

试运行阶段期间，每隔一段时间，中转程序就会报错崩溃。经查询后发现，是由客户端的异常中断导致的。当客户端向服务器发送消息`C2S`后，服务器接收到消息并返回消息`S2C`给中转程序，期间如果客户端断开连接，则中转程序找不到刚才与服务器通信的客户端，程序崩溃。

### 解决方法

在npm的node_modules目录下找到源码并修改。通常情况下Windows系统的路径为`C:\Users\用户名\AppData\Roaming\npm\node_modules\ws-tcp-bridge\ws-tcp-bridge.js`

修改代码中所有
```
ws.send(data, {any})
```
方法，添加错误回调
```
// 第三个参数为错误回调
ws.send(data, {any}, function(error){})
```
来规避掉客户端异常断线的问题

修改后再未出现过崩溃的情况。

## 客户端IP

由于采用了中转的方式, 服务器采集到的所有客户端的IP地址都是中转程序的IP。也就是说所有客户端的IP都是相同的。如果应用中有显示用户IP的功能，且该IP为服务器提供则会出现问题。

### 解决办法

可以通过淘宝API http://ip.taobao.com/service/getIpInfo.php?ip=myip 等IP查询接口来获取IP后发送给服务器