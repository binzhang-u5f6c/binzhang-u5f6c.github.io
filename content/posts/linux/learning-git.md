---
title: "Learning Git"
date: 2020-04-16T20:34:15+08:00
draft: false
toc: true
math: false
tags:
  - linux
---

## 1. Git Basics

Initializing a repository.

```bash
git init
```

Clone a repository.

```bash
git clone <url> <path>
```

Track new files or stage modified file.

```bash
git add <file>
```

Remove and move files.

```bash
git rm <file>
git mv <file>
```

Commit all staged files.

```bash
git commit
git commit --amend # commit to your last commit
```

Check the status.

```bash
git status
```

Show commit history.

```bash
git log
git log --patch # show the difference
```

Work with remotes.

```bash
git remote # show remotes
git remote add <remote-name> <url> # add remote
git remote rm <remote-name> # remove remote
git remote rename <remote-name1> <remote-name2> # rename remote
git fetch <remote-name> # fetch the remote
git pull <remote-name> <branch> # pull = fetch and merge
git push <remote-name> <branch> # push
```

Work with tags.

```bash
git tag # show tags
git tag -a <tag> <commit checksum> # create tag
git tag -d <tag> # delete tag
git push <remote-name> <tag> # push tag to remote
git push <remote-name> --tags # push all tags to remote
```

## 2. Branch

Git store all data as **snapshots**.
The snapshot is organized in the form of tree structure.
Each file is stored as a **blob** object as the leaf of the snapshot tree.
If the file is not modified, git will only store a pointer to its previous
blob object.

When making a commit, a commit object is created, including author's name,
email, commit message, a pointer to the root node of the snapshot tree,
and a pointer to its parent commit.

Both tags and branches are pointers to a commit object.
Another important pointer is **HEAD**, which decide what files are shown in
the current directory.

Create a new branch.

```bash
git branch <branch>
```

Move the HEAD to point to a branch.

```bash
git checkout <branch>
git checkout -b <branch> # create a branch and switch to it
```

Merge a branch and delete it.

```bash
git merge <branch>
git branch -d <branch>
```

If the merged branch is fast-forward current branch, the command will only
move current branch pointer forward.
If not, the merging is based on the most recent common ancestor.
A new snapshot is created and current branch pointer will be moved
to the new snapshot.

If a merge conflict occur, git will stop merging and add conflict marker in
the files that have conflicts.
You have to modify manually and commit to finalize.

```bash
git commit
```

When you clone a repository from Internet,
there are two different pointers for each branch,
one normal branch, and one remote-tracking branch.
The remote-tracking branch show the branch information of the remote
repository.
When you commit, only the local branch pointer will move.
Fetch will synchronize the remote-tracking branch pointer to the remote
repository.

After cloning a repository, git automatically create a local master branch
tracking the remote-tracking master branch.
If you want to track other branch,

```bash
git checkout --track <remote-name>/<branch>
# if you want to have a different name
git checkout -b <another-name> <remote-name>/<branch>
```

To set a local branch to a remote-tracking branch,

```bash
git branch -u <remote-name>/<branch>
```

Delete a remote branch.

```bash
git push <remote-name> --delete <branch>
```

## 3. Rebase

Rebase command change the ancestor of a branch.
It finds the common ancestor of two branches, gets all changes since the common
ancestor, replays the changes to the base branch.
The former branch will be deleted.

```bash
git rebase <base-branch> <topic-branch>
```

Often, rebase is used when you want to contribute a project.
Rebase your local branch to `origin/master`, and submit the pull request.
It will be convenient for the maintainer to merge your changes.

**Note**:

* Do not rebase commits that exist outside your repository
* Do not rebase commits that others may have based work on

Compared with merge, rebase will delete some history of your project,
but make your project history clear.

## 4. Reset and Checkout

Git maintains three trees.

* **HEAD**: last commit snapshot, which the HEAD pointer pointing to
* **Index**: files staged and to be commit
* **Working Directory**

Reset command do three things:

* move what HEAD points to (stop here if `--soft`)
* update the Index with the contents of what HEAD points to
  (stop here if `--mixed` or no options)
* update the Working Directory (stop here if `--hard`)

Thus `git reset HEAD <file>` is used to unstage a staged file.

The main differences between reset and checkout are:

* Checkout is working-directory safe.
* Checkout only move the HEAD pointer, while reset move what HEAD points to.

Both reset and checkout can be followed by `<paths>`.
Only the paths, rather than the whole directory, will be act upon.

## 5. Debug

Find which commit was the first one to introduce a bug or problem.

```bash
git bisect start
git bisect bad
git bisect good <good_commit>
```

Annotate the lines with which commit was the last one to introduce a change
and who authored that commit.

```bash
git blame -L <line1>,<line2> <file>
```

Find any string in files, no matter current or older versions.

```bash
git grep <str>
```
