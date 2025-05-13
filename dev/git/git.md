# GIT

# BASICS

* **staging area** (formerly *index*)
  review changes work before commiting
  proposals for the next commit
* **commit**
  is a complete snapshot of the project saving it to the repository
  after a commit the staging area still contains the latest snapshot

# BRANCH

- https://git-scm.com/docs/git-branch

- A branch is a new/separate version of the main repository
- Branches allow you to work on different parts of a project without impacting the main branch.
- When the work is complete, a branch can be merged with the main project.
- Switch between branches and work on different projects without them interfering with each other.

A branch (aka a **feature branch**) is designed to **encapsulate a group of changes**. These changes might be thrown away, entirely rewritten or in the majority of cases they’ll be promoted into the main history of the codebase - via a merge.

**GitHub** uses branches to encapsulate changes in a pull request.

- Main
- Develop
- Feature
- Release
- Hotfix

The two primary branches in Git flow are *main* and *develop*. There are three types of supporting branches with different intended purposes: *feature*, *release*, and *hotfix*.

## release branching

```sh
git checkout -b release-1.2.3 
```

## branch

list, create, or delete branches

```sh
git branch <new-branch>
git branch -m
git branch --move	# Move/rename a branch and the corresponding reflog  
git branch -M	# Shortcut for --move --force
git branch -M main # Renames the current branch to main

git branch -l
git branch --list	# List branches  

git branch -d
git branch --delete	# Delete a branch. 
# The branch must be fully merged in its upstream branch, or in HEAD if no upstream was set with --track or --set-upstream-to.

git branch -r
git branch --remotes # List or delete (if used with -d) the remote-tracking branches
```
## checkout

* moves the `HEAD` pointer to a specified commit
* can be used in a commit, or file level scope
* switch branches or restore working tree files

```sh
git checkout <commit> <file> # A file level checkout will change the file's contents to those of the specific commit.
git checkout HEAD <file> # restore file from the last known commit, which is HEAD

git checkout <branch> # used to check out a branch moving us from the current branch, to the one specified
# To prepare for working on <branch>, switch to it by updating the index and the files in the working tree, and by pointing HEAD at the branch. Local modifications to the files in the working tree are kept, so that they can be committed to the <branch>
git checkout -b <new_branch> # causes a new branch to be created as if git-branch(1) were called and then checked out
```

```

```sh
git checkout -- . # remove all unstaged changes (before Git 2.23)
```

## set a new upstream branch

```sh
git push --set-upstream origin <new_branch>
```

# CONFIG

- https://git-scm.com/docs/git-config

```sh
git config --<scope> <setting> <"value">
git config --global --edit # opens the global config file with the default editor
git config --global --list
```

## scopes

```sh
git config --system  # all users
git config --global  # all repos of current user
git config --local   # current repo

cat ./.git/config # location of local config
```

## settings

### user

```sh
git config --global user.name "Udo Nonner"
git config --global user.email "udo.nonner@posteo.de"
```

### core

```sh
# carriage return / line feed
git config --global core.autocrlf true    # on windows  
git config --global core.autocrlf input   # on mac|linux  
# editor
git config --global core.editor "code --wait"
# .gitignore
git config --global core.excludesfile ~/.gitignore_global # use *~/.gitignore_global* as a global .gitignore
# ssh
git config --add --local core.sshCommand 'ssh -i ~/.ssh/id_rsa'
```

### init

```sh
git config --global init.defaultBranch main # set 'main' as new default branch name
```

### credential

```sh
git config --global credential.helper cache # enable caching of the credentials
git config --global --unset credential.helper # clear cache
```


### other

```sh
git config --global diff.tool vscode
git config --global difftool.vscode.cmd "code --wait --diff $LOCAL $REMOTE" # review using `git config --global -e`
```

# ROLES

## individual developer (standalone)

- https://git-scm.com/docs/giteveryday#STANDALONE

# WORKFLOW

```sh
git branch update-readme # Create a branch
git checkout update-readme # Checkout to that branch
# Change a file or files
# Save the file or files
git add README.md # Add the files or segments of code that should be included in the next commit
git commit -m "update the README to include links to contributing guide" # Commit the changes
git push -u origin update-readme # Push the changes to the remote branch
```

# REPOSITORIES

## monorepo vs multi-repo

- https://svij.org/blog/2021/05/16/monorepos-mit-git-eine-gute-idee/

- **monorepo**
  Uses a single repository to host all the code for the multiple libraries or services composing a company’s projects. At its most extreme, the whole codebase from a company — spanning various projects and coded in different languages — is hosted in a single repository.
- **multi-repo**:
  Uses several repositories to host the multiple libraries or services of a project developed by a company. At its most extreme, it’ll host every minimum set of reusable code or standalone functionality (such as a microservice) under its repository.
## setup

```sh
git init # creates a *.git/* subdirectory in the current directory which contains the repository
echo "node_modules" >> .gitignore
git add file1 file2 # takes a snaphot of those files and adds them to the staging area
git commit -m "First commit" # stores the snapshot in the repository
```

# SAVE CHANGES

## add

* Allows you to choose *what you are going to commit*. Commits should be logical, atomic units of change.
* Being able to *shape your history* is one of the greatest advantages of Git.

```sh
git add <file> # adds files to the staging area
git add file1 file2
git add -p # interactively stage hunks of changes
```

## add all files

disadvantages of `git add --all`

* **Poorly thought out history**: By staging all available changes, the clarity of your history will  likely suffer. Being able to shape your history is one of the greatest advantages of using Git. If your commits are too large, contain unrelated changes, or are unclearly described in the commit message, you will lose the benefits of viewing and changing history.

```sh
git add -A
git add --all # stages all files, including new, modified, and deleted files, including files in the current directory and in higher directories that still belong to the same git repository

