## 相关资料

- [Pro Git 中文版](https://git-scm.com/book/zh/v1/)
- [廖雪峰 Git 教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
- [RyPress Git 教程](http://rypress.com/tutorials/git/index)（目录为竖版）
- [Githug 游戏](https://github.com/Gazler/githug)（[攻略](http://www.jianshu.com/p/482b32716bbe)）

## 存储区

![](http://www.liaoxuefeng.com/files/attachments/001384907702917346729e9afbf4127b6dfbae9207af016000/0)

#### 工作区（work directory）

工程目录

#### 暂存区（stage）

将文件 `git add` 之后，文件所存在的区域

#### 版本库

`git commit` 之后，文件所存在的区域

### 存储区切换（reset / checkout）

其实每操作一步（`git add` 或 `git commit`），然后 `git status` 查看状态，控制台都会有相应的提示。

修改文件并 add 到 stage 的时候，从暂存区移除（即从 stage 状态改为 unstage 状态）：

```shell
git reset HEAD FILE_NAME
```

将文件从 unstage 中移除（不可逆）：

```shell
git checkout FILE_NAME
```

## 配置（config）

如果从 GitHub 上 clone 的项目，没有 config `user.name` 与 `user.email`，无法更新 commit 数据。

本地配置（查看全局配置将 `--local` 改为 `--global` 即可）：

```shell
git config --local --list 		# 本地全部配置
git config --local user.name 	# 查看单独某项配置

git config --local user.name "USER_NAME" 	# 添加用户名
git config --local user.email "USER_EMAIL" 	# 添加用户邮箱
```

## 忽略指定文件（.gitignore）

在将自己代码提交 GitHub 时，如 CocoaPods 中的 Pods 文件夹，以及 Mac 平台下的 .DS_Store 文件，都是不需要提交的。

```tex
/Pods
.DS_Store
*.a
!lib.a
```

## 暂时存储修改（stash）

在修复 bug1 时，出现了优先级更高的 bug2，需要先将当前改动暂时存储，等待修改完 bug2 后，再恢复。

```shell
git stash			# 可多次调用，压栈
git stash save "MESSAGE"   # 压栈，并给一个 message，方便查看的时候，知道是什么 stash
git stash apply 	# 直接运用最顶层(不出栈)
git stash drop 		# 丢弃栈顶
git stash pop 		# 出栈(apply + drop)
git stash list		# 查看栈中全部记录
git stash apply stash@{0} # 直接运用第 0 个记录
```

## 将某个分支的 commit 直接移到当前分支（cherry-pick）

同样是上面的例子，正在修复 bug1 时，出现了 bug2。如果不使用 `git stash`，也可以用 `git cherry-pick`。

`cherry-pick` 将分支 1 上的某个 commit，直接运用到当前分支上。

```shell
git log -a # 可以在当前分支上，找到其他分支上的 commit，这样就不用去 checkout 到某个分支再找了
git cherry-pick COMMIT_ID
```

## 日志（log）

```shell
git log 			# 普通输出
git log --oneline 	# 单行输出
git log --a 		# 输出全部
```

## 给稳定版本打标签（tag）

在开发完一个版本后，给版本打上 tag，用于代替晦涩的 commitID。

```shell
git tag -a v1.0 -m "release v1.0"
```

tag 不会自动 push 到远程，所以需要手动 push。如果有删除需求，也需要手动删除。

```shell
git push origin v1.0
```

## 修改 commit 信息（commit --amend / rebase -i）

### 修改上一个 commit 信息

```shell
git commit --amend
```

### 修改之前某个 commit 信息 

找到要修改的 commit 的前一个 commitID（LAST_COMMIT_ID），然后：

```shell
git rebase -i LAST_COMMIT_ID
```

将要修改的 commit message 将 `pick` 改为 `r`。然后保存退出，Git 会自动断下来，让你修改 commit message。

### 合并多个 commit（squash）

与修改某个 commit 信息类似，不过是将 `pick` 改为 `s`。

## 撤销某个版本（revert）

假设当前有 1 ~ 10，10 次提交。想要撤销第 5 次提交，而第 6 ~ 10 的提交依然存在：

```shell
git log --oneline # 找到第 5 次提交的 COMMIT_ID
git revert COMMIT_ID # 撤销第 5 次提交
```

## 回滚到某个版本（reset）

与 `revert` 不同，`reset` 是回滚。如果当前有 1 ~ 10 次提交，要回滚到第 5 次，即第 5 次之后提交的内容都不要：

```shell
git log --oneline # 找到第 5 次提交的前一次的 COMMIT_ID，即第 4 次 COMMIT_ID
git reset --mixed COMMIT_ID
```

- `--mixed`：缺省值，回滚到某个版本，但是源码均保留；
- `--soft`：回滚到某个版本，但仅撤回了 commit message，如果还需要提交，commit 即可；
- `--hard`：代码完全回滚到指定版本。

如果是使用的 `--hard` 回滚，再次使用 `git log` 是看不到 6 ~ 10 的 commit 记录的。如果这个时候想要回到第 10 次提交，也是有办法的：

```shell
git reflog # 查看所有操作的记录
# 找到 reset --hard 之前的那一个 COMMIT_ID
git merge COMMIT_ID # 将之前 commit 中的文件又 merge 回去
```

所以，只要是 commit 过的代码，就不会丢失。

## 远程库URL（remote）

```shell
git remote		# 查看当前 URL
git remote -v	# 查看 fetch 与 push 的 URL
git remote rm	# 移除远程 URL
git remote add 	# 添加远程 URL
```

## 二分查找（bisect）

当某个 commit 中，出现问题时，可使用 Git 提供的二分查找，快速定位问题最初出现的 commit。

```shell
# 先确认没有问题的 COMMIT_ID
git log --oneline
# 开始二分查找
git bisect start
# 告诉 Git 当前 commit 有问题
git bisect bad
# 告诉 Git 哪个 commit 没有问题
git bisect good COMMIT_ID
# 然后 Git 便会自动 checkout 到 bad 与 good 中间的那个版本
# 自己验证是否有问题，然后告诉 Git
git bisect bad # 如果当前版本没有问题，则 git bisect good
# 经过多次查找，最终 Git 会给一个 COMMIT_ID，这个 COMMIT_ID 就是最初出问题的那个 commit
# 然后结束查找
git bisect reset
```
