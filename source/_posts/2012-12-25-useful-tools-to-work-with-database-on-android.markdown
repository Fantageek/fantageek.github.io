---
author: akhoi90
comments: true
date: 2012-12-25 15:48:17+00:00
layout: post
slug: useful-tools-to-work-with-database-on-android
title: Useful tools to work with database on Android
wordpress_id: 142
categories:
- Android
---

For those who has ever created an Android app using data storage with SQLite database, you must have some tools help you create, insert, update the data table.

<!-- more -->

Today, I'd like to write about 2 tools that I use in apps. One to view content of all tables in a SQLite database file on device. And another can, not only view but also edit and a lot other operations you can do on a SQLite database (generally, a SQLite DBMS).

**1. CellObject SQLite & XML Browser**

[http://www.cellobject.net/Tools/CellObjectSQLiteXMLBrowser.aspx](http://www.cellobject.net/Tools/CellObjectSQLiteXMLBrowser.aspx)

You are writting an app will store data in an SQlite database. Obviously there will be one or more *.db files created on your app data folder. When you want to check the current content of tables in a database file, you have to pull it out into host machine, then use a tool to do that job.

You can shorten the flow if you use this tool. So, actually what it does? Simple, it can show you how many tables you have in your database file, and can show all rows of each table directly, you don't need to manually create any copy of database file (pull it out). Now you have a choice to make a quick and simple look to your database files.

The tool works as a plug-in of Eclipse IDE. After installing (following steps in download pakage), connect your device to you dev machine, then choose which SQLite Db file you want in Explorer view of DDMS perspective. A blue database icon will be available at upper right of Explorer view. Just click on it and browse your database.

One more file type you can view with this tool is XML file. With this type you have right to change the file content, but not more. Because the tool will create a temp file for you at somewhere and just open that file in Eclipse. If you save, you save the temp file. Problem ?

**2. SQLite Studio**

[http://sqlitestudio.one.pl/

](http://sqlitestudio.one.pl/)

One tool for managing an SQLite database I feel most comfortable when use. Below are some of interested points:

- Only one executable file and no needed to install, SQLite Studio is very simple and compact.

- Some SQLite features such as Fulltext search, SQLite3, SQLite2 are full supported with it.

- Exporting data are also very useful functions. You can export what data you want to a specified type of file like: CSV, HTML, PLAIN, SQL, XML.
