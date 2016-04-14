---
author: onmyway133
comments: true
date: 2015-01-17 14:59:15+00:00
layout: post
slug: handle-uitextfield-change-with-symbol
title: Handling UITextField change with symbol
wordpress_id: 1574
categories:
- iOS
---

In my app, there is a requirement to allow user to input value with symbol, like $45, 28 lbs, 22", ..., plus some more requirement like limiting the number of digits, validating against max and min value, showing default text when the field is empty, only showing symbol when there is at least 1 digit, ...

I only want the easy way :]

There are 2 solutions that came into mind
1. Show $45. When user begins editing, remove that dollar symbol. When user ends editing, add that dollar symbol again
2. Show $45. When user edits, check and handle appropriately to keep that dollar symbol along

People prefer option #2 because it make user aware of the unit they are inputting. But it requires a lot of handling as you must separate the symbol from the digits. Some libraries prove this [TSCurrencyTextField](https://github.com/TomSwift/TSCurrencyTextField), ...

There is option #3, that is to use `UITextField` `leftView` and `rightView` for the symbol. The idea is to determine the `text` property of UITextField and show/hide leftView, rightView accordingly



## leftView and rightView



Let's create a label called `decoratorLabel` to use as the leftView and rightView

[code language="objc"]
- (void)setupDecoratorLabel {
    self.decoratorLabel = [[UILabel alloc] init];
    self.decoratorLabel.text = self.decorator;
    self.decoratorLabel.textColor = [UIColor blackColor];
    self.decoratorLabel.font = [UIFont lightFontWithSize:kProfileTextSize];

    CGSize size = [self.decoratorLabel sizeThatFits:CGSizeMake(20, CGRectGetHeight(self.textField.bounds))];
    self.decoratorLabel.frame = CGRectMake(0, 0, size.width, size.height);
}
[/code]



## Show and hide decoratorLabel accordingly



Subscribed to UITextFieldDelegate and UIControlEventEditingChanged event. Here I demonstrate for `leftView`, the same for `rightView`

[code language="objc"]
- (void)textFieldDidBeginEditing:(UITextField *)textField {
    [self determineToShowOrHideDecoratorLabel];
}

- (void)textFieldDidEndEditing:(UITextField *)textField {
    [self hideDecoratorLabel];
}

// This is from UIControlEventEditingChanged
- (void)textFieldEditingChanged:(UITextField *)textField {
    // Tell textField to invalidate intrinsicContentSize, so that its width is dynamic
    [textField invalidateIntrinsicContentSize];
    [self determineToShowOrHideDecoratorLabel];
}

- (void)hideDecoratorLabel {
    self.textField.leftView = nil;     
}

- (void)showDecoratorLabel {
    self.textField.leftView = self.decoratorLabel;
    self.textField.leftViewMode = UITextFieldViewModeAlways;
}

- (void)determineToShowOrHideDecoratorLabel {
    // self here is UITextField
    if (self.text.length > 0) {
        [self showDecoratorLabel];
    } else {
        [self hideDecoratorLabel];
    }
}
[/code]



### intrinsicContentSize



I don't know why but UITextField only update its `intrinsicContentSize` when it resigns its first responder status, and its size is affected by its `placeHolder` text also. See [Resize a UITextField while typing (by using Autolayout)](http://stackoverflow.com/questions/18236661/resize-a-uitextfield-while-typing-by-using-autolayout)

So the solution is to override `intrinsicContentSize` and return width based on the text

[code language="objc"]
- (CGSize)intrinsicContentSize {
    if (self.isEditing) {
        CGSize size = [self.text sizeWithAttributes:self.typingAttributes];
        return CGSizeMake(size.width + self.rightView.bounds.size.width + self.leftView.bounds.size.width + 2, size.height);
    }

    return [super intrinsicContentSize];
}
[/code]

Here we +2 to make room for the caret

PS: If for some reasons, you want UITextField `intrinsicContenSize` to take effect immediately, call `textField layoutIfNeeded`



## Reference







  1. [How to handle number text field change with symbol?](http://ux.stackexchange.com/questions/72105/how-to-handle-number-text-field-change-with-symbol)


  2. [Resize UITextField horizontally with text using Auto Layout](http://stackoverflow.com/questions/22106426/resize-uitextfield-horizontally-with-text-using-auto-layout)


  3. [UITextfield leftView/rightView padding on iOS7](http://stackoverflow.com/questions/19371018/uitextfield-leftview-rightview-padding-on-ios7)


