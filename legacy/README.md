<div style="text-align: center;">
  <img src="images/github-portfolio-db-series.png" alt="git init" width="33%">
</div>

# .git Database: Hard Reset ~1
This repository demonstrates how the .git database changes over time as you make commits, modify files, and then use `git reset --hard` to revert changes. It provides a step-by-step illustration to help users understand the effects of `git reset --hard` on the Working Tree, Staging Area and Local Repo given an "initial commit", file changes, a second commit, then a reset.

For simplicity, we're assuming a single branch, `main`.

## The Steps
- Working Tree Modifications
- Update the Staging Area (`git add README.md`)
- Commit #1 (`git commit -m "Initial commit"`)
- Working Tree Modifications
- Update the Staging Area (`git add example.txt`)
- Commit #2 (`git commit -m "Add example.txt"`)
- Reset back to Commit #1 (`git reset --hard HEAD~1`)

## Level Set
Git tracks changes in a hidden directory, `.git`. The contents of files (blobs), directories (trees) are stored in .git/objects. (That directory also stores commits and tags.) `.git/objects` (and other files/directories in `.git`) tracks changes over time.

These changes appear in various states across the **Git Storage Layer** that consist of:
   - Working Tree or Working Directory (this paper favors *Working Tree*)
   - Staging Area or Index (this paper favors *Staging Area*)
   - HEAD or local repo (this paper favors the term *Local Repo*)
   - Remote as it refers to the remote repository (i.e., GitHub, GitLab, BitBucket, CodeCommit)

| Working Tree   | Staging Area (Index) | Local Repo (HEAD) | Remote Repo |
|:--------------:|:--------------------:|:-----------------:|:-----------:|
|       ✓        |        ✓             |         ✓         |         ✓   |

The initial .git directory looks like this.

<img src="images/git-init.png" alt="git init" width="60%">

The *directories* that change in the `.git` folder (or are added) are:
```
- logs
- objects
- refs
```
The *files* that change in the `.git` folder (or are added) are:
```
- COMMIT_EDITMSG
- config
- HEAD
- index
- ORIG-HEAD
```
Given that, our analysis won't be concerned with:
```
- hooks (directory)
- info (directory)
- description (file)
```

## Summary
`git reset --hard HEAD~1` is thought of as wiping history and a somewhat destructive action. While that's true for the working tree, it's not entirely true for the .git database. As we'll see by examing the contents of the .git directory over time, while HEAD and the .git/logs/refs/heads/main files point back to the previous commit, `.git/objects` still retains references to the objects introduced in Commit #2. The log history contains a trail of hashes from 000000 to the Commit #1 hash, the Commit #2 hash, then back to the Commit #1 hash.

## The Analysis
The repo begins with a version of this README.md file, then follows these steps:
```
1. git init
2. git add README.md
3. git commit -m "Initial commit"
# add example.txt to the working tree
4. git add example.txt
5. git commit -m "Add example.txt"
6. git reset --hard HEAD~1
```

In order to understand the impact of `git reset --hard HEAD~1`, we need to understand the state of the *Working Tree*, *Staging Area* and *Object Database* for both `git commit -m "Initial commit"` and `git commit -m "Add example.txt"`. Once we've reset, we can then compare "Initial commit" to our `reset`.

We'll do more than compare the commit hashes, but will examine the .git database in some detail to compare the state of .git between steps 3 and 5, then against 6 and 3.

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


<!--
20241006111402: the result of `git init`
20241006111450: the result of `git add README.md`
20241006111541: the result of `git commit -m "Initial commit"
20241006111701: the result of adding example.txt to the working tree
20241006111740: the result of `git add example.txt`
20241006111825: the result of `git commit -m "Add example.txt"`
20241006112146: the result of 'git reset --hard HEAD~1`
-->


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

## License
This project is licensed under a custom non-commerical license. See the [LICENSE](LICENSE) file for details.

