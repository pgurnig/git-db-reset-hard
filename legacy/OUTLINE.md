| README                                      | README2                       | Article (git-reset-hard.md)                 |
|:--------------------------------------------|:------------------------------|:--------------------------------------------|
| [x] Introduction                            | [x] Introduction              |                                             |
| [x] The Steps                               | [x] Nomenclature              | [x] Level Set                               |
| [x] Summary                                 | [x] Our Steps                 | [x] Summary                                 |
| [x] The Analysis                            | [x] Explanation: Step-by-Step | [x] Types of Resets                         |
| [x] Understanding `git reset --hard HEAD~1` | [x] Screenshots: Step-by-Step | [x] Two Commits Setup                       |
| [x] Types of Resets                         |                               | [x] Understanding `git reset --hard HEAD~1` |


| Final Version                              |
|:-------------------------------------------|
| [x] Introduction                           |
| [x] Our Steps                              |
| [x] Level Set                              |
| [x] Summary                                |
| [x] Types of Resets                        |
| [ ] Lorem                                  |

### Plan
- They all have introductions which we can compare and combine.
- They all have, or should have, a list of the steps which we can compare and combine.
- At least two have a *Nomenclature* or *Level Set* section that we can break out into a separate file.
- At least two have a *Summary* that can be included inline.
- At least two have a *Types of Resets* that can be either included inline or as a separate file.
- Two seem to have some sort of detail sections such as *The Analysis* (README), *Explanation: Step-by-Step* (README2); those can be combined and included inline.
- *Two Commits Setup* (Article) seems to be incomplete and can be ignored.
- Review *Understanding `git reset --hard HEAD~1`* in two of the drafts.

### New Outline
- [x] Combine *Introductions*.
- [x] Combine List of Steps.
- [ ] Determine whether *Level Set* should go before *Summary*.
- [ ] Determine whether *Level Set* should be *Nomenclature*.
- [x] Combine *Summary*.
- [x] Combine *Types of Resets*.
- [ ] Determine whether *Types of Resets* should go in a separate file.
- [ ] Combine *The Analysis* (README) and *Explanation: Step-by-Step* (README2).
- [ ] Determine whether *The Analysis* (README) and *Explanation: Step-by-Step* (README2) should go in a separate file.
- [ ] Review *Understanding `git reset --hard HEAD~1`* in two of the drafts.

### Combining 
#### Introductions
##### README
This repository demonstrates how the .git database changes over time as you make commits, modify files, and then use git reset --hard to revert changes. It provides a step-by-step illustration to help users understand the effects of git reset --hard on the Working Tree, Staging Area and Local Repo given an "initial commit", file changes, a second commit, then a reset.

For simplicity, we're assuming a single branch, main.

##### README2
This repository demonstrates how the .git database changes over time as one follows a standard progression of initializing git, adding a file, committing that file, adding another file, committing that second file, then using git reset --hard to revert changes. It provides a step-by-step illustration to help users understand the effects of git reset --hard on the Working Tree, Staging Area and Object Database given an "initial commit", file changes, a second commit, then a reset.

For simplicity, we're assuming work being conducted on a single branch.

##### Final Draft
This article demonstrates how the .git database changes over time as one follows a progression of initializing git, adding, committing, adding new content, committing that content, then using `git reset --hard` to revert to the original commit. We'll provide a step-by-step guide to illustrate the impact of `git reset --hard` on the *Working Tree*, *Staging Area* and *Object Database*.

For simplicity, we're assuming work being conducted on a single branch.

#### The Steps
##### README
**The Steps**
- Working Tree Modifications
- Update the Staging Area (`git add README.md`)
- Commit #1 (`git commit -m "Initial commit"`)
- Working Tree Modifications
- Update the Staging Area (`git add example.txt`)
- Commit #2 (`git commit -m "Add example.txt"`)
- Reset back to Commit #1 (`git reset --hard HEAD~1`)

##### README2
**Our Steps**
```bash
git init
echo "README" > "README.md"
git add README.md
git commit -m "Initial commit"
echo "Lorem ipsum" > "example.txt"
git add example.txt
git commit -m "Add example.txt"
git reset --hard HEAD~1
```

##### Article (git-reset-hard.md)
(This was part of the intro, not a separate section.)
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

##### Final Draft
**Our Steps**
- Working Tree Modifications
- Update the Staging Area (`git add README.md`)
- Commit #1 (`git commit -m "Initial commit"`)
- Working Tree Modifications
- Update the Staging Area (`git add example.txt`)
- Commit #2 (`git commit -m "Add example.txt"`)
- Reset back to Commit #1 (`git reset --hard HEAD~1`)

#### Level Set (Final Draft)
Level Set
> See level-set.md.

#### Summary
##### README
`git reset --hard HEAD~1` is thought of as wiping history and a somewhat destructive action. While that's true for the working tree, it's not entirely true for the .git database. As we'll see by examing the contents of the .git directory over time, while HEAD and the .git/logs/refs/heads/main files point back to the previous commit, `.git/objects` still retains references to the objects introduced in Commit #2. The log history contains a trail of hashes from 000000 to the Commit #1 hash, the Commit #2 hash, then back to the Commit #1 hash.

##### README2
##### Article (git-reset-hard.md)
The intent of `git reset --hard HEAD~1` is to revert the user to the previous commit - this includes the *Working Tree*, *Staging Area* and *HEAD*. As we'll see, while changes to the *Working Tree* do absolutely revert, the *Object Database* still keeps track of history including history prior to the `reset`. What happens to the *Object Database* is interesting and insightful to not only understanding how `reset` works, but also in terms of the thought process behind Git itself.

##### Final Draft
The intent of `git reset --hard HEAD~1` is to revert the user to the previous commit - this includes the *Working Tree*, *Staging Area* and *HEAD*. As we'll see, while changes to the *Working Tree* do absolutely revert, the *Object Database* still keeps track of history including history prior to the `reset`. What happens to the *Object Database* is interesting and insightful to not only understanding how `reset` works, but also in terms of the thought process behind Git itself.

#### Types of Resets
> Note that the two versions were identical.
##### Final Draft
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

#### Combine *The Analysis* (README) and *Explanation: Step-by-Step* (README2)
##### README
##### README2
##### Article (git-reset-hard.md)
##### Final Draft

#### Review *Understanding `git reset --hard HEAD~1`* in two of the drafts
##### README
##### README2
##### Article (git-reset-hard.md)
##### Final Draft



