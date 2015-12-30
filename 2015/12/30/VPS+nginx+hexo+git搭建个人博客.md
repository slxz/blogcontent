# VPS上Nginx+Hexo+Git搭建个人博客

------

最近在VPS上利用Hexo搭了个个人博客，以下做记录

## VPS
在搬瓦工上租了个VPS，现在的博客就是跑在这个VPS上，速度还是可以的，以下是VPS的信息：
> * VPS服务商：[搬瓦工](https://bandwagonhost.com)
> * 机房选择：亚利桑那州(Arizona)
> * 选择原因：便宜(19美元/年)，能用支付宝

VPS默认的操作系统是Centos6，又需要的可以自己另换

### VPS的需要注意：
> * 如果要通过SSH管理VPS的话，需要去KiwiVM面板的`Root password modification`菜单下生成个新的root密码，使用SSH工具连接的时候就可以以root为登录名，生成的密码为密码连接了。
> * VPS直接集成了`Shadowsocks`。选择`Shadowsocks Server`菜单进行相关操作即可。

## Nginx
本来装完了Shadowsocks后能翻墙就满足了，结果发现内存，硬盘都还有很多富余，于是就打算把本来要挪到Github上的博客直接放到这里得了。

### 安装
按照[Nginx官网](http://nginx.org/)介绍的来，使用最方便的yum安装。
在`/etc/yum.repos.d`路径下添加新建`nginx.repos`文件，添加如下内容：
```shell
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/6/$basearch
gpgcheck=0
enable=1
```
使用`yum install nginx`来安装即可。

### 配置
安装完成后，可以使用`rpm -qal nginx`查看Nginx安装在了那些目录，在显示的目录上就可以发现default.conf文件，默认的配置就是在这里的
因为是个人的博客，都是些静态页面，这里我就直接修改default.conf文件。这里列出来主要修改的地方，其他的用`...`省略。
```shell
server {
	...
    location / {
        root   /usr/share/nginx/html/blog;
        index  index.html index.htm;
    }
	...
}
```
然后把Hexo生成的`public`文件夹下所有文件都传到`/usr/share/nginx/html/blog`下。这样，配置就好了

### 启动
使用命令`start service nginx`即可启动nginx，输入VPS的IP就能看到自己的博客了。

## Hexo
Hexo这里就不介绍了！

## 域名
本来是不打算弄域名的，但是总是通过IP来访问也不好，记不住，看起来也不雅观。所以还是打算弄个域名。
国内免费域名都是幌子，国外老牌免费域名网站半天刷不出来什么东西，注册更是难，半天不动的。
果断回国内，在万网注册个。连带着域名+云解析目前是25元一年。明年打算再换个域名。

## Git
一开始用xftp上传静态网页的时候就觉得好难了，慢，卡，而且一旦修改的话具体生成了那些文件也不清楚，如果都传的话实在有点慢。很自然的，应该在VPS上搭个Git服务器想法就出来了。

