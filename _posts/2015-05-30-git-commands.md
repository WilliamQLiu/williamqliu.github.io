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
*  [Cleanup](#cleanup)
    -  [Remove Large Files](#removelarge)
    -  [Remove passwords, secret info](#removesecrets)
   
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

##<a id="cleanup">Cleanup</a>

BFG Repo-Cleaner is a tool to remove large (e.g. blobs bigger than 1M) or bad data (e.g. passwords, credentials, private data) and this is faster and easier to use than the `git-filter-branch` command.

####<a id="removesecrets">Remove passwords and secret information</a>

1. Clone your repo using the `--mirror` flag: e.g. `git clone --mirror https://github.com/WilliamQLiu/reponame.git`
2. Download `BFG Repo-Cleaner` [here](https://rtyley.github.io/bfg-repo-cleaner/ "BFG Repo-Cleaner").  You will get a file that looks like this `bfg-1.12.3.jar`
3. Create a `passwords.txt` file and add in all the data you want to remove (e.g. mypassword, 'mypassword')
4. Run the command `java -jar bfg-1.12.3.jar --replace-text passwords.txt`
5. Then run `git reflog expire --expire=now --all && git gc --prune=now --aggressive`
6. Finally, `git push`

Funny story: When I first ran this, I accidentally put the passwords.txt file in Git.  I had to rerun BFG to remove the passwords file.  Oops.

