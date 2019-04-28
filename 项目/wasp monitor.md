# Wasp Monitor

#update at 2019-4-28 19:38:38

## 需求

由于目前有若干个monitor tool, 每个tool都要各自发邮件, 所有的sender account均为redmond\wbsrv这个user, 除此之外还需要将邮件的相关数据添加至wasp DB中, 在wasp的monitor info page显示出相关的信息.
1. 如果这个账户有修改或者更新密码, 必须将每个tool的运行user都更新一遍
2. redmond域的user无法在其他域中work, 如果tool部署在其他域(eg. FAREAST, redmond.corp.microsoft.com)中, 则不一定能够正常发送邮件.

目前提出一个improvement, 就是在各个monitor tool向wasp DB添加数据的这一过程进行改进, Wasp提供一个API, 其他的monitor调用这个API进行数据存储. Wasp内部需要维护一个Email Service, 当这个API被调用的时候, 将数据获取并发送相关邮件.

## API的设计思路

![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190428201650497-564781792.png)

## 数据库设计思路

## Email Service设计思路

## 参考资料

sql trigger:
[https://sweetcode.io/using-triggers-and-email-alerts-in-microsoft-sql-server/](https://sweetcode.io/using-triggers-and-email-alerts-in-microsoft-sql-server/)
