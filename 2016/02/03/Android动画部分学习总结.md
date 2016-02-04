title: Android动画部分学习总结
date: 2016-02-03
tags: Android, Android View, ValueAnimator, ObjectAnimator
---
这里将主要记录属性动画的相关问题，即主要介绍：`ValueAnimator`和`ObjectAnimator`。
控件通过属性动画，控件的属性是实时变化的，而通过补间动画则不是。如使用补间动画移动了一个按钮，但是按钮的点击区域是不会变的。
### ValueAnimator
先给出一般的使用方法吧：
```java
ValueAnimator animator = ValueAnimator.ofInt(originalHeight, 1).setDuration(mAnimationTime);
animator.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
    @Override
    public void onAnimationUpdate(ValueAnimator valueAnimator) {
        lp.height = (Integer) valueAnimator.getAnimatedValue();
        mView.setLayoutParams(lp);
    }
});
animator.start();
```
ValueAnimator是通过限定两个数（或者是一组数），按照类似微分方式，从开始的数按照顺序渐变到另一个数，在设置的时间内，按序遍历所有的数。
也就是说它是描述动画过程的，每次的变化通过`AnimatorUpdateListener`来回调。通过这样的抽象，从而保证了`ValueAnimator`能够适用基本上任何的想要的渐变模式了，不仅仅是动画！

### ObjectAnimator
`ObjectAnimator`是`ValueAnimator`的一层封装，
```java
originalTextSize = title.getTextSize();
oa = ObjectAnimator.ofFloat(title, "TextSize", DisplayUtil.px2sp(AnimaTestFragment.this.getActivity(), originalTextSize), 5f, DisplayUtil.px2sp(AnimaTestFragment.this.getActivity(),originalTextSize));
oa.setDuration(5000);
oa.addListener(new Animator.AnimatorListener() {
	...
});
```
这里是对文字大小进行动画的代码。`ObjectAnimator`并不是对对象的属性单纯进行变化，而是通过调用对象的setXXX和getXXX来进行对属性的操作的。也就是说，即使该对象没有这个属性值，只要有set和get方法，都是可以使用`ObjectAnimator`的。
因此这里当在改TextSize时，因为View.getTextSize是返回的以像素（px）单位，而setTextSize传入的参数是以sp为单位的，因此这里需要进行转换。

### AnimatorSet
顾名思义，这个就是一个动画集合，看看用法就应该知道是什么东西了。
```java
AnimatorSet as = new AnimatorSet();
as.play(oa).with(va);
//as.setDuration(5000);
as.start();
```
这里的setDuration，如果动画自己设定了时间，而AnimatorSet没有设定时间，则按照动画定义的时间来，如果AnimatorSet设置了时间则按照AnimatorSet的时间来。

动画可以通过xml来定义，这个就暂时略过吧。

### 进阶
进阶的话就要介绍下`TypeEvaluator`，这里有篇[文章](http://blog.csdn.net/jdsjlzx/article/details/45558901)，比较值得学习的！
这里`TypeEvaluator`就是之前讲的渐变中负责渐变处理的，也正是提供了这个接口，属性动画才能真正做到不仅仅是针对单纯的动画。

### 补充
通过`setInterpolator`方法，属性动画还可以设置速率，动画变化速率。
> * AccelerateDecelerateInterpolator，动画开始与结束的地方改变的比较慢，在中间时候加速
> * AccelerateInterpolator，在动画开始的地方速率改变的慢，然后开始加速
> * CycleInterpolator，动画循环播放特定的次数，速率改变沿着正弦曲线
> * DecelerateInterpolator，在动画开始的地方速率改变的比较慢，然后开始减速
> * LinearInterpolator，动画匀速改变
示例：anim.setInterpolator(new LinearInterpolator());


