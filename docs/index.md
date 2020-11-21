# Electron+Vue从零开始打造一个本地文件翻译器
又一个愉快的周末快到了，当我脚步轻快的回到家，准备拥抱女盆友的时候，却发现，女盆友愁眉苦脸，望着电脑上一堆英文文件夹，不断的唉声叹气并嘟喃道:"好累啊"。于是，我凑近一看，只见她电脑上一堆英文文件夹，不断的重复着复制文件名，然后放百度翻译里翻译成中文，然后又把翻译后的结果给文件重命名，这也太难受了吧。想到女盆友有难，我作为她的程序猿男朋友，怎么能袖手旁观，我陷入了沉思，突然想到用Node不就能做到她手上的那些操作吗，于是说做就做，我立马把女盆友抛在身后，打开电脑开始行动，毕竟女人只会影响我拔剑的速度。

## 项目搭建
项目搭建依旧使用的是老组合，**Electron + Vue + Node**，这次就不讲怎么整合electron和vue了，具体可看 [Electron+vue从零开始打造一个本地音乐播放器](https://juejin.cn/post/6887964816237920263 "Electron+vue从零开始打造一个本地音乐播放器")这篇文章。懒人可通过克隆我的模板文件直接开发，[戳这里戳这里](https://github.com/Kerinlin/simple-electron-vue-template "戳这里戳这里").

## 项目功能
明确要解决的几个痛点：
- 要能自动翻译
- 要能将翻译好的名字自动重命名
- 要能批量翻译
- 希望能操作简单

需求明确了，下面咱们一步一步来逐个解决。

## 项目实现
项目实现并不复杂，逐一解决，有的放矢。

### 前置工作

测试过有道翻译，百度翻译，谷歌翻译。经过对比，最终选择了百度翻译，百度翻译的通用翻译每月200万字符的免费，（QPS=10）还是很符合我的需求的。
[![DMVOc6.png](https://s3.ax1x.com/2020/11/19/DMVOc6.png)](https://imgchr.com/i/DMVOc6)

#### 注册申请翻译服务
要使用翻译服务需要去[百度翻译开放平台](http://api.fanyi.baidu.com/api/trans/product/apichoose "百度翻译开放平台")申请使用权限，选择通用翻译服务就可以了，注册成为开发者后，新建项目获取appid,这个appid很重要，决定了是否能正确发起翻译请求。

[![D1LVAK.png](https://s3.ax1x.com/2020/11/21/D1LVAK.png)](https://imgchr.com/i/D1LVAK)

#### 拼接翻译API

通过查看文档可知，通用翻译API HTTP地址为

`http://api.fanyi.baidu.com/api/trans/vip/translate`

可携带下面这些参数
[![D1jFns.png](https://s3.ax1x.com/2020/11/21/D1jFns.png)](https://imgchr.com/i/D1jFns)

由于安全限制，需要生成签名，签名需要 按照 appid+q+salt+密钥 的顺序拼接得到字符串,然后对字符串进行md5加密

      const salt = parseInt(Math.random() * 1000000000);
      const sign = md5(globalData.appid + q + salt + globalData.key);
