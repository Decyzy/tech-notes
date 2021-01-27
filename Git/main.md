## 开源License的区别

六种license区别：

<img src="main.assets/1336977-20191111131043256-1517901458.png" alt="img" style="zoom: 80%;" />

## 资源

[GitHub Docs 官方中文文档](https://docs.github.com/cn/github)

[Git简易指南](https://www.bootcss.com/p/git-guide/)

## 从GitHub新建开始

- 工作目录；
- 缓存区 Index
- Head

```bash
# 从GitHub新建仓库, 本地运行git clone并进入后

# 做一些改动

# add changes to Index, i.e.
git add *
# commit to HEAD
git commit -m "代码提交信息"

# push to remote
# git push <remote name> <local name>, i.e.
git push origin main

# 查看分支
git branch
# 创建分支
git checkout -b dev
# 切换分支
git checkout main
# 删除分支
git branch -d dev
# 推送分支, i.e.
git push origin dev

# 更新本地为最新改动
git pull
# merge
git merge <branch>
# 冲突
git add <filename>
# 查看不同
git diff <source_branch> <target_branch>
```

