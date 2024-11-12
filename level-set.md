## Level Set
<small>

> If you're already familiar with the *Working Tree*, *Staging Area* and *Object Database*, feel tree to skip to the **Summary** or **Detail** section.
</small>
 
Git tracks changes in a hidden directory, `.git` - this is the *Object Database*. This data store contains a wealth of useful information.

The contents of files (blobs), directories (trees) and commits are stored in the folder `.git/objects`. That directory also stores annotated tags, not in scope for this article. Understanding changes to the *Object Database* helps us understand the inner workings of Git more fully.

 We typically consider a workflow involving three (or four) areas:
   - The *Working Tree* or *Working Directory* (this paper favors *Working Tree*). These are the files on your computer that you edit with something such as VS Code or Vim.
   - The *Staging Area* or *Index* (this paper favors *Staging Area*). The result of `git add <filename>`. This area organizes files for a *commit*.
   - The Object Database, Object Store, HEAD or local repo (this paper favors the term *Object Database*). The result of `git commit ...`.
   - We can consider a fourth area often used to collaborate with others. This is the Remote as it refers to the remote repository (i.e., GitHub, GitLab, BitBucket, CodeCommit)

| Working Tree   | Staging Area (Index) | Object Database (HEAD) | Remote Repo |
|:--------------:|:--------------------:|:-----------------:|:-----------:|
|       ✓        |        ✓             |         ✓         |         ✓   |

We'll consider changes to the *Object Database* as our discussion topic.