git add . # same as --all but doesn't include higher directories

git add -u
git add --update # same as --all but doesn't include new files
```

## commit

* A commit is like a snapshot of your repository
* Commits shape history: Make new commits often, based around logical units of change. Over time, commits should tell a story of the history of your repository and how it came to be the way that it currently is.
* Commits are created on the *branch* that you're currently checked out to (wherever HEAD is pointing) so it's always a good idea to run `git status` before making a commit, to check that you're checked-out to the branch that you intend to be.

```sh
git commit # without `-m <message>` opens the default editor

git commit -m
git commit --message "message"
git commit -a
git commit --all # Commit all modified (not new!) files directly, skipping the staging area. If sure that your code doesn't need to be reviewed, you can commit in one step.
```

### commit --amend

```sh
git commit --amend # updates the most recent commit on your current branch
```

This can be an extremely useful command for commits that:

- Haven't been pushed to the remote yet
- Have a spelling error in the commit message
- Don't contain the changes that you'd like to contain

### commit messages

* Use the imperative mood (as if you are giving orders to the codebase)

  *"make xyzzy do frotz"* instead of *"makes xyzzy do frotz"* or *"changed xyzzy to do frotz"*

* Start with a verb in the present tense

* Leave a blank line between the title and body

* Body: 

  * Explain WHAT the change is, but especially WHY the change was needed
  * Separate paragraphs in the body with blank lines
  * Use a hyphen (-) for bullet points if needed
  * Use hanging indents if needed

```ini
Enable Logging Globally

Add Account Delete Route

Needed for account deletion workflow on frontend
```

```ini
Bump version to 1.0
Add .gitignore
Refactor product repository for functional isolation and clarity
Merge branch 'master'
Remove unneeded tests
Fix bug preventing menu from sliding out on mobile
```

## restore

* Was introduced in 2019 as part of Git version 2.23
* Restores files in the working tree from either the staging area or another commit
* Prior to the *git restore* command, people commonly used `git reset` to unstage files
* By default, if `--staged` is given, the contents are restored from HEAD, otherwise from the index. Use `--source` to restore from a different commit.
* Doesn't update your branch, doesn't modify your commit history
* Restoring changes from the Staging Area doesn't infer any loss of data, while restoring changes in the Working Tree might discard data completely

```sh
git restore --staged <file> # unstage file
```

```sh
git restore <file> # discard uncommitted local changes in <file>
git restore . # discard uncommitted local changes in the current directory
git restore --staged <file> # Restore a staged file
git restore --source=HEAD --staged --worktree <file> # Restore both the index and the working tree (same as git checkout)

git restore file1 file2 # restores in working directory or in the staging area
git restore --staged <file> #  
```

## rm, mv

```sh
### remove and rename
git rm file1 # remove files for both the working directory and the staging area with one command  
git mv file1 file2 # rename file for both the working directory and the staging area with one command

