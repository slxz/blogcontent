title: Ubuntu 声音修复
date: 2016-10-29
tags: Ubuntu
---
#### 今天准备自己实现下apk的简单加固技术，打开Ubuntu后提示更新，等到更新完成之后发现没有声音了，之前我都是直接使用`Sound Switcher Indicator`直接更改下输出就好了，结果今天发现这里没有常用的输出项，有种不好的预感。试了之后，果然前面板上的耳机没有声音了，于是又开始解决这个问题了。
#### 一开始试了很多方法，虽然都没有用，但是这其中有个有用的命令还是得记录下：
> * `aplay -l`,显示PLAYBACK硬件清单，感觉就是显示主要输出硬件和芯片。

> * `pacmd list-cards` 显示所有音频输出芯片。
> * `/etc/pulse/default.pa` 配置硬件使用

#### 最后安装了`pavucont`来及解决了这个问题，以后要修改音频输出的话可以直接使用这个命令打开软件来配置

#### 具体的相关链接：
> * http://www.linuxdiyf.com/linux/22936.html
