# Wasp Monitor

<font face="microsoft yahei">

#update at 2019-4-28 19:38:38

## 需求

由于目前有若干个monitor tool, 每个tool都要各自发邮件, 所有的sender account均为redmond\wbsrv这个user, 除此之外还需要将每一封邮件的相关数据添加至wasp DB中, 在wasp的monitor info page显示出相关的信息.这就引出以下问题:
1. 如果这个账户有修改或者更新密码, 必须将每个部署的tool的所属user都更新一遍
2. redmond域的user无法在其他域中work, 如果tool部署在其他域(eg. FAREAST, redmond.corp.microsoft.com)中, 则不一定能够正常发送邮件.

目前有一个improvement, 就是在各个monitor tool向wasp DB添加数据的这一过程进行改进, Wasp提供一个API, 其他的monitor调用这个API进行数据存储. Wasp内部需要维护一个Email Service, 当这个API被调用的时候, 将数据获取并发送相关邮件.

## API的设计思路

![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190428201650497-564781792.png)

## 数据库设计思路

明天研究

## Email Service设计思路

由于这个Service仅是用来发邮件作用, 所以这个service即使在调用的过程中出现任何问题也不应该影响到monitor tool的正常work. 并且每次work相关的数据要能够成功的记录在wasp db当中.

### 那么什么因素可能会造成email service挂掉:

1. wasp API所部署的环境shut down.
2. wasp API所部署的环境切换到了无法使用redmond\wbsrv的域环境.

如果能够保证上面的因素可以避免或者能够及时得到恢复, 则可以考虑使用email service这个解决方案.

## 为什么不使用数据库的trigger.

1. ​主要是提升应用程序的可扩展性. 因为trigger只能作用于一张表.
​2. 相较于上述方案, trigger更加复杂,并且不易于维护, 而且不同的数据库的trigger的配置方式也不完全一致.

## 参考资料

sql trigger:
[https://sweetcode.io/using-triggers-and-email-alerts-in-microsoft-sql-server/](https://sweetcode.io/using-triggers-and-email-alerts-in-microsoft-sql-server/)
