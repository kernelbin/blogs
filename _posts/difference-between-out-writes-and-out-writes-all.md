---
title: SAL 中 _Out_writes_ 和 _Out_writes_all_ 的区别
tags: 技术
date: 2023-09-11 14:51:03
---


经常写 Windows 程序的人一定不会对 SAL 陌生。SAL（Source Annotation Language）可以帮助你在编译的时候就检测出一些可能的运行时错误，比如数组越界。不是很了解？ [>阿硬文档传送门<](https://learn.microsoft.com/en-us/cpp/code-quality/understanding-sal?view=msvc-170) 

之前一直没有留意有些参数的 Annotation 后附带的 `_all_` 的含义，比如 `_Out_writes_all_(s)`。什么时候该用这个，什么时候又该用不带 `_all_` 的 `_Out_writes_(s)` 呢？

`_Out_writes_(s)` 表示传入数组可以写入的有效长度是 `s`，并且这个函数会往这个参数内写入一些东西，但是现在并不知道具体会写入多长的数据。比如 `strcpy` 就适用这种情况：虽然肯定知道 `dest` 会被从 `src` 中复制过来一段数据，但是多长...起码这个长度没有一个现成的变量可以指明。以下是阿硬文档中的栗子🌰：

```c
typedef _Null_terminated_ wchar_t *PWSTR;
void MyStringCopy(_Out_writes_(size) PWSTR dest, _In_ size_t size, _In_ PWSTR src);
```

`_Out_writes_all_(s)` 则说明除了数组可以写入的有效长度是 `s`，也保证调用后整个数组都是有效的，这实际上等效于写 `_Out_writes_to_(s, s)`。适用的情况有 `memset`，因为 memset 一定会把整个数组全部填充满。以下是 `memset` 在 MSVC 里的声明：

```c
void* __cdecl memset(
    _Out_writes_bytes_all_(_Size) void*  _Dst,
    _In_                          int    _Val,
    _In_                          size_t _Size
    );
```

个人认为 SAL 还是相当强大好用的，苦于文档太少只能自己摸索使用姿势和 best practise，（当然上述内容还是有在文档记录的...）希望阿硬赶紧能把 SAL 的文档写完善一些（（