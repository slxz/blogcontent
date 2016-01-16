title: View的measure以及invalidate
date: 2016-01-16
tags: Android, View, 布局
---

## View的measure(int widthMeasureSpec, int heightMeasureSpec)
之前有总结ListView生成每个Item的高度，然后根据每个Item的高度来动态生成ListView的高度，
其中获取子View的实际宽高的方法是：
```java
...
itemView.measure(0, 0);
height = itemView.getMeasuredHeight()
...
```
这样两句代码就能获得该View的高度，现在就来解释下。

View的measure方法中调用的就是OnMeasure(int, int)方法。传入的参数就是measure的参数。因此，如字面意思，measure就是用来测量这个View的长宽的，默认的这个测量好之后的值释放到`View.mMeasuredWidth`和`View.mMeasuredHeight`中
在View类的默认OnMeasure中，调用了getDefaultSize来获得宽高。
```java
public static int getDefaultSize(int size, int measureSpec){
	int result = size;
	int specMode = MeasureSpec.getMode(measureSpec);
	int specSize = MeasureSpec.getSize(measureSpec);
	switch(specMode){
		case MeasureSpec.UNSPECIFIED: 	// 0 << 30
			result = size;
			break;
		case MeasureSpec.AT_MOST: 		// 1 << 30
		case MeasureSpec.EXACTLY:		// 2 << 30
			result = specSize;
			break;
	}
	return result;
}
```
这里就是一个把传入的measureSpec解析成Mode以及Size，根据Mode来确定应该是获取size还是measureSpec中传入的size的过程。
我们通过measure传进来的参数就应该是Mode+Size的形式，而Mode如果为`MeasureSpec.UNSPECIFIED`则会直接使用系统推荐的尺寸，忽略了Size，这个推荐是尺寸是与View的最小宽高或者是背景的最小宽高，取这其中的较大的一个。
因此，我们之前使用measure(0,0)实际就是使用measure(MeasureSpec.UNSPECIFIED+0, MeasureSpec.UNSPECIFIED+0)的形式了。

## View的重绘invalidate

### 推荐阅读
#### [Android中invalidate() 函数详解(结合Android 4.0.4 最新源码)](http://blog.csdn.net/zjmdp/article/details/7713209)
#### [Android View刷新机制](http://blog.csdn.net/chenzhiqin20/article/details/8628952
#### [Android学习 之 获取可视区域的Rect对象(顺带获取状态栏和标题栏高度的方法)](http://blog.csdn.net/fengkuanghun/article/details/7481222)
### 总结
1、view的重绘的执行逻辑都是一层一层向父View传递的。最终由ViewRoot来执行相关区域的重绘的。这个区域以Rect的形式来保存的。重绘区域是子View与对应父View的交集部分。
2、ViewRoot是由DecorView向WindowMangerImp注册时产生的。在这个ViewRoot中所有的View都使用同一个AttachInfo的。

