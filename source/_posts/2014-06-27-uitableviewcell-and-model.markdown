---
author: onmyway133
comments: true
date: 2014-06-27 15:59:44+00:00
layout: post
slug: uitableviewcell-and-model
title: UITableViewCell and model
wordpress_id: 1094
categories:
- iOS
tags:
- controller
- datasource
- mapper
- mapping
- model
- uitableviewcell
- view controller
---

The most common UI element in iOS is UITableView, and the most common task is to display the UITableViewCell using the model.

[![Prototype-cell-custom-design](http://www.fantageek.com/wp-content/uploads/2014/06/Prototype-cell-custom-design-300x186.png)](http://www.fantageek.com/wp-content/uploads/2014/06/Prototype-cell-custom-design.png)

Although the title specifies UITableViewCell, but the problem involves other views (UICollectionView, custom view, ...) as well
<!-- more -->


There are many debates about this, so here I want to make a summary, all in my opinion



#### It started with


[UITableViewCell Is Not a Controller](http://inessential.com/2012/12/31/uitableviewcell_is_not_a_controller)
This article follows strict MVC and states we should not pass model object to cell and let cell manipulate directly on the model. He shows several examples that point out this job is not suitable for the cell. Instead this job should be done on the Controller (UIViewController, UITableViewDataSource, ...)



#### Category for simple case


[Skinnier Controllers Using View Categories](http://www.sebastianrehnby.com/blog/2013/01/01/skinnier-controllers-using-view-categories/)
This article states that we should keep ViewController skinnier by transfering the job (mapping model object to cell) to the cell category



#### Using subclassing


[UITableViewCell Is Not a Controller, But…](http://corporationunknown.com/blog/2013/01/01/uitableviewcell-is-not-a-controller-but/)
This articles explains the beauty of subclassing to take advantage of Polymorphism. In the theming example he gives, we see that Controller 's job now is to select the correct cell subclass, and the subclass 's job is to know how to use the model
"When a UITableViewCell subclass accepts a model object parameter and updates its constituent subviews as I have described, it is behaving as a data transformer, not a controller"



#### Model Presenter


[Model View Controller Presenter](https://medium.com/ios-apprentice/model-view-controller-presenter-8bb4149fa5ef)
This article shows that using subclassing and category will have duplication implementation when you have more cells and models. After several optimizations, he finally gets to the Model Presenter, which is the center object who knows how to represent the model in different view.
"This is an object that knows how to represent every aspect of a certain model"



#### MVVM


[MVC, MVVM, FRP, And Building Bridges](http://cocoamanifest.net/articles/2013/10/mvc-mvvm-frp-and-building-bridges.html)
This article explains MVVM but it touches our problem directly. The problem with cell and model actually is
1. How to map the model to the cell
2. Who will do this job? The cell, Controller or another Model Mapping object ?

The ViewModel is actually who does this work, which is to transform the model to something the view can easily use

"A table view data source is none of these things. It’s purely a layer between the table view and the model. The model defines lists of things, but the table view data source transform those lists into sections and rows. It also returns the actual table view cells, but that’s not what I’m focusing on here. The key is its role as a middle-tier data transformer."



#### Do we access the cell 's subviews


[Paul on UITableViewCell](http://inessential.com/2013/01/02/paul_on_uitableviewcell)
Brent follows with another post explaining how cell 's subviews should not be accessed outside of the cell



#### Data Source


[Clean table view code](http://www.objc.io/issue-1/table-views.html)
This article deals with Bridging the Gap Between Model Objects and Cells. 
"At some point we have to hand over the data we want to display into the view layer. Since we still want to maintain a clear separation between the model and the view, we often offload this task to the table view’s data source. This kind of code clutters the data source with specific knowledge about the design of the cell. We are better off factoring this out into a category of the cell class"

This together with [Lighter View Controllers](http://www.objc.io/issue-1/lighter-view-controllers.html) shows a practical example that deals with most cases



#### Reference


1. [Should UITableViewCells Know About Model Objects?](http://cocoamanifest.net/articles/2013/01/should-uitableviewcells-know-about-model-objects.html)
