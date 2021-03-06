---
title: 华为短信机.jpg
tags: 
    - 折腾
categories:
    - 技术
layout: post
date: 2019-2-08 12:28:00
---

## 剧情前提

到国外以后，国内带来的两张手机卡突然有一张没地方放了。刚好老母过来的时候让她赶紧把手里的华为P9给换了水果，顺便还注册了美区ID。既然有台这个机器……这就折腾一下吧

## 1st

首先想到的注意是做闹钟（然而华为EMUI这个*！@#¥一定要用他的语音助手，谷歌的在黑屏以后无法唤醒，还不能解锁屏幕……于是后面用了Google mini，Spotify的羊毛），顺便可以做成一个监控，然后其实才是短信。但是无论怎么样，要找个地方可以架起来放着：

https://www.amazon.com/gp/product/B06Y12XQ1X/

很好，顺便买了一个带USB口的插板，电源准备就绪

### 解锁

TL;DR，**结论看末尾加粗**

首先，这台机器（`EVA-TL00`）是移动机，我联通的卡进去直接GG。然而我发现实际上他是连得上TMO的3G网（联通卡），看来就是硬编码了一下系统（实际上也是）。如果要全网的话就要解锁拿权限

这个机器在老母用的时候就一直没有升级，拿过来以后本来打算解锁BL，结果刚好过了#¥%…的时间。然后还手贱升级了，含泪找了老半天的包降级回去，拿到了ROOT，照着[吾爱破解的教程](https://www.52pojie.cn/thread-816065-1-1.html)，结果已经失效了，拿到了是

![](../_assets/media/tasker-messenger/unlock_1.jpg)

>UUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUDDDDDDDDDDDDDDDD  
WVDEVID

这个状态应该是已经加密后的，（我…#¥）

然后直接找淘宝，他们用`USB Redirector`这种东西直接远程到手机（我本来还为此准备了虚拟机，不喜欢别人直接远程，然后在国外……迅雷用不了还是找 [@Elepover](https://daily.elepover.com/) 帮忙下的win7镜像），结果……我在国外网太慢，第一家直接给我退款了（😊）

第二家弄了一会弄好了，但是第一家走之前留下了一些没有删掉的文件，其中一个是一个很罕见的驱动：`ASC-Driver_Handset WinDriver 2.01.00.00.exe`

按照这个名字找到一堆“这类商家“分享的网盘链接：

![](../_assets/media/tasker-messenger/pan_1.jpg)

还有一个专门卖解锁码软件的QQ，稍微逆向了一下发现这些（或者说这种软件）似乎最后都是要把结果发回服务器计算结果的，emmmmmm很明显算法被搞了~~内部搞事情~~（所以都这样了华为你还关解锁通道NM$L）

反正，**淘宝花了20块钱买到了解锁码**

#### 刷！

接下来就是刷机了，虽然说已经有准备了，但是我还是没有想到能有这么恶心

因为华为本身自己的东西太多，在[Treble](https://source.android.com/devices/architecture)项目之前，每个自定义的ROM都有一个底包要求，**也就是说我要重新把我辛辛苦苦降级的系统再升级回去**（我……）

我升到`EMUI5`以后直接`XDA`找了一个Android 7的包刷了，明显的，这破东西文件系统还有各种坑，就不吐槽了

最主要是，我一开始一直进不去`TWRP`

一般来说，系统会有`Recovery` 和 `Bootloader/fastboot`，然而华为还有一个`Emergency Recovery`：这个东西会在启动的时候在OEM解锁警告界面按下键进入。**也就是你要在解锁警告界面过后按才可以进去`Recovery`**然而这个时间不是很好把握，正常进系统会把TWRP抹掉替换为原来的`Recovery`
我真是%…&*，反正试了几次以后总算进去了。~~抄水果的网络系统还原也不用这样吧草~~

反正刷机后电池续航之类的我不指望了，一直插着电。相机也能用，`Vendor`其实还是华为的，~~真的是自我安慰呢~~

**这辈子再碰华为我吃屎（~~不会真香的~~）**

## 2nd

之前 Google Pay 新用户激活送了 20 刀一直没有用，直接先拿来买了一个[Tasker](https://play.google.com/store/apps/details?id=net.dinglisch.android.taskerm)，类似于水果的捷径，但是更强大

项目配置：https://github.com/NeverBehave/Tasker-Messenger

具体怎么用我就不说了，但是有一点东西可以注意一下：

- 建议使用`Javascript let`在提交数据前`encode`一下数据（`JSON`, `URLENCODE`），这样子方便很多
	- `global()`获得变量
	- 声明的变量在后续操作里面使用`%{VAR}`可以获取到

至于说摄像头的话：https://alfred.camera

### 小插曲

一开始没刷系统的时候我是想用`Google Assistant`替换掉华为的语音助手，但是华为有两个问题：

- 只有他的语音助手可以唤醒屏幕，其他的都不行
- 设置了锁屏密码以后不能还原到没有密码的状态（……）， 然后谷歌还不能解锁手机

于是乎，有种操作就是用`tasker`监控`HiVoice`（华为语音助手）的窗口，弹出的时候唤醒谷歌助手：https://www.xda-developers.com/enable-ok-google-always-on-hotword-detection-on-huawei-honor-phones-no-root/

但是我后面不小心设置了锁屏密码，于是谷歌又炸了

## 这就没了？

其实是有的：

<TelegramEmbed link="ButNothingHappened/2823" />

研究了一下`SIM`模块，发现……真的贵：至少国外的价格不便宜，~~想要找人从国内带开发板~~，于是就先这样了

<script>
import TelegramEmbed from 'vue-telegram-embed'

export default {
    components: {
        TelegramEmbed
    }
}
</script>