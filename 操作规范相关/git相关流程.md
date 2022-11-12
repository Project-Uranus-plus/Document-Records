#                                             git相关流程

## 1.操作技巧


### 1.1切换分支技巧
本地正在开发的分支, 临时需要切换到其他分支,进行debug修复或其他操作, 使用 git stash, 可将当前开发分支的代码,进行保存, 然后git checkout 到相应分支进行开发, 开发完毕后, git checkout到之前的分支, git stash pop 或 git stash apply 相应stash号, 将stash保存的还原.
具体流程:

1. git stash save “相应的描述”
2. git checkout “要临时进行操作的分支”
3. 在临时操作分支,工作完毕
4. git checkout “之前工作的分支”
5. git stash apply stash@\{“stash对应编码”\} 

### 2.1分支命名规范

1. Bug分支: fix/修复的bug-名字简称
2. 新特性分支: feature/特性-名字简称
3. 修改功能分支：update/特性-名字简称
4. 删除功能分支：update/特性-名字简称



## 2.常用操作流程

### 2.1新建分支

首先，每次开发新功能，都应该新建一个单独的分支（这方面可以参考[《Git分支管理策略》](https://www.ruanyifeng.com/blog/2012/07/git.html)）。

> ```bash
> # 获取主干最新代码
> $ git checkout master
> $ git pull
> 
> # 新建一个开发分支
> $ git checkout -b 分支名
> ```

### 2.2提交分支commit

分支修改后，就可以提交commit了。

> ```bash
> $ git add --all
> $ git status
> $ git commit --verbose
> ```

git add 命令的all参数，表示保存所有变化（包括新建、修改和删除）。从Git 2.0开始，all是 git add 的默认参数，所以也可以用 git add . 代替。

git status 命令，用来查看发生变动的文件。

git commit 命令的verbose参数，会列出 [diff](https://www.ruanyifeng.com/blog/2012/08/how_to_read_diff.html) 的结果。

### 2.3撰写提交信息

提交commit时，必须给出完整扼要的提交信息，下面是一个范本。

> ```bash
> Present-tense summary under 50 characters
> 
> * More information about commit (under 72 characters).
> * More information about commit (under 72 characters).
> 
> http://project.management-system.com/ticket/123
> ```

第一行是不超过50个字的提要，然后空一行，罗列出改动原因、主要变动、以及需要注意的问题。最后，提供对应的网址（比如Bug ticket）。

### 2.4与主干同步

分支的开发过程中，要经常与主干保持同步。

> ```bash
> $ git fetch origin
> $ git rebase origin/master
> ```

### 2.5合并commit

分支开发完成后，很可能有一堆commit，但是合并到主干的时候，往往希望只有一个（或最多两三个）commit，这样不仅清晰，也容易管理。

那么，怎样才能将多个commit合并呢？这就要用到 git rebase 命令。

> ```bash
> $ git rebase -i origin/master
> ```

git rebase命令的i参数表示互动（interactive），这时git会打开一个互动界面，进行下一步操作。

下面采用[Tute Costa](https://robots.thoughtbot.com/git-interactive-rebase-squash-amend-rewriting-history)的例子，来解释怎么合并commit。

> ```bash
> pick 07c5abd Introduce OpenPGP and teach basic usage
> pick de9b1eb Fix PostChecker::Post#urls
> pick 3e7ee36 Hey kids, stop all the highlighting
> pick fa20af3 git interactive rebase, squash, amend
> 
> # Rebase 8db7e8b..fa20af3 onto 8db7e8b
> #
> # Commands:
> #  p, pick = use commit
> #  r, reword = use commit, but edit the commit message
> #  e, edit = use commit, but stop for amending
> #  s, squash = use commit, but meld into previous commit
> #  f, fixup = like "squash", but discard this commit's log message
> #  x, exec = run command (the rest of the line) using shell
> #
> # These lines can be re-ordered; they are executed from top to bottom.
> #
> # If you remove a line here THAT COMMIT WILL BE LOST.
> #
> # However, if you remove everything, the rebase will be aborted.
> #
> # Note that empty commits are commented out
> ```

上面的互动界面，先列出当前分支最新的4个commit（越下面越新）。每个commit前面有一个操作命令，默认是pick，表示该行commit被选中，要进行rebase操作。

4个commit的下面是一大堆注释，列出可以使用的命令。

> - pick：正常选中
> - reword：选中，并且修改提交信息；
> - edit：选中，rebase时会暂停，允许你修改这个commit（参考[这里](https://schacon.github.io/gitbook/4_interactive_rebasing.html)）
> - squash：选中，会将当前commit与上一个commit合并
> - fixup：与squash相同，但不会保存当前commit的提交信息
> - exec：执行其他shell命令

上面这6个命令当中，squash和fixup可以用来合并commit。先把需要合并的commit前面的动词，改成squash（或者s）。

> ```bash
> pick 07c5abd Introduce OpenPGP and teach basic usage
> s de9b1eb Fix PostChecker::Post#urls
> s 3e7ee36 Hey kids, stop all the highlighting
> pick fa20af3 git interactive rebase, squash, amend
> ```

这样一改，执行后，当前分支只会剩下两个commit。第二行和第三行的commit，都会合并到第一行的commit。提交信息会同时包含，这三个commit的提交信息。

> ```bash
> # This is a combination of 3 commits.
> # The first commit's message is:
> Introduce OpenPGP and teach basic usage
> 
> # This is the 2nd commit message:
> Fix PostChecker::Post#urls
> 
> # This is the 3rd commit message:
> Hey kids, stop all the highlighting
> ```

如果将第三行的squash命令改成fixup命令。

> ```bash
> pick 07c5abd Introduce OpenPGP and teach basic usage
> s de9b1eb Fix PostChecker::Post#urls
> f 3e7ee36 Hey kids, stop all the highlighting
> pick fa20af3 git interactive rebase, squash, amend
> ```

运行结果相同，还是会生成两个commit，第二行和第三行的commit，都合并到第一行的commit。但是，新的提交信息里面，第三行commit的提交信息，会被注释掉。

> ```bash
> # This is a combination of 3 commits.
> # The first commit's message is:
> Introduce OpenPGP and teach basic usage
> 
> # This is the 2nd commit message:
> Fix PostChecker::Post#urls
> 
> # This is the 3rd commit message:
> # Hey kids, stop all the highlighting
> ```

[Pony Foo](https://ponyfoo.com/articles/git-github-hacks)提出另外一种合并commit的简便方法，就是先撤销过去5个commit，然后再建一个新的。

> ```bash
> $ git reset HEAD~5
> $ git add .
> $ git commit -am "Here's the bug fix that closes #28"
> $ git push --force
> ```

squash和fixup命令，还可以当作命令行参数使用，自动合并commit。

> ```bash
> $ git commit --fixup  
> $ git rebase -i --autosquash 
> ```

这个用法请参考[这篇文章](https://fle.github.io/git-tip-keep-your-branch-clean-with-fixup-and-autosquash.html)，这里就不解释了。

### 2.6推送到远程仓库

合并commit后，就可以推送当前分支到远程仓库了。

> ```bash
> $ git push --force origin 分支名
> ```

git push命令要加上force参数，因为rebase以后，分支历史改变了，跟远程分支不一定兼容，有可能要强行推送（参见[这里](http://willi.am/blog/2014/08/12/the-dark-side-of-the-force-push/)）。

### 2.7发出Pull Request

提交到远程仓库以后，就可以发出 Pull Request 到master分支，然后请求别人进行代码review，确认可以合并到master。



## 3.注意事项

1. 除非特殊情况,请勿直接提交代码到master分支, master分支只做合并其他分支.
2. 本地配置文件信息,请勿提交