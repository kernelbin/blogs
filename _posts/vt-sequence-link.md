---
layout: blog
title: VT Sequence 输出链接
date: 2024-02-15 01:06:04
tags: 技术 Console
---

*今天在摆弄新鲜出炉的 Sudo for Windows 时候的一个小发现*

虽然截至我写下这篇博客的时候，MSDN 上 [VT Sequence 的文档](https://learn.microsoft.com/en-us/windows/console/console-virtual-terminal-sequences) 还没有记载其对输出超链接的支持，但是似乎我目前的 Windows 上的 Windows Terminal 是有相应支持的。（conhost 似乎仍然只会输出普通文本）

以下是一段简短的代码展示如何使用这一功能

```c
#include <Windows.h>
#include <cstdio>

#define ESC "\x1b"

int main()
{
    // 应该是多余的，仅仅是再次确保 VT 处理已开启
    DWORD Mode = 0;
    GetConsoleMode(GetStdHandle(STD_OUTPUT_HANDLE), &Mode);
    SetConsoleMode(GetStdHandle(STD_OUTPUT_HANDLE), Mode | ENABLE_VIRTUAL_TERMINAL_PROCESSING);

    printf(ESC "]8;;https://example.com" ESC "\\This is a link" ESC "]8;;" ESC "\\\n");
    return 0;
}
```

效果如下，一个可以用 Ctrl + 左键 打开的链接：

![VT-Sequence link example](/img/VT-Sequence-link-example.png)
