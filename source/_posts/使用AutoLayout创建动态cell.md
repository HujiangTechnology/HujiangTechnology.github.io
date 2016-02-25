title: 使用AutoLayout创建动态cell
date: 2015-11-23 16:26:13
tags: AutoLayout
categories: iOS开发
---
 **关于使用autolayout创建动态cell，网上也有不少的文章，但是里面的内容都是说的一个问题，简称换汤不换药，都是说的一些UILabel或者UITextView等等一些控件显示的文本内容不同来实现不同的高度。**

 **但是我们平常使用的自定义cell很多都是显示不同数量的控件，来显示不同的高度，比如微博首页，饿了么首页那些cell，都是下面显示不同数量的控件来显示不同高度的cell，那么下面就让我们一起看看怎么使用autolayout实现这种自定义cell**

<!--more-->

# 关于使用autolayout实现cell动态高度有两种实现方式

**iOS8以后**
iOS8以后比较爽：只需要设置下面两句代码就行了

```objc
//设置cell的估计高度
self.tableView.estimatedRowHeight = 200;

//iOS以后这句话是默认的，所以可以省略这句话
self.tableView.rowHeight = UITableViewAutomaticDimension;
```
你可能没有看到实现代理方法返回cell高度。明确告诉你iOS8以后不需要实现了，系统会自动推断出cell的高度。

# 2


**but，iOS7还是需要**

下面就是iOS7实现的方式，还是需要实现这个方法，iOS8那两句代码不写也可以

```
//如果要支持iOS7这个方法必须实现
-(CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath {

    //其他代码
    
    //自动算高度，+1的原因是因为contentView的高度要比cell的高度小1
    CGFloat height = [cell.contentView systemLayoutSizeFittingSize:UILayoutFittingCompressedSize].height + 1;

    return height;
}
```




主要就是下面这句代码，能自动计算出contentView的高度和宽度

```
CGFloat height = [cell.contentView systemLayoutSizeFittingSize:UILayoutFittingCompressedSize].height + 1;
```

上面这两种方式可以一起存在，既支持ios7也支持iOS8.


### 开始demo


