<div style="text-align: center;">
  <img src="images/github-portfolio-db-series.png" alt="git init" width="33%">
</div>

This is a pre-release version.

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
- index
- ORIG-HEAD
```
Given that, our analysis won't be concerned with:
```
- hooks (directory)
- info (directory)
- description (file)
- HEAD (file)
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
The goal is to compare `git commit -m "Initial commit"` with `git commit -m "Add example.txt"` to show the differences; then to show the result of `git reset --hard HEAD~1` back to the *initial commit* to understand the impact on the Working Tree, Staging Area and Local Repo.

We'll do more than compare the commit hashes, but will examine the .git database in some detail to compare the state of .git between steps 3 and 5, then against 6 and 3.

### Comparing Steps 3 and 5
![.git Comparison between Steps 3 and 5 - the first and second commits](images/compare-steps-03-and-05.png)

The image shows newly added files in green, updated files in blue. Let's review.
- example.txt: a new file added to the working tree
- logs: shows the trail of commits on `main`; with `git reset --hard HEAD~1` we'll be back to only line 1 as shown below

#### Logs Diff
![.git logs diff](images/compare-steps-03-and-05-logs-diff.png)

### Comparing Steps 5 and 6
![.git Comparison between Steps 5 and 6 - the second commit and the hard reset](images/compare-steps-05-and-06.png)

#### Logs Diff
![compare-steps-05-and-06-logs-diff](images/compare-steps-05-and-06-logs-diff.png)

What's fascinating with this is that we didn't lose all history. There's still a reference to the `3a23da` commit in the log history.

### Comparing Steps 6 and 3

<!--
20241006111402: the result of `git init`
20241006111450: the result of `git add README.md`
20241006111541: the result of `git commit -m "Initial commit"
20241006111701: the result of adding example.txt to the working tree
20241006111740: the result of `git add example.txt`
20241006111825: the result of `git commit -m "Add example.txt"`
20241006112146: the result of 'git reset --hard HEAD~1`
-->


## License
This project is licensed under a custom non-commerical license. See the [LICENSE](LICENSE) file for details.