git rm --cached <file> # to unstage
```

## log
**show commit logs**

- https://git-scm.com/docs/git-log

```sh
git log
git log --oneline
git log --reverse
```

## status
**info about working directory and staging area**  

```sh
git status # info about working directory and staging area  
git status -s # short version  
```

* left column = staging area
* right column = working directory
* *M* (red) # modified in working directory but no yet added to staging area
* *A* (green) # added

## ls-files
**show files in staging area**

```sh
git ls-files # 
git ls-tree #
```

## show

```sh
git show <commit-id> # shows meta data and diff of a commit; 
# <commit-id> as it is shown with git log --oneline with 7 or less characters  
git show <file-id> # shows final version of file; file-id as it is shown with git `ls-tree <commit-id>`
git show HEAD # normally this is the last commit    
git show HEAD~n # n indicates how many commits are counted backwards  
git show <commit-id>:<path> # show the final version of file within the given commit 
git remote show <remote>
```

## diff

```sh
git diff 
# list all unstaged tracked changed files
# compare changes in working directory with what we've in the stagin area  

git diff --name-only HEAD^

git diff --staged 
# list all staged tracked changed files:
# compare changes in the staging area with what we've in the last commit

git difftool # launches the configured default gui diff tool

# --name-only 
```

* KDiff3
* P4Merge
* VSCode

# GO BACK IN TIME

## revert vs reset

* `git reset`
  if working locally and no commits have been pushed
* `git revert`
  if the commits have been pushed. That way, anyone who has pulled the bad commit can also pull the revert commits as well

## reset

* `checkout` and `reset` are generally used for making local or private  'undos'
* Will delete commits from the history, so if admins are working locally and haven't pushed any commits, this will likely be the better option.
* They modify the history of a repository which can cause conflicts when pushing to remote shared repositories

Sometimes, a commit includes sensitive information and needs to actually be deleted. `git reset` is a very powerful command that may cause you to lose work. By resetting, you move the `HEAD` pointer and the branch pointer to another point in time - maybe making it seem like the commits in between never happened! 

Before using `git reset`:

- Make sure to talk with your team about any shared commits
- Research the three types of reset to see which is right for you (--soft, --mixed, --hard)
- Commit any work that you don't want to be lost intentionally - work  that is committed can be gotten back, but uncommitted work cannot

```sh
git reset # will remove all changes from the staging area. It will not delete any files
```

```bash
git reset --soft HEAD~1 # rewind your current HEAD branch to the specified revision. Here: return to the one before the current revision - effectively making our last commit undone.

# --soft flag: this makes sure that the _changes_ in undone revisions are preserved. After running the command, you'll find the changes as uncommitted local modifications in your working copy.
```

### undo commits permanently

```bash
git commit ...
git reset --hard HEAD~3
# The last three commits (HEAD, HEAD^, and HEAD~2) were bad and you do not want to ever see them again. Do **not** do this if you have already given these commits to somebody else.
```
## revert

* The safest way to change history with Git
* Undoes the effects of a bad or incorrect commit by creating a commit that does the opposite of the commit in question
* By reverting the commit in this manner, the history stays intact.
* It functions as an "undo commit" command, without sacrificing the integrity of your repository's history
* Only commit level scope, no file level functionality
* The command requires the commit ID that needs to be undone rather than the commit ID that the team wants to reset to

```sh
git log --oneline # 1a6b2cc
git revert 1a6b2cc
```

## checkout

```sh
git log --oneline
git checkout <commit-id> .
```

Don’t forget the final "**.**" - You aren’t  required to add this, and it may look like it has worked but if you  leave this off it will take you to a new “detached head state” where you can make changes and it will allow you to make commits, but nothing  will be saved and any commits you make will be lost.

## reflog

If you're changing history and undoing commits, you should know about `git reflog`. If you get into trouble, the reflog could get you out of trouble. The reflog is a log of every commit that `HEAD` has pointed to. So, for example, if you use `git reset` and unintentionally lose commits, you can find and access them with `git reflog`.

## restore a file

```sh
git checkout HEAD <file> # restore file from the last known commit, which is HEAD
```

If you accidentally committed a bad version of a file and need to yank a version from even further back in time, look in your Git log to see your previous commits, and then check it out from the appropriate commit:

```sh
git log --oneline
# 79a4e5f bad take
# f449007 The second commit
# 55df4c2 My great project, first commit.

