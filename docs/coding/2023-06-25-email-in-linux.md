---
title: 在linux服务器上自动发邮件
date: 2023-06-25 16:09:29
categories:
- linux
tags:
- linux
- automatic
---

最近又开始了一年一度的台风季搬砖。组里的NCAR GFS/GDAS下载脚本已经被我粗糙的改成了自动化version，但是去年实践下来偶尔会因为莫名原因崩掉，所以还是免不了每天上去当赛博监工看看赛博尼哥有没有在好好干活……又犯懒了，得找个办法把赛博监工的活也给代劳一下。

<!-- more -->

如果linux每天能用mail/telegram告诉我下载进度的话，就省的我每天登server查看了(虽然也没有多麻烦……)。不过这个automaticly send mail的方法在别的地方应该也有点用。

问了下new bing，大致的实现原理如下：

!!! info
    You can use a cron job to run the `ls $DIR` command every 24 hours and email the output to yourself. Here's an example of how you can do this:

    1. Open the crontab file by running `crontab -e`.
    2. Add the following line to the file:
    ```
    0 0 * * * ls $DIR | mail -s "Directory Listing" your_email@example.com
    ```
    This will run the `ls $DIR` command at 0 minutes past midnight (i.e., every 24 hours) and email the output to `your_email@example.com` with the subject "Directory Listing".

    Make sure to replace `$DIR` with the actual directory path and `your_email@example.com` with your email address.

如果要发telegram的话，同理使用telegram-send就行，不过组里的服务器(llleistu@114.212.48.200)还没挂梯子，发不了telegram……比起邮件，还是有telegram更加符合我的使用习惯。给服务器挂梯子的事留着以后再说。



