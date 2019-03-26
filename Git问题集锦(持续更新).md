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


