title: Inflate���벼���ļ�
date: 2016-01-12
tags: Android, View, ����
---
## ��Ҫ����
����������Ҫ����һ���Ի�����ʹ������Ŀ���Ѿ��ṩ���Զ���Ի��򣬴�����һ����Ƕ��ListView�Ĳ��֣������������ô���ò��֣�ListView��Զ����ֻ��ʾ����һ��Item��һ���ؼ���

## �������
ͨ��׷�ٴ��뷢�֣�����Զ��嵯����ĸ������д���һ��ScrollView����ˣ�һ��ʼ����Ϊ��ScrollView��Ƕ��ListView�����⣬���Ǽ�����ListView����Item��������̬���ɸ߶ȵĴ��룬������ǲ������á�
��̬����ListView�߶ȵĲ��ִ��룬ʵ��ʹ�����Լ��������
```java
// 1����ȡadapter
adapter = listView.getAdapter();
// 2��ͨ������adapter��ÿ����Itemview�����������Item�ĸ߶ȣ����ۼ�������и߶�
for(int i:apater.getCount()){
	itemView = adapter.getView(i, null, listView);
	itemView.measure(0, 0);
	totalHeight += itemView.getMeasuredHeight();
}
// 3������listView��layoutParams
ViewGroup.LayoutParams params = listView.getLayoutParams();
params.height = totalHeight + (listView.getDividerHeight() * listViewCount)
lv.setLayoutParams(params);
```
������ǣ�֮�����޷�����

