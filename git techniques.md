## Official Docs:
https://git-scm.com/docs

## Understanding Repository, Working Tree and Index
https://backlog.com/git-tutorial/git-workflow/

## Configuration
```
git config --global user.name "y00503458"
git config --global user.email "yujialiang1@huawei.com"
```

确认配置是否正确：
```
git config -l 
```

### git commit --amend  
修改上一个commit
Amending a Commit Without Changing Its Message:
```git commit --amend --no-edit```

If you haven’t pushed the last commit yet to your remote, a single push is enough. Otherwise, you’ll have to push using the -f option since you’ve rewritten your commit history:
```git push -f origin some_branch```

Editing a Commit Without Opening a File:
```git commit --amend -m "Your new commit message"```

Remember: __NEVER__ rewrite the commit history of public branches (like master). This will truly mess your teammates work.

### git push [-f] <repo_name> [<refspec>...]
e.g. 
- git push 
Works as git push <remote> , or git push __remote__ if no remote is configured for current branch.
- git push origin 
Without additional configuration, pushes the current branch to the configured upstream (remote.origin.merge configuration variable) if it has the same name as the current branch, and errors out without pushing otherwise.
- git push origin master
Find a ref that matches master in the source repository (most likely, it would find refs/heads/master), and update the same ref (e.g. refs/heads/master) in origin repository with it. If master did not exist remotely, it would be created.
- git push origin HEAD:master
Push the current branch to the remote ref matchigit ng master in the origin repository. This form is convenient to push the current branch without thinking about its local name.

### git reset [<mode>] [<commit>]
This form resets the current branch head to <commit> and possibly updates the index (resetting it to the tree of <commit>) and the working tree depending on <mode>. If <mode> is omitted, defaults to --mixed. The <mode> must be one of the following:

--soft
Does not touch the index file or the working tree at all (but resets the head to <commit>, just like all modes do). This leaves all your changed files "Changes to be committed", as git status would put it.

--mixed
Resets the index but not the working tree (i.e., the changed files are preserved but not marked for commit) and reports what has not been updated. This is the default action.

If -N is specified, removed paths are marked as intent-to-add (see git-add[1]).

--hard
Resets the index and working tree. Any changes to tracked files in the working tree since <commit> are discarded.

### git revert [-m parent-number] <commit>…
Given one or more existing commits, revert the changes that the related patches introduce, and __record some new commits__ that record them. This __requires your working tree to be clean__ (no modifications from the HEAD commit).
__Note:__ git revert is used to record some new commits to reverse the effect of some earlier commits (often only a faulty one). 

-m parent-number
--mainline parent-number
Usually you cannot revert a __merge__ because you do not know which side of the merge should be considered the mainline. This option specifies the parent number (starting from 1) of the mainline and allows revert to reverse the change relative to the specified parent.

When you view a merge commit in the output of git log, you will see its parents listed on the line that begins with Merge:
commit 8f937c683929b08379097828c8a04350b9b8e183
Merge: 8989ee0 7c6b236
Author: Ben James <ben@example.com>
Date:   Wed Aug 17 22:49:41 2011 +0100
Merge branch 'gh-pages'
Conflicts:
    README
In this situation, ```git revert 8f937c6 -m 1```will get you the tree as it was in ```8989ee0```, and ```git revert -m 2```will reinstate the tree as it was in ```7c6b236```.
To better understand the parent IDs, you can run:
git log 8989ee0 
and
git log 7c6b236


### <pathspecs> in git
It can be an absolute path or relative path to current working directory.

### git merge 
Join two or more development histories together
Incorporates changes from the named commits (since the time their histories diverged from the current branch) into the current branch. This command is used by __git pull__ to incorporate changes from another repository and can be used by hand to merge changes from one branch into another.

__Examples:__
Merge branches fixes and enhancements on top of the current branch, making an octopus merge:
```
$ git merge fixes enhancements
```
>octopus
This resolves cases with more than two heads, but refuses to do a complex merge that needs manual resolution. It is primarily meant to be used for bundling topic branch heads together. This is the default merge strategy when pulling or merging more than one branch.

Merge branch obsolete into the current branch, using ours merge strategy:
```
$ git merge -s ours obsolete
```
Merge branch maint into the current branch, but do not make a new commit automatically:
```
$ git merge --no-commit maint
```
This can be used when you want to __include further changes__ to the merge, or want to write your own merge commit message.

