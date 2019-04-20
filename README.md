# About Git

Git is a distributed version control system. In git, every user has a copy of the whole repository, based on the last time it was synced with the remote repository. This document mainly concentrates on the command line tool git.

## Some advantages of using git
* Keep track of changes to code.
* Synchronize code between different people
* Test changes to code without losing the original
* Revert back to old versions of code

## Git installation instructions:
https://git-scm.com/downloads

### Configure git to use your email id
```
$ git config --global user.name "John Doe"
$ git config --global user.email "johndoe@example.org"
$ git config --list`
```

### Get help for git / git <command\>
```
$ git --help
$ git <command> --help
```
Try replacing <command\> with add

### Get Manual Pages for git / git <command\>
```
$ man git
$ man git <command>
```

There will be two scenarios when you will be using git, one is when you already have the project
on your local machine and second when it is in a remote repository.

## To start tracking a local project

### Initialize a existing project
```
$ git init
```

This will create a .git folder inside the project's root. If you ever want to stop using git for tracking, just delete this hidden folder

### To check status of tracking
```
$ git status
```

### To check changes
```
$ git diff 
```

### To not track some files (compiled binaries, configuration specific to your system)
Create a .gitignore file in the project's root and add the relative paths to the files you don't want tracked.
Wildcards can also be used, for example .out. These files won't show in git status

### Git works in three stages
* Working Directory: Contains modified and untracked files, this is where we do our work
* Staging Area: Middle location for files, here we add the work we have done to be added to the repository. This is used so that we can work on anything we want, but when we want to commit it, we commit it in chunks with detailed messages for each feature we added along associated relevant files.
* .git directory: Is the repository location. This contains our committed files.

### To add file(s) to the staging area 
```
$ git add <files>
$ git add --all
```
git status will reflect changes made

### To remove file(s) from the staging area
```
$ git reset <files>
$ git reset
``` 
git status will reflect changes made

### To commit files to the repository from staging area
```
$ git commit -m "detailed-commit-message"
```
git status and git log will reflect changes

## To start tracking a remote project

### First clone the repository to local machine 
```
$ git clone <url> <where to clone> 
```

### View the repository structure (branches etc)
```
$ git branch -a
```

### Push your changes after committing them
It is good practice to pull the changes that were made since last 
refresh from the remote repository

```
$ git pull <origin> <master>
$ git push <origin> <master>
```
origin is the name of the remote repo, and master is the branch you want to make changes to

## Branching
* Branch is a version of the repository and has it's own
commit history and current version
* It is usually used to add features and test them separately 
from the main code before adding them back to master.

### To create a branch named <feature>
```
$ git branch <feature>
```

### To switch to a branch
```
$ git checkout <branch name>
```
Handy Combination of the two: $ git checkout -b <feature>

git branch command will show the branches locally and the current one (with a \*)

After switching to a branch if we commit, it will have no effect on our master branch or the remote repository

### Pushing branch to remote repository
```
$ git push -u <master> <branch>
```

This will create a remote <branch> for your local <branch>, and subsequent push/pulls will work on this relationship when inside this branch

### To merge a local branch with master

* Switch to master
```
$ git checkout master
```
* Pull recent changes
```
$ git pull origin master
```
* Check the merged branches
```
$ git branch --merged
```
* Merge the branch to master
```
$ git merge <branch>
```
* Push changes to remote repo
```
$ git push origin master
```

### Delete a branch
* Check merged branches, then delete the local branch
```
$ git branch -d <branch>
```
* This branch may be in remote repo too, check using git branch -a then delete remote branch
```
$ git push origin --delete <branch>
```

## Workflow for working on your own repository

* Clone the repo
```
$ git clone <remote url>
```
* Create a branch and switch to it
```
$ git checkout -b <branch>
```
* Make changes
* Add changes to staging
```
$ git add --all
```
* Commit the changes to repository
 ```
$ git commit -m "detailed-commit-message"
```
* Push branch to origin and set default upstream
 ```
