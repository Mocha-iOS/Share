## 存储区

## 存储区切换（reset/checkout）

## 配置（config）

如果从 GitHub 上 clone 的项目，没有 config `user.name` 与 `user.email`，无法更新 commit 数据。

## 忽略指定文件（.gitignore）

在将自己代码提交 GitHub 时，如 CocoaPods 中的 Pods 文件夹，以及 Mac 平台下的 .DS_Store 文件，都是不需要提交的。

## 暂时存储修改（stash）

在修复 bug1 时，出现了优先级更高的 bug2，需要先将当前改动暂时存储，等待修改完 bug2 后，再恢复。

## 日志（log）

```shell
git log
git log --oneline
git log --a
```

## 给稳定版本打标签（tag）

在开发完一个版本后，给版本打上 tag，用于代替晦涩的 commitID。

tag 不会自动 push 到远程，所以需要手动 push。如果有删除需求，也需要手动删除。

## 修改上一个 commit 信息（commit --amend）

## 修改之前某个 commit 信息 （rebase -i）

## 合并多个 commit（rebase -i）

## 撤销某个版本（revert）

## 回滚到某个版本（reset）

## 查看/添加远程库URL（remote）