---
title: 上传苹果商店authenticating with the itunes store问题解决方案
date: 2019-01-07 17:44:31
tags: 
    - AppStore
    - Xcode
    - iOS
comments: true
---
被这个问题折磨的死去活来. 整理一下来自stackoverflow的答案以及个人经验. 

**如果你一次都没成功过, 的确要考虑网络问题.**

比如防火墙之类.假如你之前成功过, 但是突然不好使了,可以参考以下答案. 

---
## 使用Application Loader
如果你使用Xcode的Organizer上传无效, 可以尝试使用Application Loader. 在Xcode的菜单栏中选择 `Xcode->Open Developer Tool->Application Loader`. 
<!-- more -->
## 更新itmstransporter
在终端输入以下指令:
```
cd ~  
mv .itmstransporter/ .old_itmstransporter/ 
"/Applications/Xcode.app/Contents/Applications/Application Loader.app/Contents/itms/bin/iTMSTransporter"
```
等待更新结束后重新打开Application Loader上传.
## 修改https端口
1. 打开Application Loader文件夹位置 : </br>
`/Applications/Xcode.app/Contents/Applications/Application Loader.app/Contents/MacOS/itms/java/lib`
2. 编辑 `net.properties` 修改 `#https.proxyPort=443` 为 `https.proxyPort=80`保存
3. 重新打开Application Loader

## 开启另一个Application Loader
在第一个Application Loader卡住之后,使用命令
```
open -n -a 'Application Loader'
```
开启一个新的Application Loader程序, 并上传.
## 重新生成证书(不推荐)
---
**我在尝试除了重新生成证书意外的所有方法之后,依然不行**.毕竟重新生成证书代价太大.后来我开始重新审视这个问题. 参考网上的分析, 卡住是因为客户端询问的信息在服务器返回以后, 并未正确的传递给上传进程. 我想起电脑里有一个老版本的Application Loader. 可能是由于老版本注册监听了某个端口导致新版本无法接收. 于是删除老版本并清空垃圾箱. 然后更新程序并修改端口(按上述方法), 再重启电脑, 一次成功!所以还是建议大家冷静的分析自己可能存在的问题,对症下药.

### 参考链接:

[StackOverflow: Xcode stuck at “Your application is being uploaded”](https://stackoverflow.com/questions/19953161/xcode-stuck-at-your-application-is-being-uploaded?noredirect=1&lq=1)

[StackOverflow: Application Loader stuck at “Authenticating with the iTunes store” when uploading an iOS app](https://stackoverflow.com/questions/22443425/application-loader-stuck-at-authenticating-with-the-itunes-store-when-uploadin?noredirect=1&lq=1)

[StackOverflow: application loader stuck at the stage of “Authenticating with the iTunes Store”](https://stackoverflow.com/questions/18971710/application-loader-stuck-at-the-stage-of-authenticating-with-the-itunes-store?noredirect=1&lq=1)