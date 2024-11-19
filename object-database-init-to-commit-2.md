### The Git Object Database across two commits

#### echo "README" > "README.md"
Creating the README.md file incurs no changes on the .git *Object Database*, but does create a change to our *Working Tree*.
<!-- ![alt text](images/light-02-echo-readme.png) -->
![alt text](newimages/light-01-bc-git-init-21-v-22.png) 

#### git add README.md
`git add README.md` adds a folder and file to `.git/objects` and also adds the `index` file.
<!-- ![alt text](images/light-03-git-add-readme.png) -->
![alt text](newimages/light-02-bc-echo-readme-22-v-24.png) 

Using `git cat-file -t`, we can discover the object type for the `e8/45566` folder and file:

![alt text](images/git-cat-file-t-readme.png)

Using `git cat-file -p`, we can view the contents:

![alt text](images/git-cat-file-p-readme.png)

This blob is the SHA-1 hash of metadata and the file contents.

The `index` file is tracking the changes we're introducing for a future commit.

#### git commit -m "Initial commit"
<!-- ![alt text](images/light-04-git-commit.png) -->
![alt text](newimages/light-04-bc-git-commit-25-v-26.png) 

#### echo "Lorem ipsum" > "example.txt"
<!-- ![alt text](images/light-05-git-echo-example.png) -->
![alt text](images/dark-05-git-echo-example.png) 

#### git add example.txt
<!-- ![alt text](images/light-06-git-add-example.png) -->
![alt text](images/dark-06-git-add-example.png) 

#### git commit -m "Add example.txt"
<!-- ![alt text](images/light-07-git-commit.png) -->
![alt text](images/dark-07-git-commit.png) 