P.S. You should refrain from abusing this option to sneak substantial changes into a merge commit. Small fixups like bumping release/version name would be acceptable.


### git rebase
In Git, there are two main ways to integrate changes from one branch into another: the merge and the rebase.With the rebase command, you can take all the changes that were committed on one branch and replay them on a different branch.
For this example, you would check out the experiment branch, and then rebase it onto the master branch as follows:
```
$ git checkout experiment
$ git rebase master
```
This operation works by going to the common ancestor of the two branches (the one you’re on and the one you’re rebasing onto), getting the diff introduced by each commit of the branch you’re on, saving those diffs to temporary files, resetting the current branch to the same commit as the branch you are rebasing onto, and finally applying each change in turn.

Note that the snapshot pointed to by the final commit you end up with, whether it’s the last of the rebased commits for a rebase or the final merge commit after a merge, is the same snapshot —  __it’s only the history that is different__. Rebasing replays changes from one line of work onto another in the order they were introduced, whereas merging takes the endpoints and merges them together.

### Squash commits into one
https://www.internalpointers.com/post/squash-commits-into-one-git

### git tag
Like most VCSs, Git has the ability to tag specific points in a repository’s history as being important. Typically, people use this functionality to mark release points (v1.0, v2.0 and so on).

__Listing Your Tags__
Listing the existing tags in Git is straightforward. Just type git tag (with optional -l or --list):
This command lists the tags in alphabetical order; the order in which they are displayed has __no__ real importance.

You can also search for tags that match a particular pattern. The Git source repo, for instance, contains more than 500 tags. If you’re interested only in looking at the 1.8.5 series, you can run this:
```
$ git tag -l "v1.8.5*"
```

__Creating Tags__
Git supports two types of tags: lightweight and annotated.
- A lightweight tag is very much like a branch that doesn’t change — it’s just a pointer to a specific commit.
- Annotated tags, however, are stored as full objects in the Git database. They’re checksummed; contain the tagger name, email, and date; have a tagging message; and can be signed and verified with GNU Privacy Guard (GPG).

__Annotated Tags__
The easiest way is to specify -a when you run the tag command:
```$ git tag -a v1.4 -m "my version 1.4"```
The -m specifies a tagging message, which is stored with the tag.
You can see the tag data along with the commit that was tagged by using the git show command:
```
$ git show v1.4
tag v1.4
Tagger: Ben Straub <ben@straub.cc>
Date:   Sat May 3 20:19:12 2014 -0700

my version 1.4

commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Mon Mar 17 21:52:11 2008 -0700

    Change version number
```
__Lightweight Tags__
To create a lightweight tag, don’t supply any of the -a, -s, or -m options, just provide a tag name:
```$ git tag v1.4-lw```

__Sharing Tags__
By default, the git push command doesn’t transfer tags to remote servers. You will have to explicitly push tags to a shared server after you have created them. This process is just like sharing remote branches — you can run 
```git push origin <tagname>```

If you have a lot of tags that you want to push up at once, you can also use the --tags option to the git push command. This will transfer all of your tags to the remote server that are not already there.

### git stash
Stash the changes in a dirty working directory away
Use git stash when you want to record the current state of the working directory and the index, but want to go back to a clean working directory. The command saves your local modifications away and reverts the working directory to match the HEAD commit.

The modifications stashed away by this command can be listed with ```git stash list```, inspected with ```git stash show```, and restored (potentially on top of a different commit) with ```git stash apply```. Calling ```git stash``` without any arguments is equivalent to ```git stash push```. A stash is by default listed as "WIP on branchname …​", but you can give a more descriptive message on the command line when you create one.

pop [--index] [-q|--quiet] [<stash>]
Remove a single stashed state from the stash list and apply it on top of the current working tree state, i.e., do the inverse operation of git stash push. The working directory must match the index.

git stash --all which stashes all files, including __untracked__ and ignored files.
git stash --include-untracked(-u) no longer touches ignored files

Pulling into a dirty tree
```
$ git pull
 ...
file foobar not up to date, cannot merge.
$ git stash
$ git pull
$ git stash pop
```

Interrupted workflow
```
# ... hack hack hack ...
$ git stash
$ edit emergency fix
$ git commit -a -m "Fix in a hurry"
$ git stash pop
# ... continue hacking ...
```

