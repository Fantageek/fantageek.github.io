---
author: onmyway133
comments: true
date: 2014-11-22 09:51:55+00:00
layout: post
slug: ios-dynamic-table-view-cells-with-varying-row-height-and-autolayout
title: iOS dynamic table view cells with varying row height and Autolayout
wordpress_id: 1468
categories:
- iOS
---

This post tries to explain how to achieve varying cell height with Autolayout simply and with few steps as possible. Although the title says `Table View`, it works for `Collection View` also. This works for both iOS 7 and iOS 8

The Github repo for the demo is [DynamicTableViewCellHeight](https://github.com/onmyway133/DynamicTableViewCellHeight)

The demo display a list of famous quotes, it looks like this

![](http://i57.tinypic.com/wmkqch.png)



## preferredMaxLayoutWidth



The trick to all this come from `preferredMaxLayoutWidth`. For more advanced use case, see [Auto Layout and Views that Wrap](http://devetc.org/code/2014/07/07/auto-layout-and-views-that-wrap.html)



## User Interface



I use Storyboard, but you can use xib or create from code. For how to use code, please take a look [AutoSize UITableViewCell height programmatically](http://derpturkey.com/autosize-uitableviewcell-height-programmatically/)

![](http://i58.tinypic.com/2mphr1c.png)

![](http://i60.tinypic.com/rw0xhk.png)

Here the quote label is multiple lines (Set Lines to 0). Because we have 2 labels, Autolayout does not know which will expand, which will stay the same when the cell changes size. In this case, we want the quote label to expand, so decrease its Verticle Hugging Priority and increase its Content Compression Resistance Priority

<!-- more -->

See [Cocoa Autolayout: content hugging vs content compression resistance priority](http://stackoverflow.com/a/23203345/1418457)

Note 1: This post [Dynamic Table View Cell Height and Auto Layout](http://www.raywenderlich.com/73602/dynamic-table-view-cell-height-auto-layout) tells that we should give the labels its intrinsic content priority to 1000 and set Intrinsic Size to Placeholder. I see that it is not needed

Note 2: You must add constraint to the TableViewCell `contentView`
Note 3: For the CollectionViewCell in Interface Builder, you won't see the contentView, but you are really interacting with the contentView. See



## Cell



`QuoteTableViewCell.h`
[code language="objc"]
@interface QuoteTableViewCell : UITableViewCell
@property (weak, nonatomic) IBOutlet UILabel *numberLabel;
@property (weak, nonatomic) IBOutlet UILabel *quoteLabel;

@end
[/code]

`QuoteTableViewCell.m`
[code language="objc"]
@implementation QuoteTableViewCell

// (1)
- (void)setBounds:(CGRect)bounds
{
    [super setBounds:bounds];

    self.contentView.frame = self.bounds;
}

- (void)layoutSubviews
{
    [super layoutSubviews];

    // (2)
    [self.contentView updateConstraintsIfNeeded];
    [self.contentView layoutIfNeeded];

    // (3)
    self.quoteLabel.preferredMaxLayoutWidth = CGRectGetWidth(self.quoteLabel.frame);
}

@end
[/code]

(1) It is because of [Auto Layout in UICollectionViewCell not working](http://stackoverflow.com/questions/25804588/auto-layout-in-uicollectionviewcell-not-working)

(2, 3) As explained [AutoSize UITableViewCell height programmatically](http://derpturkey.com/autosize-uitableviewcell-height-programmatically/)



<blockquote>
  Make sure the contentView does a layout pass here so that its subviews have their frames set, which we need to use to set the preferredMaxLayoutWidth below.
  
  Set the preferredMaxLayoutWidth of the mutli-line bodyLabel based on the evaluated width of the label's frame, as this will allow the text to wrap correctly, and as a result allow the label to take on the correct height.
</blockquote>



Note 1: You only need to call `[self.contentView layoutIfNeeded]`. 
Note 2: If there are some reasons that you make changes to some constraint (for example, make the quote label bottom constraint to 5), you need to call `[self.contentView updateConstraintsIfNeeded]`
Note 3: If there is `[self.contentView updateConstraintsIfNeeded]`, it must be called before `[self.contentView layoutIfNeeded]`
Note 3: There is no needs for `[self.contentView setsNeedLayout]` or `[self.contentView setsNeedUpdateConstraints]`

Take a look at these
[setNeedsLayout vs. setNeedsUpdateConstraints and layoutIfNeeded vs updateConstraintsIfNeeded](http://stackoverflow.com/questions/20609206/setneedslayout-vs-setneedsupdateconstraints-and-layoutifneeded-vs-updateconstra)
[What is the difference between all these Auto Layout update methods? Are all necessary?](http://stackoverflow.com/questions/20305654/what-is-the-difference-between-all-these-auto-layout-update-methods-are-all-nec)



## ViewController



`ViewController.m`
[code language="objc"]
#define SYSTEM_VERSION                              ([[UIDevice currentDevice] systemVersion])
#define SYSTEM_VERSION_GREATER_THAN_OR_EQUAL_TO(v)  ([SYSTEM_VERSION compare:v options:NSNumericSearch] != NSOrderedAscending)
#define IS_IOS8_OR_ABOVE                            (SYSTEM_VERSION_GREATER_THAN_OR_EQUAL_TO(@"8.0"))

@interface ViewController () <UITableViewDataSource, UITableViewDelegate>

@property (weak, nonatomic) IBOutlet UITableView *tableView;
@property (nonatomic, strong) NSArray *items;
@property (nonatomic, strong) QuoteTableViewCell *prototypeCell;

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    [self setupTableView];
    [self loadData];
}

- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}

#pragma mark - Setup
- (void)setupTableView
{
    self.tableView.dataSource = self;
    self.tableView.delegate = self;
}

#pragma mark - Data
- (void)loadData
{
    NSString *plistPath = [[NSBundle mainBundle] pathForResource:@"quotes" ofType:@"plist"];
    self.items = [[NSArray alloc] initWithContentsOfFile:plistPath];

    [self.tableView reloadData];
}

#pragma mark - PrototypeCell
// (4)
- (QuoteTableViewCell *)prototypeCell
{
    if (!_prototypeCell) {
        _prototypeCell = [self.tableView dequeueReusableCellWithIdentifier:NSStringFromClass([QuoteTableViewCell class])];
    }

    return _prototypeCell;
}

#pragma mark - Configure
- (void)configureCell:(QuoteTableViewCell *)cell forRowAtIndexPath:(NSIndexPath *)indexPath
{
    NSString *quote = self.items[indexPath.row];

    cell.numberLabel.text = [NSString stringWithFormat:@"Quote %ld", (long)indexPath.row];
    cell.quoteLabel.text = quote;
}

#pragma mark - UITableViewDataSouce
- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView
{
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
{
    return self.items.count;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    QuoteTableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:NSStringFromClass([QuoteTableViewCell class])];

    [self configureCell:cell forRowAtIndexPath:indexPath];

    return cell;
}

#pragma mark - UITableViewDelegate
// (5)
- (CGFloat)tableView:(UITableView *)tableView estimatedHeightForRowAtIndexPath:(NSIndexPath *)indexPath
{
    return UITableViewAutomaticDimension;
}

- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath
{
    // (6)
    if (IS_IOS8_OR_ABOVE) {
        return UITableViewAutomaticDimension;
    }

    // (7)
    //self.prototypeCell.bounds = CGRectMake(0, 0, CGRectGetWidth(self.tableView.bounds), CGRectGetHeight(self.prototypeCell.bounds));

    [self configureCell:self.prototypeCell forRowAtIndexPath:indexPath];

    // (8)
    [self.prototypeCell updateConstraintsIfNeeded];
    [self.prototypeCell layoutIfNeeded];

    // (9)
    return [self.prototypeCell.contentView systemLayoutSizeFittingSize:UILayoutFittingCompressedSize].height;

}

@end
[/code]

(4) The prototype cell is never displayed, it is used to layout a cell and determine the required height

(5) You can use UITableViewAutomaticDimension or specify a reasonable estimated height

(6) iOS 8 Auto sizing cell feature needs UITableViewAutomaticDimension

(7) I have met a case when the I dequeue the cell, the cell size is always zero!!! So if that is the case, we needs to set the bounds for the cell (or more precisely, the width) so that the layout and update constraints steps will work. It also helps in orientation situation. See [Table View Cells With Varying Row Heights](http://useyourloaf.com/blog/2014/02/14/table-view-cells-with-varying-row-heights.html) for Issue With Device Orientation Changes (Updated 17 March 2014)

(8) Same as (2), but for the cell



<blockquote>
  Do the layout pass on the cell, which will calculate the frames for all the views based on the constraints (Note that the preferredMaxLayoutWidth is set on multi-line UILabels inside the -`layoutSubviews` method in the UITableViewCell subclass
</blockquote>



(9) You must call it on the `contentView`



## Reference







  1. [Table View Cells With Varying Row Heights](http://useyourloaf.com/blog/2014/02/14/table-view-cells-with-varying-row-heights.html)


  2. [Auto layout constraints issue on iOS7 in UITableViewCell](http://stackoverflow.com/questions/19132908/auto-layout-constraints-issue-on-ios7-in-uitableviewcell)


  3. [Table View Cells With Varying Row Heights](http://stackoverflow.com/questions/18746929/using-auto-layout-in-uitableview-for-dynamic-cell-layouts-variable-row-heights)


  4. [AutoSize UITableViewCell height programmatically](http://derpturkey.com/autosize-uitableviewcell-height-programmatically/)


  5. [Self Sizing Table View Cells](http://useyourloaf.com/blog/2014/08/07/self-sizing-table-view-cells.html)


  6. [Dynamic Table View Cell Height and Auto Layout](http://www.raywenderlich.com/73602/dynamic-table-view-cell-height-auto-layout)


  7. [Auto Layout in UICollectionViewCell not working](http://stackoverflow.com/a/25820173/1418457)


  8. [iOS8 Day-by-Day :: Day 5 :: Auto-sizing table view cells](http://www.shinobicontrols.com/blog/posts/2014/07/24/ios8-day-by-day-day-5-auto-sizing-table-view-cells)


  9. [iOS8 Day-by-Day :: Day 37 :: Autosizing Collection View Cells](http://www.shinobicontrols.com/blog/posts/2014/10/28/ios8-day-by-day-day-37-autosizing-collection-view-cells)


