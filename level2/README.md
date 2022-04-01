# LEVEL 2 - fixup

like amending a commit  - but for earlier commits.

**Typical cases**
>I forgot to add this file to the commit before last.

>I added the test later than the implementation - but the two things belong together

```
$ git add missingFile.txt
$ git commit --fixup=[HASH OF COMMIT TO FIX]
```

PLEASE NOTE - this will create a fixup commit in your history - that we will need to get rid of in time using rebase - more on that later

This like amend - is extremely safe when done on a private branch - and can make it clearer for a reviewer to read through what you are trying to do.


# Level 2 continued - rebase
This is the big one

Rebase is a mechanism in git that can be used for a LOT of different purposes all related to rewriting the history of the branch you are working on.


**scenario**

> your current local feature branch contains a large set of commits because it was build in a iterative and explorative manner - some changes are introduced in one commit - and removed in a later.


## Rebasing within one’s branch
Works by issuing a git rebase command - with the hash of the commit older than the parts you want to change.

```
---(A)--->(B)--->(C)--->(D)
```

```
$ git rebase -i HASH_A
```


Will show a todo-list of commits all marked with a “p” for pick (keep) - in this list you can reorder, or use one of the following commands for each line:

```
pick B message of b
pick C message of c
pick D surprise - message of d

# Rebase 89dfe4f..1fbf267 onto 89dfe4f (3 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified). Use -c <commit> to reword the commit message.
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```

so if you want to delete commit C - change the “pick” to a “d” and save the file... git will now process the todo list and drop your commit.

```
pick B message of b
d C message of c
pick D surprise - message of d
```

resulting in 
```
---(A)--->(B)--->(D*)
```

PLEASE NOTE D has a new hash - since it has a different parent than before.

**CONFLICTS** - if commit C introduced a file that commit D is changing - you will have a collision - and git will prompt you to fix it… more on that later.
Rebasing between branches
scenario - My feature branch is 3 days behind master and I am ready to “get the new stuff” … dont pull/merge in master … you can “move” your branch forward in history instead.

```
master		---(A)--->(B)--->(C)--->(D)
                         \
feature                   (K)--->(L)--->(M)
```

```
$ git fetch master
```

to make sure we have newest master

```
$ git rebase master
```

```
master      ---(A)--->(B)--->(C)--->(D)
                                       \
feature*                                (K*)-->(L*)-->(M*)
```

Please note that the feature branch is now “current” or strictly ahead of master.

## Rebasing across branches 
in place of git rebase master - which rebases your feature branch onto your local master - you can avoid the fetching and do it 

```
$ git pull --rebase origin master
```

**WARNING** - when you want to push a rebased feature branch to a remote - their history will not match - and git will ask you to pull the remote branch - THIS WILL THROW AWAY ALL THAT WE JUST DID…

use (safe)force the push

```
$ git push --force-with-lease
```

now your remote branch will be exactly like your local

```
master      ---(A)--->(B)--->(C)--->(D)
                                       \
feature*                                (K*)-->(L*)-->(M*)
```

## Conflicts
Just like when using git merge - conflicts can and will occur when doing non-trivial rebase operations.

Git will help you resolve conflicts by going back in time to the latest place where your branch and the branch you are rebasing onto share a commit.

Then it adds all the commits from the branch - and then tries to add your commits one at a time.

Like with merge it is up to you to resolve the conflicts… add the changed files … and proceed by using `git rebase --continue` when done with the individual commits.
