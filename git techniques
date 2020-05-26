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

### git commit --amend  #修改commit信息
### git push [-f] <repo_name> [<refspec>...]
e.g. 
- git push 
Works as git push <remote> , or git push __remote__ if no remote is configured for current branch.
- git push origin 
Without additional configuration, pushes the current branch to the configured upstream (remote.origin.merge configuration variable) if it has the same name as the current branch, and errors out without pushing otherwise.
- git push origin master
Find a ref that matches master in the source repository (most likely, it would find refs/heads/master), and update the same ref (e.g. refs/heads/master) in origin repository with it. If master did not exist remotely, it would be created.
- git push origin HEAD:master
Push the current branch to the remote ref matching master in the origin repository. This form is convenient to push the current branch without thinking about its local name.

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
Usually you cannot revert a merge because you do not know which side of the merge should be considered the mainline. This option specifies the parent number (starting from 1) of the mainline and allows revert to reverse the change relative to the specified parent.

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
