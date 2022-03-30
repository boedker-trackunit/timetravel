# LEVEL 1 - amending a commit

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
