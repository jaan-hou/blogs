
# Git 重要概念
- HEAD指针

    指向当前所在的本地分支, 切换目录会改变HEAD指针的指向和当前工作目录中的文件

- fast-forward

    当前HEAD指针指向的提交是你当前提交的直接上游，可以只通过改变HEAD指针的指向完成合并, 因为这种情况下的合并操作并没有需要解决的分歧
- master 和 origin

    没什么特殊含义，master 是 git init 时默认的分支名字， origin 是 git clone 时默认的远程仓库名字
- 远程跟踪分支

    远程跟踪分支是远程分支状态的引用，指向你上一次连接到仓库时分支所处的状态, 如 origin/master, 假设你是在本地的 master 分支工作的，origin/master 指向远程的 master 分支你 clone 时候的状态

    注意：当使用 git fetch (origin) 抓取到新的远程跟踪分支时(假如是 issue53fix 分支)，不会自动生成本地分支，想要本地在新的远程跟踪分支上工作时
    ```
    git checkout -b issue53fix origin/issue53fix
    ```
    或者将更改合并到自己当前的分支
    ```
    git merge origin/issue53fix
    ```
    由于 git checkout -b [branch] [remotename]/[branch] 很常用，因此有其简写形式： git checkout --track [remtoe/name]/[branch]，上述命令可以简写为:
    ```
    git checkout --track origin/issue53fix
    ```

# Git 你也许会问到的问题
- git fetch 和 git pull 之间有什么区别?
    ```
    # 从服务器拉取本地没有的数据，并不会改变工作目录的内容，需要你自己合并
    git fetch --all

    # 差不多相当于 git fetch + git merge
    # 查找当前分支所跟踪的服务器分支，从上边拉取数据并尝试合并
    git pull
    ```
- 到底是使用 rebase 还是 merge?

    哲学问题, 没有答案, 有人认为修改提交, 合并的历史是一种欺骗, 有人想用 rebase 保持提交历史的整洁, 一个原则就是永远不要对仓库外有副本的分支进行变基
- 怎样保持 fork 的项目和上游同步?
    ```
    git remote -v 查看当前的远程仓库地址 
    ```
    添加上游仓库
    ```
    git remote add upstream [upstream address]
    ```
    合并上游的修改
    ```
    git fetch upstream
    git checkcout master
    git merge upstream/master

    # 推送到远程仓库
    git push origin master
    ```

- 手滑写错了commit message或者漏添加了文件，还有救吗?
    - 仅仅修改commit message
    ```
    git commit --amend
    # 或者
    git commit -m "your commit message" --amend
    ```
    - 漏了文件
    ```
    git add forgotten_file
    git commit --amend
    ```
    最终只会有一次提交记录

- 当你在一个分支上开发的时候，master 分支的内容已经变化了好多，你要合并 master 分支的修改, 怎么办? 
    ```
    git checkout your_branch
    git merge master
    ```

- 怎样忽略指定文件(夹)？

    编辑 .gitignore
    ```
    # no .a files
    *.a
    # but do track lib.a, even though you're ignoring .a files above
    !lib.a
    # only ignore the TODO file in the current directory, not subdir/TODO
    /TODO
    # ignore all files in the build/ directory
    build/
    # ignore doc/notes.txt, but not doc/server/arch.txt
    doc/*.txt
    # ignore all .pdf files in the doc/ directory
    doc/**/*.pdf
    ```
    再比如：
    ```
    *.[oa]
    *~
    ```
    忽略以 .o 或 .a 结尾的文件, 忽略以~结尾的文件(通常是编辑器产生的临时文件)


# git 比较好实践
    - 在切换分支之前保持工作目录干净
        通常有两种方法
        1. 提交 (commit) 你的修改
        2. 保存进度（stashing)
    - 使用 git fetch 而不是 git pull
        git pull 会尝试合并到当前分支, 可能这并不是你想要的

    - 只把变基命令 (rebase) 当做在提交前清理提交使之整洁的工具, 只在未推送到远程公用仓库的提交上执行变基命令, then you are safe

# Git 使用技巧
- alias
    ```
    git config --global alias.co checkout
    git config --global alias.br branch
    git config --global alias.ci commit
    git config --global alias.st status
    ```
    创建完别名, git checkout master 就可以使用 git co maseter

# 弃疗时用会到的命令(需熟记!!!, 你会用到的)
    - 撤销暂存(staged)的文件
    ```
    git reset HEAD <file>
    # 如：
    git reset HEAD README.md
    ```
    - 撤销对工作区文件的修改
    ```
    git checkout -- <file>
    # 如：
    git checkout -- README.md
    ```
    - 撤销某(几)次提交，回到指定的commit id
    ```
    git reset --hard commit_id
    git push origin HEAD --force
    ```
    回滚之后，后悔了怎么办？
    ```
    # 打印每一次的操作记录
    git reflog
    # 回退到指定操作之前的版本
    git reset --hard 操作的ID
    ```
    - 不知道自己刚才干了什么,改成了一团浆糊,放弃工作区的所有修改
    ```
    git reset HEAD --hard
    ```

    - rebase的时候肝不动了，要放弃
    ```
    git rebase --abort
    ```

    通常这时候会有一大堆的untracked files

    ```
    #删除 untracked files
    git clean -f

    #连 untracked 的目录也一起删掉
    git clean -fd
 
    #连 gitignore 的 untrack 文件/目录也一起删掉 （慎用，一般这个是用来删掉编译出来的 .o之类的文件用的）
    git clean -xfd
    ```
    最佳实践: 在用上述 git clean 前，墙裂建议加上 -n 参数来先看看会删掉哪些文件，防止重要文件被误删
    ```
    git clean -nf
    git clean -nfd
    git clean -nxfd
    ```    



