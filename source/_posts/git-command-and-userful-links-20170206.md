---
title: git 常用命令与学习链接
date: 2017-02-06 10:45:06
tags: [git,learn]
categories: [Tools]
---

[Pro Git 中文 第二版](https://git-scm.com/book/zh/v2)： 官网地址，权威

branch early, and branch often

### About HEAD
`HEAD` is the symbolic name for the currently checked out commit -- it's essentially what commit you're working on top of.

`HEAD` always points to the most recent commit which is reflected in the working tree. Most git commands which make changes to the working tree will start by changing `HEAD`.

Detaching `HEAD` just means attaching it to a commit instead of a branch.

### The `~` operator
Say you want to move a lot of levels up in the commit tree. It might be tedious to type `^` several times, so Git also has the tilde (`~`) operator.

The tilde operator (optionally) takes in a trailing number that specifies the number of parents you would like to ascend.

`git checkout HEAD~4`

### Branch forcing
You can directly reassign a branch to a commit with the `-f` option. So something like:
`git branch -f master HEAD~3`
moves (by force) the `master` branch to three parents behind HEAD.

### Reversing Changes in Git
There are two primary ways to undo changes in `Git` : one is using `git reset` and the other is using `git revert`.

`git reset` reverts changes by moving a branch reference backwards in time to an older commit.
`git reset HEAD~1`

While `git reset` works great for local branches on your own machine, its method of "rewriting history" doesn't work for remote branches that others are using.
In order to reverse changes and *share* those reversed changes with others, we need to use `git revert`.
`git revert HEAD`

### Git Cherry-pick
`git cherry-pick` takes on the following form:
`git cherry-pick <Commit1> <Commit2> <...>`

It's a very straightforward way of saying that you would like to copy a series of commits below your current location (`HEAD`).

Git cherry-pick is great when you know which commits you want (and you know their corresponding hashes).

### git interactive rebase
All interactive rebase means is using the `rebase` command with the `-i` option.

If you include this option, git will open up a UI to show you which commits are about to be copied below the target of the rebase. It also shows their commit hashes and messages, which is great for getting a bearing on what's what.
`git rebase -i HEAD~4`

***

## Git Remotes
Remote repositories have a bunch of great properties:
+ First and foremost, remotes serve as a great backup! Local git repositories have the ability to restore files to a previous state (as you know), but all that information is stored locally. By having copies of your git repository on other computers, you can lose all your local data and still pick up where you left off.

+ More importantly, remotes make coding social! Now that a copy of your project is hosted elsewhere, your friends can contribute to your project (or pull in your latest changes) very easily.

### What fetch doesn't do
`git fetch`, however, does not change anything about your local state. It will not update your `master` branch or change anything about how your file system looks right now.

This is important to understand because a lot of developers think that running `git fetch` will make their local work reflect the state of the remote. It may download all the necessary data to do that, but it does not actually change any of your local files.

In fact, the workflow of *fetching* remote changes and then *merging* them is so common that git actually provides a command that does both at once! That command is `git pull`.

During a clone, git creates a remote branch for every branch on the remote. It then creates a local branch that tracks the currently active branch on the remote, which is `master` in most cases.
This also explains why you may see the following command output when cloning:
`local branch "master" set to track remote branch "origin/master"`

创建远程origin的dev分支到本地，用这个命令创建本地dev分支：
`git checkout -b dev origin/dev`

### git push arguments
`git push <remote> <place>`
What is a `<place>` parameter you say? We'll dive into the specifics soon, but first an example. Issuing the command:
`git push origin master`
translates to this in English:

*Go to the branch named "master" in my repository, grab all the commits, and then go to the branch "master" on the remote named "origin." Place whatever commits are missing on that branch and then tell me when you're done.*

By specifying `master` as the "place" argument, we told git where the commits will come from and where the commits will go. It's essentially the "place" or "location" to synchronize between the two repositories.

In order to specify both the source and the destination of `<place>`, simply join the two together with a colon:
`git push origin <source>:<destination>`

### git fetch arguments
`git fetch <remote> <place>`
If you specify a place with `git fetch` like in the following command:

`git fetch origin foo`

Git will go to the `foo` branch on the remote, grab all the commits that aren't present locally, and then plop them down onto the `origin/foo` branch locally.

`git fetch origin <remote_source>:<local_dest>`

### 实用命令

**查看远程仓库详细信息**
`git remote -v`

**更改远程仓库地址**
`git remote set-url origin git@git.coding.net:jjjjjj.git`

**增加远程仓库地址，设置多个远程仓库**
`git remote set-url --add origin git@bitbucket.org:flaer.git`

**删除远程仓库地址**
`git remote remove origin`

**复制ssh key 到剪切板**
`cat ~/.ssh/id_rsa.pub | clip`


### 子模块的增加与删除

**增加**  
`git submodule add git@bitbucket.org:flamepeak/name.git themes/name`

**删除**  

Since git 1.8.5.2, two commands will do:  

```git
git rm the_submodule
rm -rf .git/modules/the_submodule
```

注意：经测试，the_submodule，文件夹会被删除，要注意
