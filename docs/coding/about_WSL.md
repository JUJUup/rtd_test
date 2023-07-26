---
title: about WSL 
date: 2023-07-21 14:20:00
categories:
- coding
tags: 
- coding
---
<!-- more -->
# About WSL in windows

## install

[windows official totorial](https://learn.microsoft.com/en-us/windows/wsl/install)

the distribution i installed: Ubuntu. $wsl --install Ubuntu$

!!! info
    about this bug: 
    Installing, this may take a few minutes...
    WslRegisterDistribution failed with error: 0x800701bc
    Error: 0x800701bc WSL 2 ?????????????????? https://aka.ms/wsl2kernel

    please see this [issue](https://github.com/microsoft/WSL/issues/5393)
## using

[Get started using Visual Studio Code with Windows Subsystem for Linux](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-vscode)

> You can access Windows files from WSL by navigating to the `/mnt` directory. For example, to access the C drive, you can use the `cd` command in the Linux terminal to navigate to `/mnt/c` ¹. You can also move, edit, create, and delete files between the two systems through WSL ¹.
>
> For example, to move a file from the Windows file system to the Linux file system, you can use the `cp` or `mv` commands ¹. To edit a Windows file through WSL, you can use any text editor that can be opened in the terminal ¹.
>
> For more detailed information on how to work across Windows and Linux file systems, you can check out this guide from Microsoft Learn ².
>
> 源: 与必应的对话， 7/21/2023
> (1) Access the Windows file system through WSL! - DEV Community. https://dev.to/ivayloiv/access-the-windows-file-system-through-wsl-1ac4.
> (2) Working across file systems | Microsoft Learn. https://learn.microsoft.com/en-us/windows/wsl/filesystems.
> (3) File Permissions for WSL | Microsoft Learn. https://learn.microsoft.com/en-us/windows/wsl/file-permissions.