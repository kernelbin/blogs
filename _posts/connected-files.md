---
layout: blog
title: Connected Files
date: 2023-11-05 03:03:35
tags: 技术
---


*今天用 Chrome 的 Ctrl+S 抓了一整个网页下来想看看能不能把网页上的一个 canvas 保存下来却以失败告终。不过最后准备删掉文件的时候却有个意外的小发现*

Chrome 保存下来了 `foo.html` 这个文件和 `foo_files` 这个文件夹。在我删除文件的同时，**文件夹也一并消失不见了！**百思不得其解的我把他们俩从垃圾桶找了出来。摆弄了一会后，发现复制时似乎这两个文件也总是被一起复制移动。我又仔细检查了文件和文件夹的属性，但一无所获。与此同时，我还在更改 `foo.html` 的文件名时候被提示这个文件属于 `foo_files`。

一番调查之后，终于发现这并非是什么神秘的 NTFS 的特殊隐藏属性，而**仅仅是 explorer 干的事**。对于 `xxx.html` 文件，explorer 会试着找到名为 `xxx_files` 的文件夹，并且一并操作这两个文件。这个行为阿硬记录在了[\[这里\]](https://learn.microsoft.com/en-us/windows/win32/shell/manage#connected-files)，名为 Connected Files
