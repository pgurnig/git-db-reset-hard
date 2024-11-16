## Level Set
Git tracks changes in a hidden directory, `.git` - this is the *Object Database*. Some authors refer to this as simply a *Database* or an *Object Store*.

The contents of files (blobs), directories (trees) and commits are stored in the folder `.git/objects`. That directory also stores annotated tags. Understanding changes to the *Object Database* helps us understand the function of Git commands and the inner workings of Git more fully.

 When conceptualizing about Git, we typically consider three (or four) areas:
   - The *Working Tree* or *Working Directory* (many of the references here favor *Working Tree*). These are the files on your computer.
   - The *Staging Area* or *Index* (many of the references here favor *Staging Area*). The result of `git add <filename>`. Think of this as organizing files for a *commit*.
   - The *Object Database*, *Object Store*, *HEAD* or local repo (this paper favors the term *Object Database*). The result of `git commit ...`.
   - We often think of a fourth area often used to collaborate with others. This is the Remote as it refers to the remote repository (i.e., GitHub, GitLab, BitBucket, CodeCommit). It's also often referred to as the *origin*; however, any Git repository can serve as the *origin* whether local or remote.

### The Four Areas
| Working Tree   | Staging Area (Index) | Object Database (HEAD) | Origin (Remote) Repo |
|:--------------:|:--------------------:|:-----------------:|:-----------:|
|       ✓        |        ✓             |         ✓         |         ✓   |