### git restore
git restore [<options>] [--source=<tree>] [--staged] [--worktree] [--] <pathspec>…​
Restore specified paths in the working tree with some contents from a restore source. If a path is tracked but does not exist in the restore source, it will be removed to match the source. The command can also be used to restore the content in the index with --staged, or restore both the working tree and the index with --staged --worktree. By default, the restore sources for working tree and the index are the index and HEAD respectively. --source could be used to specify a commit as the restore source.

-s <tree>
--source=<tree>
Restore the working tree files with the content from the given tree. It is common to specify the source tree by naming a commit, branch or tag associated with it.

If not specified, the default restore source for the working tree is the __index__, and the default restore source for the index is HEAD. When both --staged and --worktree are specified, --source must also be specified.
```$ git restore --source=HEAD --staged --worktree hello.c```
or the short form which is more practical but less readable:
```$ git restore -s@ -SW hello.c```


### git switch
(added in Git v2.23)
__<branch-name>__
The name of a local or remote branch that you want to switch to. If you specify the name of an existing local branch, you will switch to this branch and make it the current "HEAD" branch.
But you can also specify a remote branch: in that case, Git will create a new local branch based on that remote branch and set up a tracking relationship.

__-c <new-name>__
The name of a new local branch you want to create. Using the "-c" flag, you can specify a name for a new branch that should be created. You can also specify a starting point (either another branch or a concrete revision); if you don't provide any specific starting point, the new branch will be based on the current HEAD branch.

__<branch-name> --discard-changes__
Switch to the specified branch and discard any local changes to obtain a clean working copy. As a general rule, your working copy does NOT have to be clean before you can use "switch". However, if you have local modifications that would conflict with the switched-to branch, Git would abort the switch. Using the "--discard-changes" flag will discard any of your current local changes and then switch to the specified branch.

__\-__
Switch back to the previous branch. When specifying just the "-" character instead of a branch name, Git will switch back to the last checked out branch. This can be helpful if you want to often and quickly jump between two branches.

### git switch v.s. git checkout ?
```git checkout``` is a bit of a swiss army knife in that has several unrelated uses.

If you modify a file but haven't staged the change, then ```git checkout <filename>``` will reverse the modifications... a quick and easy way to cancel changes to a file. You remain in the same branch.```git checkout <branchname>``` (as you noted) switches branches.
Two completely different purposes, which could lead to confusion if a file name and a branch name are similar.
Having it as two commands is clearer.


### Git Error: Filename too long 报错
 1、修改windows的文件路径限制
window10左下角搜索“regedit”，发现“注册表编辑器”，进入到路径“计算机\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem”中，显示界面的右边双击“LongPathsEnabled”，将“数值数据”从“0”改为“1”，确认退出。

2、windows10右下角“win”右键选择“运行”，进入路径“计算机配置\管理模板\系统\文件系统”，界面右侧将“启用Win32长路径”的状态修改为“已启用”。

3、进入到项目根路径，打开git bash界面，执行命令
```
git config --global core.longpaths true
```
注意
1、执行第一步和第二步需要重启电脑后生效
2、如果执行第三步报错：
```
error: could not lock config file /pathto/file/.gitconfig: No such file or directory
```
尝试了网上很多方法，无效，删除整个项目文件然后重新clone项目后再执行第三步，不报错误，问题解决。

### Syncing a fork
Foreword: Your fork is the "origin" and the repository you forked from is the "upstream".

Let's assume that you cloned already your fork to your computer with a command like this:
```
git clone git@github.com:your_name/project_name.git
cd project_name
```
If that is given then you need to continue in this order:

Add the "upstream" to your cloned repository ("origin"):

`git remote add upstream git@github.com:original_author/project_name.git`
Fetch the commits (and branches) from the "upstream":

`git fetch upstream`
Switch to the "master" branch of your fork ("origin"):

`git checkout master`
Stash the changes of your "master" branch:

`git stash`
Merge the changes from the "master" branch of the "upstream" into your the "master" branch of your "origin":

`git merge upstream/master`
Resolve merge conflicts if any and commit your merge

`git commit -am "Merged from upstream"`
Push the changes to your fork

`git push`
Get back your stashed changes (if any)

`git stash pop`
You're done! Congratulations!

GitHub also provides instructions for this topic: Syncing a fork
