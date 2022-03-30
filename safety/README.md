# Practice Safe Time travel
Traveling safe, and allow for experimentation  - when all else fails - reflog can get you home

Safety tips:

1. Don’t change history for shared (remote) branches.
2. Set up an editor that you like/know well for git operations
3. When rebasing or cherry-picking - small steeps - Do one thing at a time - until you are used to resolve conflicts
4. Use a separate branch for rebasing a feature
5. Save the game before a boss fight `git branch safegame -f` so you have a place to go back to if everything blows up
6. If all else fails - use reflog and reset to get back to safety

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

