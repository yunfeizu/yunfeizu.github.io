---
layout: post
title: "git分支管理"
date: 2013-04-23 17:55
comments: true
keywords: git, branch, merge, svn
categories: Git
---
### Git的分支
Git 的一个显著特点就是Branch 和 Merge 非常的方便

###删除远程分支
As of Git v1.7.0, you can delete a remote branch using
git push origin --delete <branchName>

git push origin :<branchName>
which was added in Git v1.5.0 "to delete a remote branch or a tag."
