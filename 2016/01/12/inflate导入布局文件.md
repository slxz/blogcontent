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
意外的是，之后还是无法运行

