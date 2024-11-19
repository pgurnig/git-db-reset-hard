### The Git Object Database across two commits

We'll consider changes to the *Object Database* following this set of commands.
```bash
git init
echo "README" > "README.md"
git add README.md
git commit -m "Initial commit"
echo "Lorem ipsum" > "example.txt"
git add example.txt
git commit -m "Add example.txt"
```

#### git init / echo "README" > "README.md"
##### git init
In the screenshot on the left below, we can see the initial state of .git. In many of our analyses, we don't consider the `hooks` folder, so we're leaving that collapsed for this analysis.

##### echo "README" > "README.md"
Creating the README.md file incurs no changes on the .git *Object Database*, but does create a change to our *Working Tree*.
<!-- ![alt text](images/light-02-echo-readme.png) -->
![alt text](newimages/light-01-bc-git-init-21-v-22.png) 

#### git add README.md
`git add README.md` adds a folder and file to `.git/objects` and also, given nothing staged yet, adds the `index` file.
![alt text](newimages/light-02-bc-echo-readme-22-v-24.png) 

Using `git cat-file` with the appropriate switches, we can discover the object type for the `e8/45566` folder and file:

![alt text](images/git-cat-file-t-readme.png)

Using `git cat-file -p`, we can view the contents:

![alt text](images/git-cat-file-p-readme.png)

This blob is the SHA-1 hash of metadata and the file contents.

The `index` file is tracking the changes we're introducing for a future commit.

#### git commit -m "Initial commit"
With `git commit`, we see additional changes to the *Object Database*.
<!-- ![alt text](images/light-04-git-commit.png) -->
![alt text](newimages/light-03-bc-git-add-24-v-25.png)

- Our `e8` object remains unchanged, but we have two new objects beginning with `9b` and `c5`.
- `refs/heads` now has a file `main`.
- There's a new file, `COMMIT_EDITMSG`.
- The `index` file that was introduced in the prior step (`git add <filename>`) has changed.
- There's a new `logs` directory with several additions.

Let's look at each of these in turn.

#### echo "Lorem ipsum" > "example.txt"
<!-- ![alt text](images/light-05-git-echo-example.png) -->
![alt text](images/dark-05-git-echo-example.png) 

#### git add example.txt
<!-- ![alt text](images/light-06-git-add-example.png) -->
![alt text](images/dark-06-git-add-example.png) 

#### git commit -m "Add example.txt"
<!-- ![alt text](images/light-07-git-commit.png) -->
![alt text](images/dark-07-git-commit.png) 