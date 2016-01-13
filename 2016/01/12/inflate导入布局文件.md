title: Inflate导入布局文件
date: 2016-01-12
tags: Android, View, 布局
---
## 主要问题
开发过程中要弹出一个对话框，我使用了项目中已经提供的自定义对话框，传入了一个镶嵌了ListView的布局，结果不管我怎么设置布局，ListView永远都是只显示单独一个Item的一个控件。

## 问题分析
通过追踪代码发现，这个自定义弹出框的父布局中存在一个ScrollView。因此，一开始我以为是ScrollView来嵌套ListView的问题，于是加入了ListView根据Item个数来动态生成高度的代码，结果还是不起作用。
动态计算ListView高度的部分代码，实际使用请自己补齐代码
```java
// 1、获取adapter
adapter = listView.getAdapter();
// 2、通过调用adapter的每个子Itemview，计算这个子Item的高度，并累加算出所有高度
for(int i:apater.getCount()){
	itemView = adapter.getView(i, null, listView);
	itemView.measure(0, 0);
	totalHeight += itemView.getMeasuredHeight();
}
// 3、设置listView的layoutParams
ViewGroup.LayoutParams params = listView.getLayoutParams();
params.height = totalHeight + (listView.getDividerHeight() * listViewCount)
lv.setLayoutParams(params);
```
意外的是，之后还是无法运行，再跟代码，发现这里用了`addView(int, LayoutParams)`来将传入的view加入到布局中，LayoutParams初始化都是`MATCH_PARENT`。
这样的话，虽然ListView是动态设置高度，但是传过来的view还是根据父控件来计算的。因此这里只要让addView的LayoutParams参数也是动态的设置的即可解决了。

## 相关学习
Inflate导入布局文件
```java
View mainView = LayoutInflater.from(context).inflate(int layoutid, ViewGroup root); // #1
LayoutInflater.from(context).inflate(int layoutid, ViewGroup rootview, boolean attachroot); // #2
```
1处实际调用的就是2处的方法：`inflate(resource, root, root != null)`
第一个参数是你要生成的View的资源id，第二个参数是你要将个view附加到的ViewGroup，第三个参数是个boolean值，标记是否附到root上。
通过源码可以发现可以做出以下的判断
| 参数 | 说明 |
| ---- | ----: |
| rootview=null, attachroot=false | 返回的是即是你要的view，layoutParams即是你要的params |
| rootView!=null, attachroot=false | 返回的是即是你要的view，layoutParams是root的params |
| rootView!=null, attachroot=true | 返回的是添加了view的root |

## 总结
先递上一篇[Google的怨妇文](http://blog.csdn.net/lxgwm2008/article/details/36376109)，虽然都是英文，但是还能看懂的。
我们项目中这里的inflate的第二个参数传null了，所以即使我之前设置好了高度后还是有问题。因此在代码里面还得重新设置params的值。
以后inflate时一定注意要加上root，就像Google文章中说的，大部分时候都要root的，root为null的情况很少。保证布局中设置的Params在inflate或者addview时候也能用！
~~或者可以这样对应上：有root，则addView时可以只用传resId；没有root时，则addView时需要传入resId以及LayoutParams~~
修正下，inflate当rootview不是null时，不能与addView一起共用！
因为addView中调用的addViewInner中会判断添加的View的parent是否已经为空，否则会报IllegalStateException。



