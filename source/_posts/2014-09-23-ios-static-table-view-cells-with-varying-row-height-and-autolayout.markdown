---
author: onmyway133
comments: true
date: 2014-09-23 01:59:14+00:00
layout: post
slug: ios-static-table-view-cells-with-varying-row-height-and-autolayout
title: iOS static table view cells with varying row height and Autolayout
wordpress_id: 1395
categories:
- iOS
---

In this post, I 'll show how to achieve varying row height with static tableview and autolayout



### Make sure your static cell Autolayout is setup correctly



This step is the same as dynamic table view, just make sure you add constraints to the `contentView`, not the cell



### Dynamic height



Since this is a static tableview, you don't need that `prototypeCell` trick, you can just use the IBOutlet directly. This is what happens in my `UITableViewDelegate`, note how I reference `descriptionLabel`

[code language="objc"]
- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath
{
    if (indexPath.section == 1 && indexPath.row > 0) {
        return 0;
    }

    if (indexPath.section == 0 && indexPath.row == 2) {
        CGSize size = [self.descriptionLabel systemLayoutSizeFittingSize:UILayoutFittingCompressedSize];
        return size.height;
    }

    return [super tableView:tableView heightForRowAtIndexPath:indexPath];
}
[/code]



## Note



Make you you have set `preferredMaxLayoutWidth` for the `descriptionLabel`



### Reference







  1. [Using Auto Layout in UITableView for dynamic cell layouts & variable row heights](http://stackoverflow.com/a/18746930/1418457)


  2. [Dynamic Table View Cell Height and Auto Layout](http://www.raywenderlich.com/73602/dynamic-table-view-cell-height-auto-layout)


  3. [Table View Cells With Varying Row Heights](http://useyourloaf.com/blog/2014/02/14/table-view-cells-with-varying-row-heights.html)


