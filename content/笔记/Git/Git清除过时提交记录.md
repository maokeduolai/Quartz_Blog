---
title: Git清除过时提交记录
date: 2024-12-15
tags:
    - Git
---

当使用Git作为Obsidian的版本管理工具时，往往会产生非常多的提交记录，这会大大增加仓库负担。

在具有上百条提交记录后，需要使用一定方法来清理过时的提交，可以参考以下步骤实现。该方法可以实现清除特定某次提交之前的提交记录。

>[!danger] 务必在尝试操作前，备份完整仓库！！！

1. 使用 `git log` 查找你想要保留的最近一次提交的哈希值（例如，`<commit-hash>`）。
2. 使用 `git checkout --orphan temp-branch` 创建一个新的分支，且该分支没有任何提交历史。

   >[!info]
   >`--orphan` 选项用于创建一个没有父提交的新分支。换句话说，它会创建一个孤立的分支，分支上没有任何提交历史记录。这样可以在这个新分支上从头开始提交，而不会继承任何现有的提交历史。

3. 使用 `git add .` 和 `git commit -m "Clean old commit"` 提交当前的工作目录。
4. 使用 `git rebase --onto temp-branch <commit-hash> master` 将 `master` 分支 `<commit-hash>` 后的所有提交应用到 `temp-branch` 上，并将 `master` 分支变基到当前最新的 `temp-branch` 提交记录上。

   >此处仅保留 `<commit-hash>` 后的提交记录。具体请参见文末的举例解释。

5. 使用 `git branch -d temp-branch` 删除临时的 orphan 分支。
6. 使用 `git push --force` 强制推送到远程仓库。

这样就完成了过时提交记录的清除操作。

>[!example] 第4步骤较为复杂，可能产生误解，因此以下特别为其做举例说明。
>
>假设原 `master` 分支是 `A---B---C---D---E---F---G`，选中的 `<commit-hash>` 是 `C` 记录对应的哈希值。
>
>执行 `git commit -m "Clean old commit"`，这会在 `temp-branch` 上创建一个新的提交，假设这个提交的哈希值为 `H`。
>
>执行`git rebase --onto temp-branch <commit-hash> master`，这会将 `master` 分支 `C` 后的所有提交（即 `D---E---F---G`）应用到 `temp-branch` 上，并将 `master` 分支变基到当前最新的 `temp-branch` 提交记录上。结果如下：
>
>```
>HEAD -> master: H---D'---E'---F'---G'
>```
>
>>[!info]
>>`D'`, `E'`, `F'`, `G'` 是重新应用后的提交，哈希值会不同于原来的 `D`, `E`, `F`, `G`
>
>这样 `master` 分支的提交历史会变成：
>
>`H---D'---E'---F'---G'`
>
>原来的 `A---B---C` 提交记录将被移除。