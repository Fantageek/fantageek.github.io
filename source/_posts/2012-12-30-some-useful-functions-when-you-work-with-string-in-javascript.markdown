---
author: khoathai
comments: true
date: 2012-12-30 04:19:20+00:00
layout: post
slug: some-useful-functions-when-you-work-with-string-in-javascript
title: 'Some useful functions when you work with string in javascript '
wordpress_id: 179
categories:
- Java Script
tags:
- javascript
- lower
- replace
- special characters
- split
- string
- trim
- upper
- useful
---

[![jsstring](http://www.fantageek.com/wp-content/uploads/2012/12/jsstring-300x262.png)](http://www.fantageek.com/?attachment_id=180)




There are some useful functions when you work with string in javascript





# 1. Replace:




## a/ Formal



    
    var str="Hello World";
    var n=str.replace("World","Fantageek");
    
    --> n = Hello Fantageek




## b/ Replace all special characters



    
    var desiredString = stringToReplace.replace(/[^\w\s]/gi, '')


It's easier to do this as a whitelist - replace the characters which _aren't_ in your safelist.



	
  * The (^) character is the negation of the set [...],

	
  * gi say global and case-insensitive (the latter is a bit redundant but I wanted to mention it)

	
  * the safelist in this example is digits, word characters,underscores (\w) and whitespace (\s).




# 2.Trim



    
    var originalString = "   foo   ";
    var desiredString = originalString.trim();


Running the following code before any other code will create String.trim if it's not natively available.

    
    if(!String.prototype.trim) {
            String.prototype.trim = function () {
            return this.replace(/^\s+|\s+$/g,'');
        };
    }


If not again:

With IE 7

    
    String.prototype.trim=function(){return this.replace(/^\s+|\s+$/g, '');};
    
    String.prototype.ltrim=function(){return this.replace(/^\s+/,'');};
    
    String.prototype.rtrim=function(){return this.replace(/\s+$/,'');};
    
    String.prototype.fulltrim=function(){return this.replace(/(?:(?:^|\n)\s+|\s+(?:$|\n))/g,'').replace(/\s+/g,' ');};




# 3. Split



    
    var str="How are you?";
    var n=str.split(" ");
    ----------
    n[0] = How
    n[1] = are
    n[2] = you?




# 4. Some other function:




## a/ Lower case:



    
    var lowerStr = stringToLower.toLowerCase();




## b./ Upper Case:



    
    var upperStr = stringToUpper.toUpperCase();




_**khoathai**_




_(summary from stackoverflow.com)_
