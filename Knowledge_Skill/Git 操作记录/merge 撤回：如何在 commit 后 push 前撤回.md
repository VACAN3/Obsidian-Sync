我来帮你解决这个git merge撤销的问题。在执行 git push 之前,你可以通过以下步骤撤销错误的merge操作:

```bash
# 查看当前分支的commit历史,找到merge前的commit id
git reflog

# 将b分支重置到merge之前的状态
git reset --hard HEAD@{1}   # 或者使用具体的commit id
```

让我解释一下这个方案:

1. `git reflog` 会显示所有分支的所有操作记录,包括merge操作前的状态。你可以在输出中找到merge之前的那个commit。

2. `git reset --hard HEAD@{1}` 会将分支状态完全重置到上一次操作之前。HEAD@{1}表示"上一个操作之前的状态"。你也可以用具体的commit id替代HEAD@{1}。

如果你想确认现在的状态:
```bash
# 查看当前分支状态
git status

# 查看提交历史
git log
```

需要注意的是:
- `--hard` 参数会直接丢弃工作区的修改
- 如果你有未提交的更改想保留,建议先用 `git stash` 保存