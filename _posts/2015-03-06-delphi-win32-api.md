---
layout: post
title: Delphi的Win32的API调用简单介绍
categories: delphi之多线程 delphi之系统调用
tags: delphi 集合 多线程 windows api
---

## 介绍Win32 API和Win32系统

还要讨论Win32系统的功能以及它与16位系统在功能上的几个主要区别。只是让对Win32系统有一个基本的了解。当已经基本了解Win32操作后，就可以在任何需要的时候使用Win32系统提供的高级功能了。

## Win32环境中有两种基本的对象类型

Win32环境中有两种基本的对象类型：内核对象和GDI/用户对象。

内核对象是Win32系统原有的，包括事件、文件映射、文件、邮件槽、互斥、管道、进程、信号灯和线程。Win32 API包含有针对不同内核对象的函数。

## 对象与它的句柄

对象与它的句柄之间存在直接的关系。一个对象的句柄实际上是一个指针，这个指针指向构成对象的数据。依赖于不同的对象类型，对象数据存储于GDI或用户数据段中。另外，对于分配于全局堆的对象，他们的句柄也是指针，指向全局内存段。

Win32 GDI子系统对GDI句柄的管理包含两个方面，一个是对GDI对象的校验，另一个是句柄的重复使用

用户对象和GDI对象有些类似，它是由Win32用户子系统管理的。然而用户对象的句柄不像GDI对象那样存储于进程的地址空间，而是有一个专门的用户句柄表。因此，像窗口、窗口类、原子等对象可以在不同的进程之间共享。

## 多任务

多任务是指操作系统可以同时运行多个应用程序。操作系统把CPU的时间分成片分配给每个应用程序。在这种情况下，多任务其实并不是真正的多任务，只能说是任务切换。或者说，操作系统并没有真正同时运行多个应用程序。相反，它先运行一个应用程序一定的时间，再切换到另一个应用程序运行一定的时间。它对每个应用程序都这样处理。因为时间被划分得很短，对于用户来说，就好像多个应用程序在同时运行一样。

## 多线程

是指一个应用程序内部的多任务。这意味着应用程序可以同进行不同类型的处理。一个进程可以有多个线程，每个线程都有各自不同的执行代码。一个线程可能要依赖于另一个线程，这样就必须要同步。例如，不能假设一个线程在另一个线程要使用它的结果时已完成了处理。线程同步技术用于多个线程能够同步执行。

## 内存与虚拟地址

你的计算机不太可能安装4GB的物理内存。那么，Win32系统是怎样获得比实际安装的物理内存大得多的地址空间的？32位的地址并不真正代表物理内存的一个位置，其实Win32使用的是虚拟地址。

通过虚拟地址，每一个进程可以获得4GB的虚拟地址空间。上端的2MB空间属于Windows，下端的2MB空间是放置应用程序及可以分配内存的地方。这种模式的优势在于一个进程中的线程不能访问其他进程的内存。同样的地址$54545454在不同的进程中指向不同的位置。

一个进程并不是真的有4GB的内存而只是具有访问4GB内存的能力，注意到这一点是很重要的。一个进程真正能够访问的内存大小取决于计算机安装了多少物理内存以及磁盘上有多少的空间可被页交换文件使用。对于一个进程而言，物理内存和页交换文件是按页来划分使用的。页的大小取决于Win32安装在什么类型的系统上。在Intel的平台上，没页的长度是4KB；在Alpha平台上，每页的长度是8KB。对于PowerPc和MIPS平台而言，每页的长度也是4KB。系统会把页从页交换文件移到物理内存中，需要的时候在移回来。系统会维护进程当中虚拟地址和物理地址之间的关系。