+++
categories = ["Software devlopment"]
date = 2021-01-21T01:43:00Z
description = ""
draft = false
slug = "git-fixup"
tags = ["Software devlopment"]
title = "Git: How to fixup small mistake (autosquash)"

+++


It happens to all of us. You are working on project. Boss calls you that there is a bug. You try to debug this problem. You fix it and push it to git. After more commits you relase that one specific commit contains one very stupid mistake. One line has breakpoint inside the code which now breaks code. _Don't ask why I am so specific_.

Now one option is to push another commit which will remove this line. But this will be saved in commit history. And you might be _embarrassed_ with this.

Now luckily there is a much more elegant option how to fix. This one is called _fixup_ (convenient name). Fixup is very simple feature to rewrite _history._

Now no more talk. Simple example example will tell you all.

### 1.Example

Lets say we have project:

```
870215c 2021-01-19 Ales Lerch (HEAD -> master) Another commit
9cae9d6 2021-01-19 Ales Lerch Commit that contais bug
effba84 2021-01-19 Ales Lerch Some commit

⛵ default in example on  master
▽  
```

Simple project that contains three commits: initial, commit that contains bug we need to change and another commit. What we need is to create small change in commit `9cae9d6`. What you do is make that fix and create new commit.

```
git commit --fixup 9cae9d6
```

This will connect this new commit with the one that we need to repair. As here:

```
d31e70c 2021-01-19 Ales Lerch (HEAD -> master) fixup! Commit that contais bug
870215c 2021-01-19 Ales Lerch Another commit
9cae9d6 2021-01-19 Ales Lerch Commit that contais bug
effba84 2021-01-19 Ales Lerch Some commit
```

As you can see new commit contains special keyword `fixup!` and then same commit message as the one we try to fix.

What we have now is code is fixed, but there is still commit message on the top of the history. Here is the last thing to _smooth_ the git history.

```
git rebase effba84 -i --autosquash
```

This command will rebase git history. More what is does [here](https://git-scm.com/docs/git-rebase). The commit id is here used where git rebase should start. The switch `-i` mean interative. Gives you choice how to change history. This will show you file with commit where you can remove, reword or edit specified commits:

```
pick 9cae9d6 Commit that contais bug
fixup d31e70c fixup! Commit that contais bug
pick 870215c Another commit

# Rebase effba84..d31e70c onto effba84 (3 commands)
#
...
```

Pick means _don't touch_ the commit. However fixup will basically fix the commit. If you save the file to apply this settings. As you can see git history is clean again:

```
ac7ee17 2021-01-19 Ales Lerch (HEAD -> master) Another commit
0dd4685 2021-01-19 Ales Lerch Commit that contais bug
effba84 2021-01-19 Ales Lerch Init commit
```

One less rubish commit help to maintain clean git history. Helps you or any other member of your team.
