---
layout: post
title: Git Commands
---

## {{ page.title }}

- - - -

##Table of Contents

*  [Summary](#summary)
    -  [How Git works](#howgitworks)
    -  [Stages of Git](#stages)
    -  [Setup](#setup)
    -  [Basic Workflow](#workflow)
*  [Initialize a Repository](#init)
    -  [Create a new repository](#initrepo)
    -  [Clone an existing repository](#clone)
*  [Basic Git Commands](#basiccommands)
    -  [Add and Commit](#addcommit)
    -  [Branches](#branches)
    -  [Merging](#merging)
    -  [Stash](#stash)
*  [Undo with Reset, Checkout, and Revert](#undo)
    -  [Undo Scope](#undoscope)
    -  [Undo Commands](#undocommands)
    -  [Undo Parameters](#undoparameters)
*  [Cleanup with BFG](#cleanup)
    -  [Remove passwords, secret info](#removesecrets)
    -  [Remove file](#removefile)
*  [Pull Request](#pull)
*  [Rebase](#rebase)
*  [Different Workflows](#diffworkflows)
    -   [Feature Branch](#featurebranch)
    -   [GitFlow](#gitflow)
   
##<a id="summary">Summary</a>

__Git__ is a local software version control system.  The service __GitHub__ is one of many companies that does remote hosting of your git repositories.

####<a id="howgitworks">How Git works</a>

The concept of git is that data is seen as a set of snapshots of this mini filesystem.  Every time you commit and save your project, it basically takes a picture of what all your files look like at that moment and stores a reference to the snapshot.

If files have not changed, git does not store the file again (just a link to the previous identical file it has already stored).  Git is different than some other systems (like __mercurial__) in that some other systems store the deltas between saves (which saves space).

####<a id="stages">Stages of Git</a>

To see your current status and branch, do: `git status`.  This tells you the state (tracked or untracked) of each file.  The three main stages are:

1. __Modified__ means you changed the file, but have not tracked the changes yet.
2. __Staged__ means you marked a modified file in its current version to go to your next commit snapshot.
3. __Committed__ means this change has been safely snapshotted.

####<a id="setup">Setup</a>

Download git and optionally signup for GitHub.

*  To see your setup config, do: `git config --list`
*  You can edit the following:
    -  `$git config --global user.name "William Liu`
    -  `$git config --global user.email "william.q.liu@gmail.com"`
*  Setup SSH-KeyGen with `ssh-keygen -t rsa -c "william.q.liu@gmail.com"`
    -  On Windows, ssh is by default setup like `C:\Users\wliu.ssh`
    -  On Mac, ssh is by default setup like `Mac HD > Users > williamliu`
    -  Assuming you name the file `id_rsa`, you get two files:
        +  `id_rsa` is the private half of the key (keep this secret)
        +  `id_rsa.pub` is the public half of the key (free to give away)

####<a id="workflow">Basic Workflow</a>

So how does Git work in the real world?

1. The idea is that we will program on say a 'features' branch (or anything that is not the 'master' branch).
2. When the code is ready, you issue a pull request to merge the 'features' branch with the master.
3. Pull requests are the time for code reviews and to potentially make any changes before merging to master.
4. Once approved, merge the feature branch back into master.
5. Optionally Tag the releases.


##<a id="init">Initialize a Repository</a>

To get started, you can either create an empty project or copy an existing repository from another server (like GitHub).

####<a id="initrepo">Create a new repository</a>

If you want to create a new project, you can do the following:

1.  `git init` initializes a new project directory
2.  This step creates a `.git` subdirectory that has all your necessary repository files (e.g. your `.gitignore` file, a file that says what files to ignore)
3.  You may want to create your `.gitignore` file (what to ignore) and add a License

####<a id="clone">Clone an existing repository</a>

You can clone an existing repository from another server (like GitHub) using the command:  `git clone https://github.com/WilliamQLiu/myrepo.git`.

*  You can change the option from `https` to `git` if you want to use SSH transfer.
*  If you want to call this something else, just add an additional argument (e.g. `mynewrepo` after `myrepo.git`)

##<a id="basiccommands">Basic Commands</a>

####<a id="addcommit">Add and Commit</a>

    git add .   # Adds all files or specify the specific files
    git commit -m "This is a git message for the commit"  # Commit with msg
    git commit -a -m "Made a change"  # Automatic Adds and Commits

####<a id="branches">Create Branch, Checkout Branch</a>

    git branch features  # Create a branch called features
    git checkout features  # Check out a branch named features
    git checkout master  # Check out the master branch

####<a id="branches">Merging changes back to master branch</a>

    git pull # Fetch and merge changes on the remote server to your working dir
    git merge features # Merge a different branch into your active branch
    git diff  # View all the merge conflicts
    git reset --hard  # Undo a bad merge
    git push # push changes back to a remote repository (e.g. on GitHub)

####<a id="stash">Stashing</a>

    git stash  # hide your current changes on branch
    git stash pop  # get your hidden changes on branch

##<a id="undo">Undo with Reset, Checkout, and Revert</a>

To undo changes to your repository, you first need to think about the scope of what you want to change and then what command to use.

####<a id="undoscope">Undo Scope</a>

In a Git repository, we have the following components.

  * the working directory
  * the staged snapshot
  * the commit history

####<a id="undocommands">Undo Commands</a>

With these git commands, you can pass in the above component (e.g. the working directory) as a parameter (e.g. `--soft`, `--mixed`, `--hard`) and that determines the scope of the undo.

  * `git reset` - moves the tip of a branch to a different commit; this is used to remove commits from the current branch (e.g. go back two commits would be `git checkout hotfix` and then `git reset HEAD~2`).  We end up throwing away these last two commits.
  * `git checkout` - moves `HEAD` to a different branch and updates the working directory to match.  If there are any differences, you have to commit or stash any changes in the working directory first.
  * `git revert` - undoes a commit by creating a new commit.  This is safe since it does not re-write a commit history.

####<a id="undoparamters">Undo Parameters</a>

Again when you do an undo, you can pass in an optional parameter with your command to specify the scope of the change.  To be safe, only use `HEAD` as the parameter.

For example, with a `git reset`, we have:

  *  `--soft` means we reset only the commit history (code in the working directory and staged snapshot is untouched)
  *  `--mixed` means we reset the staged snapshot and the commit history (code in the working directory is untouched)
  *  `--hard` means we reset everything

`git revert` is the only one that does not have a file-level counterpart.

##<a id="cleanup">Cleanup with BFG</a>

BFG Repo-Cleaner is a tool to remove large (e.g. blobs bigger than 1M) or bad data (e.g. passwords, credentials, private data) and this is faster and easier to use than the `git-filter-branch` command.

####<a id="removesecrets">Remove passwords and secret information</a>

1. Clone your repo using the `--mirror` flag: e.g. `git clone --mirror https://github.com/WilliamQLiu/reponame.git`
2. Download `BFG Repo-Cleaner` [here](https://rtyley.github.io/bfg-repo-cleaner/ "BFG Repo-Cleaner").  You will get a file that looks like this `bfg-1.12.3.jar`.  If you are on a mac, just setup with homebrew using `brew install bfg` and then you can use the `bfg` command.
3. Create a `passwords.txt` file and add in all the data you want to remove (e.g. mypassword, 'mypassword')
4. Run the command `java -jar bfg-1.12.3.jar --replace-text passwords.txt`
5. Then run `git reflog expire --expire=now --all && git gc --prune=now --aggressive`
6. Finally, `git push`

Funny story: When I first ran this, I accidentally put the passwords.txt file in Git.  I had to rerun BFG to remove the passwords file.  Oops.

####<a id="removefile">Remove a file</a>

1. Go to your repository
2. Delete your file, commit and save to repo (make sure to list what files were deleted)
3. Then use BFG to erase your history of that file: `bfg --delete-files myfile.txt` (files from earlier step)
4. `git reflog expire --expire=now --all && git gc --prune=now --aggressive` when you're complete with all the files from Option 3

##<a id="pull">Pull Request</a>

Once you start working with other people, you may want to make suggestions to their code or they might have suggestions for your code.  This is called a pull request and involves these steps:

1. Fork a repository (the one that you want to make a pull request/repo where you want to have the code changed).  Click on the fork button on the GitHub repo page.
2. Clone the repo to your local machine.  Click the clone in desktop button beside the repo name.
3. Make the local changes/commits to the files (trying to touch as few files as possible)
4. Sync the changes
5. Go to your github forked repository and click 'Compare, review, create a pull request' (a green icon button that has no text).
6. A new page opens that shows the changes, then click the pull request link and it will send the request to the original owner of the repo.

##<a id="rebase">Rebase</a>

__Rebasing__ (`git rebase`) is an alternative to __merging__ (`git merge`), but does this in a destructive manner (opposed to merging's non-destructive operation).

What this means is that if you work on a feature branch, __merge__ ties together the histories of both branches.  The advantage is that this is __non-destructive__, but the issue is that we can have a polluted feature branch history if there were a lot of commits in the master branch (which makes it hard for other developers to understand the history of the project).  If this happens, look into `git log` options.

With __rebase__, you can rebase the feature branch to begin on the tip of the master branch.  This basically moves the entire feature branch to begin at the end of the master branch.  The issue with rebase is that you __re-write__ the project history by creating brand new commits for each commit in the original branch.  You get a cleaner project history (linear project history), but this is dangerous.  If you have to, consider doing an __interactive rebasing__ to alter commits as they are moved to the new branch so you can get complete control over the branch's commit history.  Helpful commands are `pick` and `fixup`.  Do NOT use rebase on a public branch (i.e. if someone else might be looking at the branch).

    git checkout feature
    git rebase -i master

##<a id="diffworkflows">Different Workflows</a>

Besides using git as just a simple save in time on one branch workflow, we can have other options depending on project and team size.

####<a id="featurebranch">Feature Branch Workflow</a>

The __Feature Branch Workflow__ is a git workflow where all feature development takes place in a dedicated branch instead of the __master__ branch.  This means that the master branch will only contain valid code and that work on a particular feature does not disturb the main code.

####<a id="gitflow">GitFlow Workflow</a>

GitFlow is a specific type of workflow for larger projects and is built off of the __Feature Branch Workflow__.  The branch structure is slightly more complicated by having more specific roles to different branches and adding in tags around a project release.

  * Historical Branches - instead of a single master branch, there is now __master__ and __develop__
  * Feature Branches - each feature resides in its own branch.  Feature branches branch from __develop__ instead of master.
  * Once develop has enough features for a release, you fork a release branch off of develop.  No new features are added and only bug fixes, document generation are added.  Once this is ready to ship, the release gets merged into __master__ and tagged with a version number.  Once merged back to master, we merge back into develop.
