# Git

## Basic

```bash
# install on centos
sudo yum install git
```

## Branch

```bash
# show all branches
git branch

# force replace remote branch
git push -f <remote> <branch_name>

# delete remote branch
git push origin --delete <branch_name>

# delete local branch
git branch -d branch_name
# delete local branch force
git branch -D branch_name

# rename local branch
git branch -m <new_name>

# replace master with branch
git checkout <branch>
git merge -s ours master
git checkout master
git merge <branch>

# merge specific file
git checkout <feature_branch> my_file1 my_file2

# opposite of strategy ours
git merge -X theirs master

# merge ignore folders
git merge --no-ff --no-commit <source_branch>
git reset -- <folder>

# mrege by folders
git merge --no-ff --no-commit <source_branch>

# clean history
git checkout --orphan newBranch
git add -A  # Add all files and commit them
git commit
git branch -D master  # Deletes the master branch
git branch -m master  # Rename the current branch to master
git push -f origin master  # Force push master branch to github
git gc --aggressive --prune=all

# update last commit message
git commit --amend -m "<new_msg>"
git push origin <branch> --force
```

## diff

```bash
# compare files
git diff branch1:path/to/file branch2:path/to/file
git diff branch1name branch2name path/to/file
```

## Tag

```bash
# add tag
git tag <tag_name>

# use tag to archive
git tag archive/<branch_name> <branch_name>

# delete tag
git tag -d oldtag

# rename tag
git tag <new_tag> <old_tag>
git tag -d <old_tag>

# update an exsisting Tag
git tag -f <tag_name>
```

## Remote

```bash
# show remote url
git remote -v

# set remote url
git remote set-url <repo>
git remote set-url --add origin <repo>
git remote set-url --delete origin <repo>

# add to github
git remote add github <repo>
git push github master
git branch -u github/master master
git remote set-url origin <repo>

## add original github repo  as remote upstream
git remote add upstream <github repo>
git fetch upstream <branch>

```

## Submodule

submodule info in .gitmodule and .git/config

```bash
# add other project as submodule
git submodule add <repo> <submodule_path_or_local_dir_name>

# init submodule
git submodule init

# update submodule
git submodule update
git submodule update --init --force

# delete submodule
git submodule deinit <submodule_path_or_local_dir_name>
git rm <submodule_path_or_local_dir_name>
rm -rf .git/modules/<submodule_path_or_local_dir_name>

# refresh
git submodule deinit <submodule>
git submodule init <submodule>
git submodule update <submodule>

# reinit submodule
git submodule deinit --all -f
rm -rf .git/modules
rm -rf <path_to_module>
git submodule update --init --recursive

```

## Stash

```bash
# show stashed
git stash list

# save current
git stash push

# apply latest
git stash pop

# clear all
git stash clear
```

## Error

```bash
# error: The following untracked working tree files would be overwritten by checkout
git clean  -d  -fx ""

# error: you need to resolve your current index first
git reset —merge

# fatal: pathspec '...' did not match any files
# list all index
git ls-files

# re init repo
cat .git/config  # note <github-uri>
rm -rf .git
git init
git add .
git commit -m "Initial commit"
git remote add origin <github-uri>
git push -u --force origin master
```

## count lines

```bash

# by author
git log --format='%aN'  | sort -u |
while read name; do echo -en "$name\t"; git log --author="$name" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }' -; done

# filter time
git log --format='%aN' | sort -u |
while read name; do echo -en "$name\t"; git log --author="$name" --since==2020-01.01 --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }' -; done

# filter include src but excluede assets
git log --format='%aN'  | sort -u |
while read name; do echo -en "$name\t"; git log --author="$name" --since==2020-01.01 --pretty=tformat: --numstat | grep src | grep -v assets | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }' -; done

# myself
git log --author="?" --since==2020-01.01 --pretty=tformat: --numstat | grep src | grep -v assets/js | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }'
```

##

```bash
# proxy
git config --global url."https://hub.fgit.cf/".insteadOf "https://github.com/"

# config user name and email for current repo
git config user.name "Your New Username"
git config user.email "your_new_email@example.com"
```

```.gitconfig
[push]
  default = simple
[user]
 name = ?
 email = ?@gmail.com
[core]
    editor = vim
    compression = 0
 pager = cat
[alias]
    l = log --oneline --decorate --graph --all
    l5 = log --oneline --decorate --graph -5
    l20 = log --oneline --decorate --graph -20

    s = status
    co = checkout
    a = add -A
    cm = commit -a -m
    cb = checkout -b
    d = branch -d
    pd = push origin --delete

    cf= config --global -e

    pl= pull origin HEAD
    ps= push origin HEAD
    pss= push --set-upstream origin HEAD
    pst= push origin HEAD --tags
    bomb= !git checkout --orphan newBranch && git add -A && git commit -m "init" && git branch -D main && git branch -m main && git push -f origin main && git gc --aggressive --prune=all &&:
[color]
    ui = always
```

## lfs

```bash
# clone without downloading large fies
git lfs install --skip-smudge
git clone <repo>

# pull single file
git lfs pull --include=<filename>
```

```bash
# install
brew install git-lfs
yum install git-lfs

git lfs install

# add .gitattributes
git lfs track "*.psd"

# show tracked files
git lfs ls-files

# migrate file to lfs pointer
git lfs migrate info
git lfs migrate import --include="*.psd"

# download file
git lfs pull

# status
git lfs status

# push
git lfs push origin master

# replace large file with lfs pointer
git read-tree HEAD && GIT_LFS_SKIP_SMUDGE=1 git checkout -f HEAD
```