# Git 常用命令

## 文件操作
```
# -s得到更紧凑格式的输出
git status -s

# 查看尚未暂存的文件更新了哪些部分
git diff

# 查看已暂存的将要添加到下次提交的文件更新了哪些部分
git diff --staged

# 从git中移除某个文件(如果文件已经放到暂存区，要加-f选项强制删除，防止误删还没有添加到快照的数据) 
git rm

# 移动文件或改名
git mv file_from file_to
```

## 标签操作
Git 中有两类标签, 轻量标签和附注标签, 轻量标签是一个特定提交的引用, 附注标签是存储在 Git 数据库中的一个完整的对象, 包括打标签者的名字, 时间, 邮件地址等.
```
# 列出已有的标签
git tag

# 只列出 v1.8.5 系列的标签
git tag -l 'v1.8.5*'

# 查看标签
git show [tagname]
```

- 轻量标签
```
# 打轻量标签
git tag <tagname>

删除标签
git tag -d <tagname>
```
- 附注标签
```
# 打附注标签
git tag -a [tagname] -m 'your tag message'

```
- 对过去的提交打标签
```
# 查看过去的提交的校验和
git log --pretty=oneline

# 打标签, 需指定过去某次提交的(部分)校验和, -a 参数是可选的, 不加打轻量级标签, 加上则打附注标签
git tag -a [tagname] [某次提交的部分校验和]
# 如:
git tag -a v1.2 9fceb02
```
- 推送标签
默认情况下, git push 并不会推送标签到远程服务器上, 创建完标签必须显式地推送
```
# 推送标签
git push origin [tagname]

# 一次推送所有标签
git push origin --tags

# 在特定的标签上创建新的分支
git checkout -b [branchname] [tagname]
```

## 分支操作
```
# 显示分支列表
git branch

# 查看每个分支的最后一次提交
git branch -v

# 查看已经合并的分支
git branch --merged

# 查看没有合并的分支
git branch --no-merged

# 强制删除没有合并的分支
git branch -D branch_name

# 新建分支
git branch branch_name

# 切换到 target_branch
git checkout target_branch

# 新建分支并切换到
git checkout -b target_branch

# 合并 hot_fix 分支到 master
git checkout master
git merge hot_fix

# 删除 hot_fix 分支
git branch -d hot_fix

# 删除远程的某个分支
git push origin --delete branch_name

# 显示远程引用的完整列表
git ls-remote

# 推送分支,本地的分支不会自动和远程仓库同步，必须显式推送
git push [remote] [branch]
git push origin issue53fix

# 修正正在跟踪的上游分支
git branch --set-upstream-to [remotename]/[branch]
# 简写为
git branch -u [remotename]/[branch]

# 查看设置的所有跟踪分支
git branch -vv
```

## 变基操作
变基操作也属于分支操作，由于这个名字比较特别，单独拿出来说一说~

git 中整合不同分支的修改，主要有两种方式，merge 和 rebase, merge 两个分支的最新快照，和这两个分支的最近公共祖先进行三方合并，合并的结果生成一个新的快照，并提交。 变基是将某一分支上的所有修改都移动到另一个分支上, 优点是能够保证提交历史的整洁吧, 将自己的代码变基到 origin/master 再提PR, 这样项目的维护者就可以 fast forward (快进合并).

***永远不要对仓库外有副本的分支进行变基!!! 不然会被打死的*** (那样所有人都不得不将他们的修改和你的分支整合, 他们 git pull 之后又将产生新的合并, 推送到远程仓库实际上又将变基抛弃的提交又找了回来, 陷入万劫不复的深渊), 万一发生了这种情况,可能会有帮助的命令

    ```
    # 使用
    git pull --rebase
    # 而不是 git pull
    ``` 



## 查看提交历史
```
# 查看提交的历史
git log

# -p 参数显示每次提交的内容差异，-2 显示最近的两次提交，-stat 查看每次提交简略的统计信息
git log -p -2
git log -stat

# 将每个提交放在一行显示
git log --pretty=oneline

# 显示图形表示的分支合并历史
git log --pretty=oneline --graph

# 列出最近两周的提交
git log --since=2.weeks

# 列出指定时间之前的提交
git log --before=2.weeks

# 列出添加或移除了某个字符串的提交
git log --Sfunction_name
```

# 推荐的 git 工作流

## 分支
- 长期分支
如 master, light_merge, develop 分支等
- 特性分支
如 hot_fix, feature_XXX 等功能分支