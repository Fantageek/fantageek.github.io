---
author: onmyway133
comments: true
date: 2014-04-13 17:27:34+00:00
layout: post
slug: compilers-and-interpreters-c-jones
title: Compilers And Interpreters - C. Jones
wordpress_id: 729
categories:
- Others
---

This article is great, but I can only find it in archive.org, so I make a copy it here, as a reference for my self and for those who need it

Original link [http://ugweb.cs.ualberta.ca/resources/java/faq/compile.html](http://ugweb.cs.ualberta.ca/resources/java/faq/compile.html)

**Introduction**
In order for us to make the computer perform the tasks we want it to, we have to communicate with the machine. We write our programs in a high-level language such as Java, C, Pascal, etc. but the machine cannot understand that language, as it operates only in bits (**b**inary dig**its** of zero or one). So there needs to be some translation process which will convert our Java instructions (our "source code") into machine code which the computer can then understand and execute (the "object code"). There are two main ways of achieving this translation process:



	
  1. **Compilation**

	
  2. **Interpretation**


The method used depends upon the high level language you are using. Some languages are compiled (e.g. Turbo Pascal, C, etc. ) while others are interpreted languages (e.g. QBasic). (Java, however, uses a combination of both approaches which we'll talk about at the end of this page). Compilers and interpreters are simply pieces of software (in other words, they are programs themselves!) which have been specially written to take the commands we write in our chosen programming language and translate them into a form which the machine understands. Here's an explanation of the difference between a compiler and an interpreter: 

**Comparison of Traditional Compilers and Interpreters**
If 2 native English speakers wish to communicate, there is no language problem. But what if an English speaker wishes to communicate with a French speaker? There are two possible solutions to the problem:



	
  1. The English speaker employs an _interpreter_ who translates the English sentences into French as they are spoken. The English speaker says a sentence in English, the interpreter hears it, translates it in his/her brain into French, and the speaks the sentence in French. This is repeated for each sentence. Progress is slow: there are pauses between sentences as the translation process takes place.

	
  2. The English speaker writes down (in English) what needs to be said. The whole document is then translated into French, producing another piece of paper with the French version written on it. There are two factors to consider in this scenario:

	
    1. There is a slight delay at the start as the English document needs to be translated in its entirity before it can be read.

	
    2. The translated document can be read at any time after that, and at the normal speed at which the French-speaker reads.





In this analogy, English = the _SOURCE CODE_. French = the _MACHINE CODE_. You can think of the French speaker's brain as the computer's CPU.

 A **compiler** translates a complete source program into machine code. The whole source code file is compiled in one go, and a complete, compiled version of the file is produced. This can be saved on some secondary storage medium (e.g. floppy disk, hard disk...). This means that:



	
  * The program can only be executed once translation is complete

	
  * ANY changes to the source code require a complete recompilation.


An **interpreter**, on the other hand, provides a means by which a program written in source language can be understood and executed by the CPU line by line. As the first line is encountered by the interpreter, it is translated and executed. Then it moves to the next line of source code and repeats the process. This means that:



	
  * The interpreter is a program which is loaded into memory alongside the source program

	
  * Statements from the source program are fetched and executed one by one

	
  * No copy of the translation exists, and if the program is to be re-run, it has to be interpreted all over again.


**Considerations**
There are a number of other points which we need to consider: 



	
  * Because the interpreter must be loaded into memory, there is less space available during execution; a compiler is only loaded into memory during compilation stage, and so only the machine code is resident in memory during run-time;

	
  * Once we have a compiled file, we can re-run it any time we want to without having to use the compiler each time; With any interpreted language, however, we would have to re-interpret the program each time we wanted to run it;

	
  * Machine code programs run more quickly than interpreted programs;

	
  * However, it is often quicker and easier to make changes with an interpreted program than a compiled one, and as such development time may be reduced.


**The Approach That Java Uses**

As I said above, Java uses a combination of compilation and interpretation. When you create and save a Java source code file (the file that contains your Java instructions and is saved using the .java extension) it needs to be translated before a computer can understand the instructions. So we compile the .java file. However, unlike other compiled languages, the Java compiler does not produce machine code designed specifically for a particular type of computer. Instead, it produces a new file containing **bytecode**, and this file has the .class extension. Bytecode is like an idealised form of machine language, and the really special thing about bytecode is that it is not machine dependent. Another words, the bytecode produced by the Java compiler is not designed so that only one type of computer can read it, any computer (that has the Java interpreter installed) can read and understand bytecode. The Java interpreter is sometimes referred to as the "Java Virtual Machine" ( Java VM for short) and some books even refer to it as the "Java run-time system". The interpreter on your computer takes the Java bytecode in the .class file, turns it into machine code which your particular computer can understand, and then executes (or "runs") the code.

You might wonder why the Java creators designed things this way - why use a two stage approach instead of just compiling or just interpreting the code like most other languages do? Well, the advantage is that once a Java file has been compiled, any computer with a Java VM can interpret and run that file. So the code is a lot more portable. Think about the huge number of network and Internet applications we run today - lots of different types of computers trying to communicate with each other. This approach overcomes these machine-dependency problems.