git checkout 55df4c2 filename
```

Essentially, you have rewound the tape and are taping over a bad take. So you need to re-record the good take.

Now the older version of the file is restored into your current position. (You can see your current status at any time with the `git status` command.) You need to add the file because it has changed, and then commit it:

```bash
git add filename
git commit -m 'restoring filename from first commit.'
```

## accidentally committed to the wrong branch

* Checkout to the branch that you intended to commit to: `git checkout <branchname>`
* Merge the commits from the branch that you did accidentally commit to: `git merge <master>`
* Push your changes to the remote: `git push`
* Fix the other branch by checking out to that branch, finding what commit it should be pointed to, and using `git reset --hard` to correct the branch pointer

## change wdir

**Use git commands with another path rather than the actual working dir**:

```
Run as if git was started in <path> instead of the current working directory. 
```

It is very important that the `-C` option comes before actual command you want to execute:

```sh
Wrong:   git add . -C some-path
Correct: git -C some-path add .
```

# REMOTES

## authentication

### personal access token

```sh
# add token to git global config:
git config --global credential.helper '!f() { sleep 1; echo "username=git token=<TOKEN>"; }; f'
```

```sh
# supply token with url:
git push https://<GITHUB_ACCESS_TOKEN>@github.com/<GITHUB_USERNAME>/<REPOSITORY_NAME>.git
```

### ssh

1. Add a deploy key on the github repo:
   https://github.com/unonweb/un.lvm.snapshots/settings/keys
2. Copy content of public key into the deploy key:

```sh
cat ~/.ssh/id_rsa.pub
cat ~/.ssh/frida@zapata.pub
```

3. Then:

```sh
SSH_CMD="ssh -i ~/.ssh/frida@zapata.pub"
git config --add --local core.sshCommand $SSH_CMD # add to current repo
# add remote
git remote add origin git@github.com:username/your-repository.git
git remote set-url origin git@github.com:unonweb/haerer-geruestbau-c-elements.git
```

## remote

```sh
git remote # lists the shortnames of each remote handle you’ve specified. 
# If you’ve cloned your repository, you should at least see *origin* 
# - that is the default name Git gives to the server you cloned from

git remote show <remote>
git remote show origin # inspect a remote repo
git remote -v # shows the URLs that Git has stored for the shortname to be used when reading and writing to that remote
```

```sh
git remote set-url # Changes URLs for the remote
```

### add

```sh
git remote add <shortname> <url> # add a remote repo
git remote add origin https://github.com/unonweb/litcomponents.git
```

* `git fetch <name>` can then be used to create and update remote-tracking branches

### rename, remove

```sh
git remote rename <old> <new>
git remote remove <shortname>
```

## push

### set-upstream

```sh
git push -u origin main
git push --set-upstream origin <branch> # add upstream (tracking) reference for every branch that is up to date or successfully pushed
```

* Sets the remote `origin` as the *upstream* reference
* This allows you to later perform `git push` and `git pull` commands without having to specify an `origin` since we always want GitHub in this case.

```sh
git push https://<GITHUB_ACCESS_TOKEN>@github.com/<GITHUB_USERNAME>/<REPOSITORY_NAME>.git
```

## clone

Cloning a repository from a remote server downloads the project to your local computer and leaves you with a local Git repository. This local Git repository will already have a connection to the original remote set up, automatically. This is what the "origin" remote connection points to.

```sh
git clone <url> # Clone (download) a repository that already exists on GitHub, including all of the files, branches, and commits.
```

* the common convention is to call a repository's main remote connection *origin*

## fetch

* is safe to run at any time since it never changes any of your local branches under 

```sh
git fetch <remote> # fetches any new work that has been pushed to that server since you cloned (or last fetched from) it. It only downloads the data to your local repository - it doesn’t automatically *merge* it with any of your work or modify what you’re currently working on. You have to merge it manually into your work when you’re ready.
# fetch provides a safe way to review the information before committing to your local branch.
git fetch --all # Fetch all remotes
```

### workflow: fetch and diff

```sh
git branch --remotes # List all the fetched remote branches

# 1. Fetch the remote repository with:
git fetch origin test # fetches branch 'test'

