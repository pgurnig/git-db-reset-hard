## The Detail
### Table of Contents
- [Overview](#overview)
- [Getting Started](#getting-started)
- [Workflow](#workflow)
- [Understanding the .git Directory](#understanding-the-git-directory)
- [Observing Changes](#observing-changes)
- [Contributing](#contributing)
- [License](#license)

### Overview
Git is a powerful version control system that tracks changes to files. Sometimes, you may want to revert your working directory to match a previous commit. This can be done using `git reset --hard`, which can alter the Git history and working directory, along with the contents of the `.git` directory.

This repository walks you through an example of how the `.git` directory evolves as changes are made, committed, and then reverted with `git reset --hard`.

One particularly useful comparison for git and especially for being able to experiment freely is illustrated by the concept of git as a "laboratory notebook" of sorts. In the paper

> "Just as experiments are logged in laboratory notebooks, it is important to document the code you use for analysis."
>
> — John D. Blischak, Emily R. Davenport, Greg Wilson, *A Quick Introduction to Version Control with Git and GitHub*

### `git reset` Types
There are three types of resets:
1. **hard**: the type of reset we're exploring here
2. **mixed**: the default type executed when you simply type `git reset`
3. **soft**: `git reset --soft`

These three types may or *may not* impact each of:
Working Directory
Index (Staging Area)
HEAD

For `git reset --hard`, each of these areas is impacted.

| Working Tree  | Index (Staging) |     HEAD     |
|:-------------:|:---------------:|:------------:|
|      ✓        |        ✓        |      ✓       |

### The `reset` areas
A complete explanation of Working Tree, Index (Staging) and HEAD is beyond the scope of this document, but we'll discuss each briefly.

- Working Tree: your local filesystem that may contain committed or uncommitted files
- Index or Staging Area: the area where you stage the files you intend to commit. i.e., the result of `git add <filename>`
- HEAD: a reference to the current commit

### Workflow
The following workflow shows how changes in the `.git` database occur through various operations. You'll be able to track how the Git object database evolves, and how history changes with each step:

1. **init, add and commit**: We create our initial .git state.
   ```bash
   git init
   git add README.md
   git commit -m "Initial commit"
   ```
2. **Examine directory changes**

3. **Make another commit**: Add a new file and commit it.
   ```bash
   echo "Lorem ipsum" >> example.txt
   git add example.txt
   git commit -m "Second commit"
   ```
4. **Examine directory changes**

5. **Perform a hard reset**: Use `git reset --hard` to revert to the first commit.
   ```bash
   git reset --hard HEAD~1
   ```

<!--
3. **Examine the .git directory**: Check the `.git` directory to observe how objects and references change with each commit.
   ```bash
   ls .git/objects
   ```


5. **Re-examine the .git directory**: After the reset, check the `.git` directory again to see how the commit history has been rewritten.
   ```bash
   ls .git/objects
   ```
-->
### Understanding the .git Directory
The `.git` directory is the hidden folder where Git stores all the information about your repository. Some important components:
- **Objects**: Stores the actual file contents, commit objects, trees, and blobs.
- **Refs**: Contains references to commits, such as branches and tags.
- **HEAD**: Points to the current branch and the latest commit.

By exploring the `.git` directory after each operation, you will gain a better understanding of how Git manages your project's history.

### Observing Changes
During the steps above, you can observe the following key changes:
- New **objects** are created each time you make a commit.
- The **HEAD** reference is updated to point to the new commit.
- After `git reset --hard`, the **HEAD** and working directory are rolled back to match the previous state.

These changes allow you to see how Git keeps track of your project's history and how `git reset --hard` alters that history.
