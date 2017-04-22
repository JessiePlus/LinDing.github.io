---
layout: post 
title:  "Masonry进阶用法"
excerpt: "Masonry进阶用法"
date:   2017-04-22 10:10:03 +0800
categories: 自动布局
---

Masonry进阶用法
==

1. UIEdgeInsets的使用
--
	//相对于父视图边距为10
	UIEdgeInsets padding = UIEdgeInsetsMake(10, 10, 10, 10);
	[self.avatarView mas_makeConstraints:^(MASConstraintMaker *make) {
	    make.top.equalTo(superview.mas_top).with.offset(padding.top); //with is an optional semantic filler
	    make.left.equalTo(superview.mas_left).with.offset(padding.left);
	    make.bottom.equalTo(superview.mas_bottom).with.offset(-padding.bottom);
	    make.right.equalTo(superview.mas_right).with.offset(-padding.right);
	}];
	
	//相对于父视图边距为10简洁写法
	[self.avatarView mas_makeConstraints:^(MASConstraintMaker *make) {
	    make.edges.equalTo(superview).with.insets(padding);
	}];
2. Priority的使用
--
	[self.avatarView mas_makeConstraints:^(MASConstraintMaker *make) {
	    make.left.greaterThanOrEqualTo(self.view.mas_left).with.priorityLow();
	    make.top.equalTo(self.view.mas_top).with.priority(600);
	}];


3. 修改返回的MASConstraint
--
	MASConstraint *widthConstraint;
	[view mas_makeConstraints:^(MASConstraintMaker *make) {
		widthConstraint = make.width.equalTo(@(imageViewSize.width));
	}];
	width.equalTo(@20);
4. NSLayoutConstraint搭配MASConstraint使用
--
    UIView *containerView;
    NSLayoutConstraint *widthConstraint;//containerView的约束
	[view mas_makeConstraints:^(MASConstraintMaker *make{
	    make.width.equalTo(containerView.mas_width).multipliedBy(0.5);
    }];
5. 动态计算UITableViewCell高度
--

	@interface DLFeedsViewController ()<UITableViewDelegate, UITableViewDataSource>
	//用户信息列表
	@property (nonatomic) UITableView *feedsListView;
	@property (nonatomic) NSMutableArray <DLFeed *>*feedsList;
	//计算高度
	@property (nonatomic, strong) UITableViewCell *templateCell;
	@end

	@implementation DLFeedsViewController
	-(CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(nonnull NSIndexPath *)indexPath {
	    
	    NSInteger section = indexPath.section;
	    NSInteger row = indexPath.row;

	    DLFeed *feedInfo = self.feedsList[row];
	    
	    DLFeedInfoCell *cell = (DLFeedInfoCell *)self.templateCell;
	    [cell.nickNameLab setText:feedInfo.nickName];
	    [cell.msgContentLab setText:feedInfo.msgContent];
	
	    CGFloat cellHeight = [cell.contentView systemLayoutSizeFittingSize:UILayoutFittingCompressedSize].height + 0.5f;;
	    
	    return cellHeight;
	
	}
	@end
6. 使用mas_topLayoutGuide和mas_bottomLayoutGuide处理导航栏和标签栏
--
	make.top.equalTo(self.mas_topLayoutGuide);
	make.bottom.equalTo(self.mas_bottomLayoutGuide);
7. Baseline
--
baseline，翻译过来就是“基线”，在Autolayout里面对应着 NSLayoutFormatAlignAllBaseline ，也是一种对齐的标准。  
例如，UIButton的baseline就是内部的文字。对于自定义的View来说，baseline默认就是整个view的底部，如果想改变baseline的话，可以重写UIView的 viewForBaselineLayout ，返回当作baseline的view即可。  
8. 仿Twitter头部缩放效果
--
	@property (nonatomic) UIImageView *headerImageView;
	@property (strong, nonatomic) MASConstraint * headerImageViewHeightConstraint;

	-(UIImageView *)headerImageView {
		if(!_headerImageView) {
		_headerImageView = [[UIImageView alloc] init];
		    _headerImageView.contentMode = UIViewContentModeScaleAspectFill;
		
		}
		return _headerImageView;
	}

    [self. headerImageView mas_makeConstraints:^(MASConstraintMaker *make) {
        make.left.and.right.equalTo(self.view);
        make.top.equalTo(self.mas_topLayoutGuideBottom);
        _headerImageViewHeightConstraint = make.height.equalTo(@100);
    }];
	- (void)scrollViewDidScroll:(UIScrollView *)scrollView {
	    if (scrollView.contentOffset.y < 0) {
	        self.headerImageViewHeightConstraint.equalTo(@(100 - scrollView.contentOffset.y));
	    } else {
	        self.headerImageViewHeightConstraint.equalTo(@100);
	    }
	}

9. `UITableView`的`beginUpdates``endUpdates` 
--
10. `MASConstraint`的`activate``deactivate``install``uninstall`方法
--
11. 优先级的用法
--
可以通过设定不同约束的优先级的方法来处理约束冲突问题

	@property (nonatomic, strong) MASConstraint *leftConstraint;
	 // 优先级要比边界条件低
	_leftConstraint = make.centerX.equalTo(_containerView.mas_left).with.offset(50).priorityHigh();
12. 高级调试技巧
--
* 不可满足的约束条件
遇到不可满足的约束条件只能在输入的日志中看到视图的内存地址。  
```
	(lldb) po 0x7731880
```
```
	$0 = 124983424 <UIView: 0x7731880; frame = (90 -50; 80 100);
	layer = <CALayer: 0x7731450>>
```
```
	(lldb) po [0x7731880 superview]  
```
```
	$2 = 0x07730fe0 <UIView: 0x7730fe0; frame = (32 128; 259 604);
	layer = <CALayer: 0x7731150>>
```
```
	(lldb) po [[0x7731880 superview] recursiveDescription]  
```
```
	$3 = 0x07117ac0 <UIView: 0x7730fe0; frame = (32 128; 259 604); layer = <CALayer: 0x7731150>>
	| <UIView: 0x7731880; frame = (90 -50; 80 100); layer = <CALayer: 0x7731450>>
	| <UIView: 0x7731aa0; frame = (90 101; 80 100); layer = <CALayer: 0x7731c60>>
```
可以在控制台修改有问题的视图  
```
	(lldb) expr ((UIView *)0x7731880).backgroundColor = [UIColor purpleColor]  
```
这里有[Daniel](https://twitter.com/danielboedewadt)的一个调试Auto Layout的范例：<https://github.com/objcio/issue-3-auto-layout-debugging>
* 有歧义的布局
13. 
--
