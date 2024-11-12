<div style="text-align: center;">
  <img src="images/github-portfolio-db-series.png" alt="" width="20%">
</div>

*This is part of a series.*

# .git Database: Hard Reset ~1
This article demonstrates how the .git database changes following this progression:
```
git init
echo "README" > "README.md"
git add README.md
git commit -m "Initial commit"
echo "Lorem ipsum" > "example.txt"
git add example.txt
git commit -m "Add example.txt"
git reset --hard HEAD~1
```

This workflow is straightforward and documented in detail [here](level-set.md).

This article provides insight into the impact of `git reset --hard` on the *Working Tree*, *Staging Area* and *Object Database* given the progression above.

For simplicity, we're assuming a single branch.

## Level Set
If you're not familiar with the concepts of a *Working Tree*, *Staging Area* or *Object Database* relative to Git, review [this](level-set.md). This contains the same detail cited above.

### Initialization of the *Object Database*
The initial .git directory looks like this:
<img src="images/git-init.png" alt="git init" width="70%">

During our review, the directories that will change in the `.git` folder (or are added) are:
```
- logs
- objects
- refs
```
The files that will change in the `.git` folder (or are added) are:
```
- COMMIT_EDITMSG
- config
- HEAD
- index
- ORIG_HEAD
```
Given that, our analysis won't be concerned with:
```
- hooks (directory)
- info (directory)
- description (file)

```
## Summary
The intent of `git reset --hard HEAD~1` is to revert the user to the previous commit - this includes the *Working Tree*, *Staging Area* and *HEAD*. As we'll see, while changes to the *Working Tree* do absolutely revert, the *Object Database* still keeps track of history including history prior to the `reset`. What happens to the *Object Database* is interesting and insightful to not only understanding how `reset` works, but also in terms of the thought process behind Git itself.

## Types of Resets
`git reset --hard` is one type of reset among three: `--hard`, `--mixed` and `--soft`. Each of these affects the *Working Tree*, *Staging Area* and *HEAD* in different ways, as we'll see below.

`git reset --hard HEAD~n` impacts all three areas. It reverts everything back to the commit specified by *n*. What's interesting is that the *Object Database* may still retain references committed after *n*, but HEAD will move *n* commits back. Think of this as starting over cleanly from the last commit where you want to start cleanly everywhere, including your local files.

| Moves HEAD | Reverts Staging | Reverts Working |
|:----------:|:---------------:|:---------------:|
|     Y      |       Y         |       Y         |

`git reset --mixed HEAD~n` impacts two areas, clearing your *Staging Area* and moving HEAD while leaving your *Working Tree* intact. In the case of `mixed`, your local files remain intact while your *Staging Area* and HEAD revert to the *n* state allowing you to redo the *index* and commit.

| Moves HEAD | Reverts Staging | Reverts Working |
|:----------:|:---------------:|:---------------:|
|     Y      |       Y         |       N         |

`git reset --soft HEAD~n` impacts only HEAD leaving your *Working Tree* and *Staging Area* intact. You might use this to further refine the *Staging Area* and *Working Tree* for a particular commit.

| Moves HEAD | Reverts Staging | Reverts Working |
|:----------:|:---------------:|:---------------:|
|     Y      |       N         |       N         |

## Two Commits Setup
The details of the commits can be found [here](two-commits.md).

## Understanding `git reset --hard HEAD~1`
We can review this on a number of levels.

- Changes in the *Object Database* from our previous `commit` step to the current `reset` step.
- Changes to the `index`.
- A comparison of the contents of `.git` from our "Initial commit" versus what transpired based on `git reset --hard HEAD~1'. We might conclude that `.git` should be identical between the two, but it's not.

First let's look at the change from our previous step, `git commit -m "Add example.txt"`.

<img src="images/dark-08-git-reset-hard.png" alt="reset hard HEAD~1">

What's interesting here is that the contents of `.git/objects` hasn't changed! Let's review the object types:
```bash
Object hash: 2ef4d42b1de7e382575a8d614517c12acab3cab6 - Type: commit
Object hash: 3be11c69355948412925fa5e073d76d58ff3afd2 - Type: blob
Object hash: 9bd9e28a95ee603c5e584689c84d6b9c4acee7cd - Type: tree
Object hash: a0f25153294a9472a721f576ae7fe6584ee2ad7c - Type: commit
Object hash: ae67265a86b2408ee3f263de0f9c6581ac7e295c - Type: tree
Object hash: e845566c06f9bf557d35e8292c37cf05d97a9769 - Type: blob
```

We've kept both commits and both trees as well as both blob objects. If we list out the objects in each snapshot, we see exactly what we would expect:
<img src="images/ls-current-state-reset-v-past-state-commit-2.png" alt="current state v past state" width="60%">

Our `reset` version shows only README.md. What we might not expect is that the *Object Database* still contains `example.txt` and the `commit` and `tree` associated with that file.

As we remarked earlier in the `git add example.txt` section, executing `git reset --hard HEAD~1` reverts the `index` to the prior commit, `git commit -m "Initial commit". We see too, in the directory comparison, that the index file is different.

<img src="images/git-ls-files-stage-git-reset-hard.png" alt="git ls-files --stage" width="60%">

<img src="images/dark-10-compare-initial-commit-to-reset-hard.png" alt="compare reset hard to original commit">

There are differences here that we might not expect, but that we can explain.

As we determined earlier, the objects `a0` and `ae` represent the commit and tree from our second `example.txt` commit. Let's take a look at 3b, although we can probably guess what it is at this point.

``` bash
git cat-file -p 3be11c69355948412925fa5e073d76d58ff3afd2
Lorem ipsum
```
This is the `example.txt` file still active in our *Object Database*.

Let's see what's going on with `logs/refs/heads/main`.

<img src="images/git-initial-commit-v-git-reset-main-graph.png" alt="initial commit to reset main graph">

Here we see that path from:
- no commit `000000` to our initial commit `2ef4d4`
- the initial commit `2ef4d4` to our second commit `a0f251`
- from the second commit `a0f251` back to `2ef4d4`

That makes sense. `reset` has pointed us back to the initial commit, but kept the history alive.

`.git/logs/HEAD` shows us the same path.

COMMIT_EDITMSG did not revert. This is because that is not a tracked file, therefore `reset` has no impact on that file.

<img src="images/git-reset-v-initial-commit-commit-msg.png" alt="commit_edit">

What is also interesting is the inclusion of the file `ORIG_HEAD`. The contents of this file is simply: `a0f25153294a9472a721f576ae7fe6584ee2ad7c` or the `commit` associated to our second commit. The purpose of this file is to provide an easy way to reference the state of HEAD before `reset'. In fact, we could then simply issue the following command to continue back here:

```bash
git reset --hard ORIG_HEAD
```


