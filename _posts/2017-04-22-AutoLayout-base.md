---
layout: post 
title:  "AutoLayout的基础"
excerpt: "AutoLayout的基础"
date:   2017-04-22 10:10:03 +0800
categories: 自动布局
---

AutoLayout的基础
==
1. 常用关键字
--
1. `HuggingPriority` 阻止自己变大的优先级，值越大越固定。
2. `CompressionResistancePriority` 阻止自己变小的优先级，值越大越固定。
3. `setContentCompressionResistancePriority`通常被用来保障内容优先显示。
3. `automaticallyAdjustsScrollViewInsets` 根据按所在界面的status bar，navigationbar，与tabbar的高度，自动调整scrollview的 inset,设置为no，不让viewController调整。
4. `preferredMaxLayoutWidth`用来制定最大的宽，一般用在多行的UILabel中
5. `systemLayoutSizeFittingSize`方法能够获得UIView的高度
6. `topLayoutGuide`和`bottomLayoutGuide`，这个两个主要是方便获取UINavigationController和UITabBarController的头部视图区域和底部视图区域。

2. AutoLayout关于更新的几个方法的区别
--
1. `setNeedsLayout`告知页面需要更新，但是不会立刻开始更新。执行后会立刻调用layoutSubviews。
2. `layoutIfNeeded`告知页面布局立刻更新。所以一般都会和setNeedsLayout一起使用。如果希望立刻生成新的frame需要调用此方法，利用这点一般布局动画可以在更新布局后直接使用这个方法让动画生效。
3. `layoutSubviews`系统重写布局
4. `setNeedsUpdateConstraints`告知需要更新约束，但是不会立刻开始
5. `updateConstraintsIfNeeded`告知立刻更新约束
6. `updateConstraints`系统更新约束
7. `intrinsicContentSize`，就是获取这个View的固有的，内在的，本质的的内容大小。简单说，就是Autolayout布局系统是不知道自定义View的内容大小的，所以自定义View可以重写这个方法，返回自己的内容大小，交由Autolayout布局系统去计算布局。并且，这个大小是不能依赖外部的大小的。
8. 
