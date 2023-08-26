---
title: cet-endbr
tags:
  - 技术
  - 汇编
date: 2023-08-26 00:52:15
---


最近在读 [Wireguard-NT](https://github.com/WireGuard/wireguard-nt) 的源代码中 [SIMD 优化部分](https://github.com/WireGuard/wireguard-nt/blob/master/driver/crypto-amd64.asm)时，发现在几乎每个函数的入口处都有如下汇编代码：

```ASM
SomeFunction PROC PUBLIC
DB 243,15,30,250
; other code...
```
对此感到非常奇怪。在一番调查后，发现这段数据对应的是 `ENDBR64` 指令，属于英特尔控制流强制技术（CET）的一部分。

CET 技术主要由影子堆栈 (Shadow Stack, SS) 和间接分支跟踪 (Indirect Branch Tracking, IBT) 组成，而这里的 `ENDBR64` 指令就属于后者，这些技术都是为了一定程度防止攻击者通过篡改跳转类型指令的目的地址构造攻击。`ENDBR64` 是 64 位下的，自然也有对应的 32 位版本。

在启用 IBT 的情况下，间接跳转和调用类型的指令不能跳转到任意的地址，如果跳转到的目标地址没有对应的 `ENDBR` 指令则会触发异常，相当于是规定了只能跳转到指定的标记有 `ENDBR` 指令的地址。在较老的不支持的机器上，这条指令会被视为 `NOP`。

*gcc 可以通过参数 `-fcf-protection=branch` 来自动生成相关代码，但似乎我编写这篇博客的时候 MSVC 还没有相应的支持*

SS 技术则是通过把返回地址在另一个独立的堆栈里再存储一份返回地址，并在返回时对一式两份的地址进行比较来增加构造攻击的难度。（不过这类技术似乎会影响 `setjmp` 和 `longjmp` 函数的兼容性，可能需要额外做一些工作）