$ git push -u origin <branch>
```
* Change to master
 ```
$ git checkout master
```
* Pull in latest changes
 ```
$ git pull origin master
```
* Merge the branch
 ```
$ git merge <branch>
```
* Push changes
 ```
$ git push origin master
```
* Then delete the branch..

## Pull Requests
When working with a team, to get your work merged upstream, a pull request is sent.
It contains the changes made and a message to elaborate over that.
Then the repository owner may accept/reject the changes depending on 
the testing, quality of code etc.

### Issue a pull request
```
$ git request-pull -p <start commit> <url of remote repo> <end>
```
* start means the version from where you started working at (remote repo)
* end means where in their history you want your code to be merged

### Merge a pull request
* First get the branch on your local machine
 ```
$ git fetch origin pull/<pull request ID>/head:<branch>
```
* Switch to the new branch that's based on this pull request
 ```
$ git checkout <branch>
```
* Test/Make modifications
 ```
$ git push origin <branch>
```

## Problems:

### Merge Conflicts
Happens when there are different versions of the same file
and git is unable to determine which is the correct one.
For example two collaborators working on the same file.

If you commit a change and it conflicts with the version in the remote repo
and then pull it, it will result in a merge conflict

The conflicting files when opened will look like:

```
<<<<<< HEAD //your version 
<lines of conflicting code>
=================== // start of remote version
<lines of conflicting code>
>>>>>>> Commit Hash
```

Just decide what to do with the conflicting lines and
delete the <<< , >>> and === lines and push the changed code
after committing again

### Reset to a previous commit (dangerous see [revert](https://github.com/shas-g48/0-3XK/tree/master/contributing/git-guide#revert-a-commit)

One of the benefits of using git is that we can roll back
to a previous version of your code easily.
To do this:

* Find the commit hash to roll back to by using $ git log ,then to reset,
 ```
$ git reset --hard <commit hash>
```
* To reset local repo to version in remote
 ```
$ git reset --hard origin/master
```

git reset brings all files to their initial state,
and also the staging area is reverted.

Note that this does not change untracked files,
they can be removed with
```
$ git clean -df
```

d for dirs, f for files

Normal git reset (mixed) will bring the files to their 
initial state but keep the work you've done on the working directory
 ```
$ git reset <commit hash>
```

Git reset soft will bring files to their initial states
but keep the work you have committed in the staging directory
i.e. you don't lose the committed work you've done
 ```
$ git reset --soft <commit hash>
```

Reset a file in a branch to master version
 ```
$ git checkout <file>
```

### Fix a bad commit message, after committing;

```
$ git commit --amend -m "detailed-corrected-commit-message"
```

### Add a left out file after committing, to the same commit
```
$ git add <missed file(s)>
$ git commit --amend
```

### Lost all work due to git reset

* Use git reflog to get the relative history of commits you've made
 ```
$ git reflog
```
* Get the hash of the commit you want to revert to and then
 ```
$ git checkout <backup hash>
```

We have our changes back, but we are in a headless state, as this is not actually a branch. git has a garbage collector that automatically removes unused commits not in the working tree (approx 30 days), so revert quick.

* Create a branch out of this state
 ```
$ git branch <backup branch>
```

* Now do the merge

 ```
$ git checkout master
$ git merge <backup branch>
$ git branch -D <backup branch>
```

## Revert a commit
If working in a team, never use commands that change the 
git hash of previous commits that they may have pulled,
so to revert work (creates a new commit but "resets" to the commit you want)
```
$ git revert <commit hash>
```

# References
* [Learn git without any code](https://guides.github.com/activities/hello-world/)
* [Git Tutorial for Beginners: Command-Line Fundamentals - YouTube](https://www.youtube.com/watch?v=HVsySz-h9r4)
* [An Introduction to Git and GitHub by Brian Yu - YouTube](https://www.youtube.com/watch?v=MJUJ4wbFm_A)
* [git scm documentation](https://git-scm.com/)
* [GitHub Help](https://help.github.com/)