先来看一下我们最终的效果：
![效果图](https://raw.githubusercontent.com/TrackyTian/TrackyTian.github.io/master/2015/11/23/%E4%BD%BF%E7%94%A8AutoLayout%E5%88%9B%E5%BB%BA%E5%8A%A8%E6%80%81cell/%E5%8A%A8%E6%80%81cell.gif)

* 有的cell只有一个UITextLabel,并且label会根据内容的多少来改变高度
* 有的cell下面还有一个UIButton，并且cell会根据有没有button来动态改变高度



实现思路
- 首先自定义一个cell，顺便创建一个xib文件
- 在xib文件中把所有包含的控件全部搞上去，设置好约束
- 如果不需要哪个控件显示就把哪个控件的高约束设置为0
- 如果需要显示哪个控件就保证这个控件没有高度约束（应该容易想的到因为是动态高度的cell，所以不能有高度的约束）
- 然后更新约束，显示cell


在这里我们使用xib的方式创建自定义cell

如图：

![](https://raw.githubusercontent.com/TrackyTian/TrackyTian.github.io/master/2015/11/23/%E4%BD%BF%E7%94%A8AutoLayout%E5%88%9B%E5%BB%BA%E5%8A%A8%E6%80%81cell/2.png)

这里设置约束有几个注意点

- 每一个控件都只设置上下左右的约束，不设置宽高的约束，有些必须要固定大小的可以设置一下
- 有些控件如果不设置宽高xib会报错的话，就设置占位符placehoder
- 像一些要消失或者显示的控件，最好设置他与其他显示的控件间距为0




这是我自定义cell的类

![](https://raw.githubusercontent.com/TrackyTian/TrackyTian.github.io/master/2015/11/23/%E4%BD%BF%E7%94%A8AutoLayout%E5%88%9B%E5%BB%BA%E5%8A%A8%E6%80%81cell/1.png)



在控制器中我们实现数据源代码

```
- (void)viewDidLoad {
    [super viewDidLoad];
    [self.tableView registerNib:[UINib nibWithNibName:@"MyTableViewCell" bundle:nil] forCellReuseIdentifier:@"cell"];
    
    self.tableView.allowsSelection = NO;
    
    //胡乱写的，为了懒省事，大家凑合着看吧
    self.tableData = @[@"1\n2\n3\n4\n5\n6", @"123456789012345678901234567890", @"1\n2", @"1\n2\n3", @"123456789012345678901234567890", @"1\n2\n3\n4\n5\n6", @"123456789012345678901234567890", @"1\n2", @"1\n2\n3", @"123456789012345678901234567890", @"1\n2\n3",@"1\n2\n3",@"123456789012345678901234567890", @"1\n2", @"1\n2\n3", @"123456789012345678901234567890", @"1\n2\n3",@"1\n2\n3"@"123456789012345678901234567890", @"1\n2", @"1\n2\n3", @"123456789012345678901234567890", @"1\n2\n3",@"1\n2\n3"@"123456789012345678901234567890", @"1\n2", @"1\n2\n3", @"123456789012345678901234567890", @"1\n2\n3",@"1\n2\n3"@"123456789012345678901234567890", @"1\n2", @"1\n2\n3", @"123456789012345678901234567890", @"1\n2\n3",@"1\n2\n3"@"123456789012345678901234567890", @"1\n2", @"1\n2\n3", @"123456789012345678901234567890", @"1\n2\n3",@"1\n2\n3"];
}


-(NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

-(NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    return self.tableData.count;
}

-(UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    MyTableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"cell"];
    
    cell.text = self.tableData[indexPath.row];
    
    return cell;
}
```


我们先使用iOS8的方法
接下来就是去自定义cell类(MyTableView.m)中重写-updateConstraints 方法

```
-(void)updateConstraints {
    //remove Constraints
    [self.height uninstall];

    //add Constraints
    [self.content mas_makeConstraints:^(MASConstraintMaker *make) {
       self.height = make.height.equalTo(@60);//any number
    }];
    
    if(self.isShowView == NO) {
        //change Height Constrains to be 0
        [self.content mas_updateConstraints:^(MASConstraintMaker *make) {
            make.height.equalTo(@0);
        }];
    }
    else {
        //remove Constraints
        [self.height uninstall];
    }
    
    [super updateConstraints];
}
```

- 如果不需要显示就设置高度约束为0
- 如果需要显示就把高度约束去掉 

这个方法超级重要，有些view需要显示的话，一定要把高度约束去掉，不然cell循环使用的话会出问题。

然后就是设置要显示的text

```
-(void)setText:(NSString *)text {
    _text=text;
    self.textView.text = text;
    
    if ([self.text isEqualToString:@"123456789012345678901234567890"]) {
        self.isShowView = NO;
        self.button.hidden=YES;
        self.content.hidden=YES;
        self.intextView.hidden=YES;
    }else {
        self.isShowView = YES;
        self.button.hidden=NO;
        self.content.hidden=NO;
        self.intextView.hidden=NO;

    }
}
```


接下来在Controller文件中
将cellForRowAtIndexPath方法改为下面样子

```
-(UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    MyTableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"cell"];
    
    cell.text = self.tableData[indexPath.row];
    
    //返回cell之前重新刷新约束，重新计算高度
    [cell setNeedsUpdateConstraints];
    [cell updateConstraintsIfNeeded];
    
    return cell;
}
```

然后在-viewDidLoad方法中增加两句话就行了

```
self.tableView.estimatedRowHeight = 200;
self.tableView.rowHeight = UITableViewAutomaticDimension;
```

****

接下来是支持iOS7的

只需要重写代码方法heightForRowAtIndexPath

将这个方法内容改为下面这样：

```
//如果要支持iOS7这个方法必须实现
-(CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath {
    self.cell = (MyTableViewCell *)[tableView dequeueReusableCellWithIdentifier:@"cell"];

    
    MyTableViewCell *cell = self.cell;
    cell.text = [self.tableData objectAtIndex:indexPath.row];
    
    //这句代码必须要有，也就是说必须要设定contentView的宽度约束。
    //设置以后，contentView里面的内容才知道什么时候该换行了
    CGFloat contentViewWidth = CGRectGetWidth(self.tableView.frame);
    [cell.contentView mas_makeConstraints:^(MASConstraintMaker *make) {
        make.width.equalTo(@(contentViewWidth));
    }];
    
    //重新加载约束,每次计算之前一定要重新确认一下约束
    [cell setNeedsUpdateConstraints];
    [cell updateConstraintsIfNeeded];
    
    //自动算高度，+1的原因是因为contentView的高度要比cell的高度小1
    CGFloat height = [cell.contentView systemLayoutSizeFittingSize:UILayoutFittingCompressedSize].height + 1;

    return height;
}
```


### 好了大概就是这样吧，具体demo点[这里](https://github.com/CoderTian/DynamicCellWithAutolayout)


