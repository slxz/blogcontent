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
������ǣ�֮�����޷����У��ٸ����룬������������`addView(int, LayoutParams)`���������view���뵽�����У�LayoutParams��ʼ������`MATCH_PARENT`��
�����Ļ�����ȻListView�Ƕ�̬���ø߶ȣ����Ǵ�������view���Ǹ��ݸ��ؼ�������ġ��������ֻҪ��addView��LayoutParams����Ҳ�Ƕ�̬�����õļ��ɽ���ˡ�

## ���ѧϰ
Inflate���벼���ļ�
```java
View mainView = LayoutInflater.from(context).inflate(int layoutid, ViewGroup root); // #1
LayoutInflater.from(context).inflate(int layoutid, ViewGroup rootview, boolean attachroot); // #2
```
1��ʵ�ʵ��õľ���2���ķ�����`inflate(resource, root, root != null)`
��һ����������Ҫ���ɵ�View����Դid���ڶ�����������Ҫ����view���ӵ���ViewGroup�������������Ǹ�booleanֵ������Ƿ񸽵�root�ϡ�
ͨ��Դ����Է��ֿ����������µ��ж�
| rootview=null, attachroot=false | ���ص��Ǽ�����Ҫ��view��layoutParams������Ҫ��params
| rootView!=null, attachroot=false | ���ص��Ǽ�����Ҫ��view��layoutParams��root��params
| rootView!=null, attachroot=true | ���ص��������view��root

## �ܽ�
�ȵ���һƪ[Google��Թ����](http://blog.csdn.net/lxgwm2008/article/details/36376109)����Ȼ����Ӣ�ģ����ǻ��ܿ����ġ�
������Ŀ�������inflate�ĵڶ���������null�ˣ����Լ�ʹ��֮ǰ���ú��˸߶Ⱥ��������⡣����ڴ������滹����������params��ֵ��
�Ժ�inflateʱһ��ע��Ҫ����root������Google������˵�ģ��󲿷�ʱ��Ҫroot�ģ�rootΪnull��������١���֤���������õ�Params��inflate����addviewʱ��Ҳ���ã�
���߿���������Ӧ�ϣ���root����addViewʱ����ֻ�ô�resId��û��rootʱ����addViewʱ��Ҫ����resId�Լ�LayoutParams



