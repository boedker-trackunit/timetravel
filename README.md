# Git time travel

Demystifying git time travel - short demo for front end fan club.

This article does not try to recommend any workflow - it is only showing the possibilities for re-writing history in a git repository.

First rule of time travel:
> don't do it in public


The different mechanisms for working with history should only be used on private branches 99% of the time.

Assumptions for this article - you are familiar with git for creating (feature-) branches and viewing the history of these


Please note that there are many ways of reaching a goal using git these examples are only that examples...

--- 

## LEVEL 1 - amending a commit

Amend adds content to the newest commit in the history of the current branch.

**Typical case**

> you forgot to add a file to the latest commit on your local feature branch


Don't make a new commit - with the message “forgot to add a file” - change the last commit - via the amend mechanism.


Instead of making another commit with a vague commit message - you can time travel back to change the commit you just did - and make sure that is DOES include the missing file or change.


```console
$ git add missed-file.txt
$ git commit --amend 
```

Git will give you the option to rewrite the commit message - or keep it as it was…

the previous commit is now changed - and includes all the changes from before AND the changes in missing-file.txt.

This is extremely safe when done on a private branch - and can make it clearer for a reviewer to read through what you are trying to do.

**Why do this**

This will make it easier for a fellow developer to understand your code - since the changes that belongs together is in a single commit - with its awesome commit message.

It will also make it easier to minimize the amount of commits involved in a collision.

---

## LEVEL 2 - fixup
**Typical cases**
>I forgot to add this file to the commit before last.

>I added the test later than the implementation - but the two things belong together

```
$ git add missingFile.txt
$ git commit --fixup=[HASH OF COMMIT TO FIX]
```

PLEASE NOTE - this will create a fixup commit in your history - that we will need to get rid of in time using rebase - more on that later

This like amend - is extremely safe when done on a private branch - and can make it clearer for a reviewer to read through what you are trying to do.


## Level 2 continued - rebase
This is the big one

Rebase is a mechanism in git that can be used for a LOT of different purposes all related to rewriting the history of the branch you are working on.


**scenario**

> your current local feature branch contains a large set of commits because it was build in a iterative and explorative manner - some changes are introduced in one commit - and removed in a later.


### Rebasing within one’s branch
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
master		---(A)--->(B)--->(C)--->(D)
                                       \
feature*                                (K*)-->(L*)-->(M*)
```

Please note that the feature branch is now “current” or strictly ahead of master.

### Rebasing across branches 
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
master		---(A)--->(B)--->(C)--->(D)
		                                 \
feature*                                (K*)-->(L*)-->(M*)
```

### Conflicts
Just like when using git merge - conflicts can and will occur when doing non-trivial rebase operations.

Git will help you resolve conflicts by going back in time to the latest place where your branch and the branch you are rebasing onto share a commit.

Then it adds all the commits from the branch - and then tries to add your commits one at a time.

Like with merge it is up to you to resolve the conflicts… add the changed files … and proceed by using `git rebase --continue` when done with the individual commits.

--- 

## LEVEL 3 - cherry-pick

```
master      ---(A)--->(B)--->(C)--->(D)
                         \             \
> feature                 \             (K)-->(L)-->(M)
                           \
feature2                    (X)-->(Y)-->(Z)
```

Scenario: stealing or borrowing a commit from another branch 

```
$ git cherry-pick HASH_OF_Y
```


```
master      ---(A)--->(B)--->(C)--->(D)
                         \             \
> feature                 \             (K)-->(L)-->(M)-->(Y*)
                           \
feature2                    (X)-->(Y)-->(Z)
```

--- 

## Practice Safe Time travel
Traveling safe, and allow for experimentation  - when all else fails - reflog can get you home

Safety tips:

1. Don’t change history for shared (remote) branches.
2. Set up an editor that you like/know well for git operations
3. When rebasing or cherry-picking Do one thing at a time - until you are used to resolve conflicts
4. Use a separate branch for rebasing a feature
5. Save the game before a boss fight `git branch safegame -f` so you have a place to go back to if everything blows up
6. If all else fails - use reflog

```
$ git reflog 
```
shows you EVERYTHING that has happened in the current repo on your machine

```
boedker@TU-VirtualBox:~/source/git-timetravel$ git reflog
dd54cc6 (HEAD -> main, origin/main) HEAD@{0}: rebase -i (finish): returning to refs/heads/main
dd54cc6 (HEAD -> main, origin/main) HEAD@{1}: rebase -i (squash): Add Presentation
e63090d HEAD@{2}: rebase -i (squash): # This is a combination of 3 commits.
49573e3 HEAD@{3}: rebase -i (squash): # This is a combination of 2 commits.
89dfe4f HEAD@{4}: rebase -i: fast-forward
7e3deba HEAD@{5}: rebase -i (start): checkout 7e3deba9ab2822d264b7d256ffabb361f898436a
053056e (presentation) HEAD@{6}: rebase finished: refs/heads/main onto 053056e25802de499b30decef78c22201974020d
053056e (presentation) HEAD@{7}: rebase: checkout presentation
83b7648 (tag: main-prestine) HEAD@{8}: checkout: moving from presentation to main
053056e (presentation) HEAD@{9}: commit (amend): added structure and text to presentation
19db893 HEAD@{10}: commit (amend): added structure and text to presentation
1fbf267 HEAD@{11}: commit (amend): added structure and text to presentation
66742f0 HEAD@{12}: commit: added structure and text to presentation
83b7648 (tag: main-prestine) HEAD@{13}: checkout: moving from main to presentation
83b7648 (tag: main-prestine) HEAD@{14}: checkout: moving from subfeature1 to main
2954625 (tag: subfeature1-prestine, origin/subfeature1, subfeature1) HEAD@{15}: checkout: moving from main to subfeature1
83b7648 (tag: main-prestine) HEAD@{16}: checkout: moving from feature1 to main
3f3bc77 (tag: feature1-prestine, origin/feature1, feature1) HEAD@{17}: checkout: moving from subfeature1 to feature1
2954625 (tag: subfeature1-prestine, origin/subfeature1, subfeature1) HEAD@{18}: checkout: moving from feature1 to subfeature1
3f3bc77 (tag: feature1-prestine, origin/feature1, feature1) HEAD@{19}: checkout: moving from main to feature1
```

and you can do a reset to any point 

```
$ git reset --hard 83b7648
```

and now EVERYTHING after (above) this commit never happened...




















