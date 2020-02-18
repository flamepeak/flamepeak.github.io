---
title: EFI 程序测试注意事项
date: 2017-03-08 10:23:32
tags: [EFI, WikiLeaks]
categories: [Network Security]
---

本文来自：[WikiLeaks CIA:EFI Program Testing Considerations](https://wikileaks.org/ciav7p1/cms/page_29851664.html)


**EFI Program Testing Considerations**

This page is not designed to teach how to exhaustively test EFI executables, but will serve as a point of reference for different issues raised in EFI that are not typically common elsewhere.
本文并不是教给你如何彻底的测试EFI可执行文件，但是它是一个由EFI引起的在其它地方并不常见的各种问题的参考。

### Launching the EFI Shell 启动EFI Shell
On a Mac, the easiest way to run an EFI executable is to boot into the EFI shell and run it from there.
在一台Mac上，运行EFI可执行文件最简单的方法是：启动进入EFI shell并从shell中运行。

Because Macs still use EFI 1.10 (versus UEFI 2.5), you will need to run the EFI shell version 1. You can grab that binary here: UEFIShell1.efi
因为Macs仍然使用EFI 1.10（对应UEFI 2.5），你需要运行EFI shell的版本1. 你可以获取这个二进制文件：[UEFIShell1.efi](https://wikileaks.org/ciav7p1/cms/files/UEFIShell1.efi)

In order to run the EFI shell, you need to either put in onto your EFI partition or onto a bootable USB drive.
为了运行EFI shell， 你需要把它放在EFI分区，或者放在一个可启动的USB驱动器上。

Whichever you choose, the file must be placed in /EFI/BOOT on the drive, and must be named BOOTX64.efi
无论你采用哪种方式，这个文件必须放在`/EFI/BOOT`内，并且必须命名为`BOOTX64.efi`.

Once it is there (either on your USB or the EFI system partition), you can reboot your machine, hold down the option key on boot, and select the "EFI Boot" drive.
把它放好之后，你可以重新启动你的机器，在启动时按住选项键，并选择`EFI Boot`.



### Testing Considerations 测试注意事项
#### Running Your Program 运行你的程序
It is fairly simple to run an EFI program from the EFI shell.
从EFI shell运行EFI程序是非常简单的。

First, you need to find the file system that your executable is on. In order to do this, you need to find which "fs#" it is on. For example, the EFI system partition is typically fs0, and the OS X Recovery partition is typically fs1.
首先，你需要找到你的可执行文件在哪个文件系统上。为了做到这一点，你需要找到它在那个`fs#`上。例如，EFI系统分区通常是`fs0`，OS X恢复分区通常是`fs1`。

You can type the command "fs0:" and the shell will move you to fs0. From there, this shell is similar to a DOS shell: you can use dir to list the files in your directory, cd to move around, etc.
你可以输入命令`fs0:`，然后shell将会使你的工作路径到`fs0`。这个shell有点类似DOS shell，你可以使用`dir`查看目录中的文件，`cd`可以更改工作目录等等。

If you need more help in the EFI shell, type "help -b". The -b flag makes the help output show one screen at a time, similar to more in a bash shell.
如果你想在EFI shell中获取帮助信息，输入命令`help -v`。`-b`标志使帮助的输出一次显示一屏，有点类似bash shell中的`more`。

From there, find your executable. If you created HelloWorld.efi as an application, you can run it from the EFI shell simply by typing HelloWorld.efi from its directory.
找到你的可执行文件，如果你创建了一个`HelloWorld.efi`程序，你只需在shell中输入`HelooWorld.efi`就可以运行它（注意目录）。

If your executable is a driver, you can run it using the load command. For the HelloWorld example, you would type "load HelloWorld.efi" from its directory.
如果你的可行性文件是一个驱动(driver)，你可以使用`load`命令运行它。对于HelloWord的例子，你需要在它的目录中输入`load HelloWorld.efi`。

You can also use absolute or relative paths when loading or running your executables.
在load或者运行可执行文件时，你可以使用绝对路径或者相对路径。

### Pointers 指针
Pointers are irritating enough: you think you're pointing to the right part of your structure, and instead you get a seg fault, or you dereference a NULL pointer, or something else nasty.
指针非常令人不爽：你认为它指向了代码结构中的正确部分，但是你得到了一个段错误(seg fault)，或者间接引用了一个NULL指针，或者其它令人讨厌的错误。

Normally, this will cause a crash, and you may get some sort of error message (or a core dump.) However, in EFI, if you commit this type of memory-related error, the program will simply hang.
正常情况下，这将会造成程序崩溃，同时你可能会得到一些错误信息。然而，在EFI中，如果你遇到这种内存相关的错误，程序将会挂起。

As of right now, I have not found a way to kill a program that has hung from within EFI. This means that if your executable hangs, you will need to power cycle your machine. (Please let me know if you find a work-around to this; that would be great.)
到目前为止，我还没有找到一种在EFI中杀死挂起程序的方法。这意味着，如果你的可执行文件挂起了，你将不得不重启机器。（如果你找到一种方法，请告诉我，那就太棒了）

### Stripped Binaries
By default, the EDK includes a lot of debug information when it compiles its executables, as noted in the EDK2 Compiler Information and CI Concerns page.
默认情况下，当编译可行性文件时EDK（Embedded Development Kit）包含很多调试信息，正如在[EDK2 Compiler Information and CI Concerns](https://wikileaks.org/ciav7p1/cms/page_27721733.html)中提到的那样。

If you are trying to minimize the amount of debug strings in your executables like I did, you may be tempted to add the --strip-all flag to the set of flags to pass to objcopy. Don't do that.
如果你像我一样试图最小化可执行文件中调试字符串的数量，你可能想给传递给objcopy的标记集合增加`--strip-all`标记。不要这样做！

Although I have not had the time to pinpoint what exactly is being stripped, if you use --strip-all, the loader (for the application or for the driver) will not be able to find your program's entry point and your program will hang. No errors in compilation, nothing in the EFI shell – it will simply hang.
尽管我没有时间精确的指出到底是什么东西被删掉，如果你使用`--stip-all`，loader将不能找到程序的入口点，你的程序会挂起。编译时不会有错误提示，EFI shell也没有，它只是挂起！


## 补充
其它的CIA的EFI资料：
[搭建EFI的Linux开发环境](https://wikileaks.org/ciav7p1/cms/page_27262984.html):Setting Up a Linux Build Environment for EFI：This page will walk you through building a build environment for a Linux machine. Specifically, this tutorial is focused on Ubuntu/Linux Mint.

[ExitBootServices Hooking](https://wikileaks.org/ciav7p1/cms/page_36896783.html)

[Active EFI/UEFI Projects](https://wikileaks.org/ciav7p1/cms/page_26968082.html):Below are links to project pages for projects that are using/going against UEFI/EFI.
+ [DerStarke](https://wikileaks.org/ciav7p1/cms/page_3375125.html) :Apple EFI implant via flash unlock
+ [QuarkMatter](https://wikileaks.org/ciav7p1/cms/page_21561431.html) :Apple EFI implant via EFI system partition

[EFI Basics: NVRAM Variables](https://wikileaks.org/ciav7p1/cms/page_26968084.html):NVRAM 非易失性随机访问存储器 (Non-Volatile Random Access Memory)，是指断电后仍能保持数据的一种RAM。

[EDK2 Compiler Information and CI Concerns](https://wikileaks.org/ciav7p1/cms/page_27721733.html): This page will discuss locations of compiler flags within the TianoCore EDK2 (UDK 2014) build environment and CI concerns that need to be taken into account when building EFI binaries.This page documents everything noteworthy that we have found so far.
