# Branch相关的git命令

查看分支: `git branch`

创建分支: `git branch <name>`

切换分支: `git checkout <name>`

创建+切换分支: `git checkout -b <name>`

合并某分支到当前分支: `git merge <name>`

删除分支: `git branch -d <name>`

# Questions 
>Another git process seems to be running in this repository, e.g. an editor opened by 'git commit'. Please make sure all processes
are terminated then try again. If it still fails, a git process
may have crashed in this repository earlier:
remove the file manually to continue.

`rm -f .git/index.lock`

出现这个问题一般是执行某个git命令中断了, 并且在你得代码目录某个文件夹下会有一个*.lock的文件. 你需要通过git命令将这个文件去除.
[资料链接](https://stackoverflow.com/questions/38004148/another-git-process-seems-to-be-running-in-this-repository)


抱歉理解错问题

可以尝试使用插件: [AutoVolume](http://www.jesseclaven.com/projects/AutoVolume/) 来解决从睡眠状态唤醒mac时发出声音.

最新版下载链接: [https://github.com/jesse-c/AutoVolume/releases/tag/v1.0.1](https://github.com/jesse-c/AutoVolume/releases/tag/v1.0.1)

源码: [https://github.com/jesse-c/AutoVolume](https://github.com/jesse-c/AutoVolume)

参考资料:[https://www.howtogeek.com/308291/how-to-automatically-mute-your-macbook-when-it-goes-to-sleep/](https://www.howtogeek.com/308291/how-to-automatically-mute-your-macbook-when-it-goes-to-sleep/)