# 2. Compare the local branch to the remote by listing the commit differences:
git log --oneline test_local..origin/test # local branch = test_branch, remote = origin/test

# 3. Checkout the local branch where you want the changes merged:
git checkout test_local 

git checkout -b test_branch origin/test # checkout the fetched content to a new branch
```

## pull

* a combination of `git fetch` + `git merge`
* updates some parts of your local repository with changes from the remote repository
* Updates your current local working branch, and all of the remote tracking branches. It's a good idea to run `git pull` regularly on the branches you are working on locally.
* Without `git pull your local branch wouldn't have any of the updates that are present on the remote.

```sh
git pull origin # Kombination aus 
git fetch origin; git merge origin/main # fetches data from the server you originally cloned from and automatically tries to merge it into the code you’re currently working on.
git pull --verbose
```

## merge

Join two or more development histories together

```sh
git merge <remote>/<branch> --allow-unrelated-histories # By default, git merge command refuses to merge histories that do not share a common ancestor. This option can be used to override this safety when merging histories of two projects that started their lives independently.
git merge --abort # abort the merge
```

### review before merge

```sh
git log ..otherbranch # list of changes that will be merged into current branch.
git diff ...otherbranch # diff from common ancestor (merge base) to the head of what will be merged. Note the three dots, which have a special meaning compared to two dots
gitk ...otherbranch # graphical representation of the branches since they were merged last time.
```

Empty string implies HEAD, so that's why just ..otherbranch instead of HEAD..otherbranch.

The two vs. three dots have slightly different meaning for diff than for the commands that list revisions (log, gitk etc.). For log and others two dots (a..b) means everything that is in b but not a and three dots (a...b) means everything that is in only one of a or b. But diff works with two revisions and there the simpler case represented by two dots (a..b) is simple difference from a to b and three dots (a...b) mean difference between common ancestor and b (git diff $(git merge-base a b)..b).

### the save way: merge off a temporary branch

If you want to ensure you don't mess up your current branch, or you're just not ready to merge regardless of the existence of conflicts, simply create a new sub-branch off of it and merge that:

```sh
git checkout mybranch
git checkout -b mynew-temporary-branch
git merge some-other-branch
```

## request-pull

Generates a summary of pending changes

## push

```sh
git status # check what branch you are on
git push <remote> <branch>
git push -u origin master # When pushing a branch for the first time, this type of push will configure the relationship between the remote and your local repository so that you can use `git pull` and `git push` with no additional options in the future. (It creates an upstream tracking branch with a lasting relationship to your local branch).
```

By default, git push only updates the corresponding branch on the remote. So, if you are checked out to the master branch when you execute git push, then only the master branch will be updated. It's always a good idea to use `git status` to see what branch you are on before pushing to the remote.

# .GITIGNORE

## ignore everything except

```sh
# Ignore all files
*
# Do not ignore directories
!*/
# Do not ignore .md files
!*.md
```
## .gitignore local

```sh
.gitignore # a file in the root of your directory containing all files/folders that should be ignored by Git  

touch .gitignore
echo logs/ > .gitignore # tells Git to ignore the logs directory
echo node_modules > .gitignore
```

Ignoring files only works if you haven't already commited those files to the repository.    
If you have accidentally commited a file that actually should be ignored, you'll have to remove it from the stagin area:  

```sh
git rm --cached <file>
git rm --cached -r <dir>
```

## .gitignore global

```sh
# 1.
git config --global core.excludesfile ~/.gitignore_global # use *~/.gitignore_global* as a global .gitignore
# 2.
vi ~/.gitignore_global # create and edit the global .gitignore
```

# NOTES

## What's stored in a commit?

* ID
* Message
* Date/time
* Author
* Complete snapshot (full content - no delta)
  * compressed
  * no duplicate content

## commiting style

* the whole point of commiting is to create checkpoints as you go
* as you reach a state you want to record then make a commit
* each commit should represent a single unit of work
* don't mix things up: make one commit for a bug fix and one commit for the typo
* convention: message in present tense

## git objects

* commits
* blobs (files)
* trees (directories)
* tags

# GH

## auth

```sh
gh auth login # authenticate with your GitHub account. 
GITHUB_TOKEN=sfsdfsdf gh
```

## config

```sh
gh config set editor <editor>
```

## repo

```sh
gh repo create
```

