# 自定义 Django 文件存储类

> 思考：
* 下图首页页面中图片无法显示的原因。

<img src="/goods/images/43首页广告渲染结果（无图片）.png" style="zoom:35%">

<img src="/goods/images/44首页广告页面源码.png" style="zoom:50%">

<img src="/goods/images/45首页广告渲染源码.png" style="zoom:50%">

<img src="/goods/images/42广告内容.png" style="zoom:40%">

> 结论：
* 通过 FastDFS 上传文件后返回的`'Remote file_id'`字段是文件索引。
* **文件索引**会被我们存储到 **MySQL 数据库** 。所以将来读取出来的也是文件索引，导致界面无法下载到图片。

> 解决：
* 重写 Django 文件存储类的 url( ) 方法。
* 在重写时拼接完整的图片下载地址（协议、IP、端口、文件索引）

### 1. Django 文件存储类 url( ) 方法介绍

<img src="/goods/images/46文件存储类url()方法.png" style="zoom:40%">

> 结论：
* 文件存储类`url()`方法的作用：返回`name`所代表的文件内容的URL。
* 文件存储类`url()`方法的触发：`content.image.url`
    * 虽然表面上调用的是 `ImageField` 的 `url` 方法。但是内部会去调用文件存储类的 `url()` 方法。
* 文件存储类`url()`方法的使用:
    * 我们可以通过自定义 Django 文件存储类达到重写 `url()` 方法的目的。
    * 自定义 Django 文件存储类必须提供 `url()` 方法。
    * 返回 name 所指的文件对应的绝对 URL。



### 经过上面的更新后, 首页界面就可以正常展示了: 

<img src="../images/QQ002.png" style="zoom:40%">