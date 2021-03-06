# Git

Version control is a system that records changes to a file or set of files over time so that you can recall specific versions later.

* Local Version Control Systems
* Centralized Version Control Systems: a single server that contains all the versioned files
* Distributed Version Control Systems: Every clone is really a **full** backup of all the data \(the repository, including its full history\).

Git was birth in 2005, developed by Linux development community. Git is amazingly fast, very efficient with large projects. Git has an incredible branching system for non-linear development.

Git thinks about its data more like a stream of **snapshots**. if files have not changed, Git doesn’t store the file again, just a link to the previous identical file it has already stored. In Git, nearly every operation is local.

Everything in Git is check-summed \(SHA-1 hash\). It's very hard to make unredoable changes.

[Learn how to migrate from SVN to Git in no-time flat](https://www.atlassian.com/software/bitbucket/how-to-migrate-svn-to-git).

## Git Basics

In Git, all files has 3 states: committed, modified, staged.

Git has 3 levels of **config**: 

* `git config --system` applied to every user on the system
* `git config --global` values specific personally to you
* `.git/config` specific to that single repository

The `git add` can be used to 1. tracking new files. 2. to stage files. 3. to do other things like marking merge-conflicted files as resolved.

The `git rm` stages the file’s removal and also remove it from your working tree.

The `git rm --cached FILE`  keeps the file in your working tree but remove it from your staging area.

You can amend your last commit by `git commit --amend`.

Use `git reset file` to unstage a staged file.

Use `git checkout file` to unmodify a modified file.

By `git clone`, you set up your local **master** branch to track the remote **master** branch.

To see more information about a particular remote, use `git remote show origin`.

### Tag

Git supports two types of tags: lightweight and annotated.

A lightweight tag `git tag v1.4`,  is very much like a branch that doesn’t change — it’s just a pointer to a specific commit.

An annotated tag `git tag -a v1.4 -m "my version 1.4"` is checksummed; contain the tagger name, email, and date; have a tagging message; and can be signed and verified with GNU Privacy Guard \(GPG\).

You will have to explicitly push tags to a shared server after you have created them by `git push origin tagname`.

### Detached HEAD

If you want to view the versions of files a tag is pointing to, you can do a git checkout, though this puts your repository in “detached HEAD” state, which has some ill side effects:

{% hint style="info" %}
You are in 'detached HEAD' state. You can look around, make experimental changes and commit them, and you can discard any commits you make in this state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may do so \(now or later\) by using -b with the checkout command again. Example: git checkout -b &lt;new-branch&gt;
{% endhint %}

In “detached HEAD” state, if you make changes and then create a commit, the tag will stay the same, but your new commit won’t belong to any branch and will be unreachable, except by the exact commit hash.

### RefLog

Every time your branch tip is updated for any reason, Git stores information for you in this temporary history. It’s important to note that reflog information is strictly local — it’s a log only of what you’ve done in your repository.

## Git Branching

Some people refer to Git’s branching model as its “killer feature”. Git encourages workflows that branch and merge often, even multiple times in a day.

A branch in Git is simply a lightweight movable **pointer** to the commits. Because a branch in Git is actually a simple file that contains the 40 character SHA-1 checksum of the commit it points to, branches are cheap to create and destroy.

```c
$ git checkout -b iss53
// Switched to a new branch "iss53"
```

**HEAD** is a pointer to the local branch you’re currently on.

![](../.gitbook/assets/screen-shot-2018-10-24-at-4.28.48-pm.png)

In this case, Git does a simple three-way merge, using the two snapshots pointed to by the branch tips and the common ancestor of the two. Git creates a new snapshot that results from this three-way merge and automatically creates a new commit that points to it.

![](../.gitbook/assets/screen-shot-2018-09-17-at-21.07.33.png)

You typically have two kinds of workflows:

1. Long-Running Branches \(progressive-stability branching\): having only code that is entirely stable in their **master** branch \(possibly only code that has been or will be released\). And another parallel branch named **develop**.
2. Topic Branches: a short-lived branch that you create and use for a single particular feature or related work.

![](../.gitbook/assets/screen-shot-2018-09-17-at-21.22.57.png)

### **Remote Branches**

Remote references are references \(pointers\) in your remote repositories, including branches, tags, and so on. Remote-tracking branches are references to the state of remote branches. They’re local references that you can’t move; Git moves them for you whenever you do any network communication, to make sure they accurately represent the state of the remote repository. Remote-tracking branches take the form like _origin/master_.

It’s important to note that when you do a _fetch_ that brings down new remote-tracking branches, you don’t automatically have local, editable copies of them. In other words, in this case, you don’t have a new _serverfix_ branch — you only have an _origin/serverfix_ pointer that you can’t modify.

The remote branch that the local branch tracks is called an “upstream branch”.

If you see "ahead by three and behind by one", meaning that there is one commit on the server we haven’t merged in yet and three commits locally that we haven’t pushed.

### Rebasing

In Git, there are two main ways to integrate changes from one branch into another: the **merge** and the **rebase**.

With the rebase command, you can take all the changes that were committed on one branch and replay them on another one.

![](../.gitbook/assets/screen-shot-2018-09-17-at-21.52.23.png)

At this point, you can go back to the master branch and do a fast-forward merge.

![](../.gitbook/assets/screen-shot-2018-09-17-at-21.52.43.png)

Rebasing makes for a cleaner history. If you examine the log of a rebased branch, it looks like a linear history: it appears that all the work happened in series, even when it originally happened in parallel.

Often, you’ll do this to make sure your commits apply cleanly on a remote branch — perhaps in a project to which you’re trying to contribute but that you don’t maintain. In this case, you’d do your work in a branch and then rebase your work onto _origin/master_ when you were ready to submit your patches to the main project. That way, the maintainer doesn’t have to do any integration work — just a fast-forward or a clean apply.

The Perils of Rebasing can be summed up in a single line: **Do not rebase commits that exist outside your repository**. In general the right way is to rebase local changes you’ve made but haven’t shared yet before you push them in order to clean up your story, but never rebase anything you’ve pushed somewhere.

## Git on the server

A remote repository is generally a **bare** repository — a Git repository that has no working directory. Because the repository is only used as a collaboration point, there is no reason to have a snapshot checked out on disk; it’s just the Git data. By convention, bare repository directory names end with the suffix .git.

You can use several type of protocols to make communication with server: local, HTTP, SSH, and Git protocol.

By HTTP, you can use things like username/password authentication. This is probably become the most popular way to use Git now. It can both serve anonymously like the git:// protocol, and also be pushed over with authentication and encryption like the SSH protocol.

SSH doesn’t support anonymous access to your Git repository. All your teammates can log into the server and get a shell as the _git_ user. If you want to restrict that, you will have to change the shell to something else in the _passwd_ file.

Git protocol listens on a dedicated port \(9418\), and with absolutely no authentication, it's suitable for serving a lot of traffic for a public project or serving a very large project that doesn’t require user authentication for read access.

## GitHub

To contributing to a project, you typically do the following steps:

1. **Fork** the project
2. Create a topic branch from master.
3. Make some commits to improve the project.
4. Push this branch to your GitHub project.
5. Open a **Pull Request** on GitHub.
6. Discuss, and optionally continue committing.
7. The project owner merges or closes the Pull Request.

A _Readme_ file serves typically illustrate the following things: 

* What the project is for.
* How to configure and install it.
* An example of how to use it or get it running.
* The license that the project is offered under.
* How to contribute to it.

## Git Tools

### Stashing and Cleaning

Often, when you’ve been working on part of your project, things are in a messy state and you want to switch branches for a bit to work on something else. The problem is, you don’t want to do a commit of half-done work just so you can get back to this point later.

To push a new stash onto your stack, run `git stash`. At this point, you can switch branches and do work elsewhere; your changes are stored on your stack.

To see which stashes you’ve stored, you can use `git stash list`.

You can reapply the one you just stashed by: `git stash apply`. To apply one of the older stashes, you can specify it by naming it: `git stash apply stash@{2}`.

The apply option only tries to apply the stashed work — you continue to have it on your stack. To remove it: `git stash drop stash@{0}`.

You’ll want to be pretty careful with `git clean` command, since it’s designed to remove files from your working directory that are not tracked. If you change your mind, there is often no retrieving the content of those files. A safer option is to run `git stash --all` to remove everything but save it in a stash.

To remove all the untracked files in your working directory, you can run `git clean -f -d,`  \(-force, -directory\), which removes any files and also any subdirectories that become empty as a result.

By default, the `git clean` command will only remove untracked files that are not ignored. Any file that matches a pattern in your .gitignore or other ignore files will not be removed.

### Reset Demystified

An easier way to think about `reset` and `checkout` is through the mental frame of Git being a content manager of three different trees: **HEAD**, **Index** and **Working Directory**.

`reset` does up to three basic operations:

1. Move the **branch** HEAD points to \(stop here if --soft\)
2. Make the Index look like HEAD \(stop here if --mixed, default\)
3. Make the Working Directory look like the Index \(end here if --hard\) ❗

![](../.gitbook/assets/image%20%281%29%20%281%29.png)

If you specify a **path**, reset will skip step 1, and limit the remainder of its actions to a specific file or set of files.

`git reset file.txt`, shorthand for `git reset --mixed HEAD file.txt`: this has the practical effect of unstaging the file.

### Submodules

It often happens that while working on one project, you need to use another project from within it. Submodules allow you to keep a Git repository as a subdirectory of another Git repository.

To add a new submodule you use the git submodule add command with the absolute or relative URL of the project you would like to start tracking:

```bash
$ git submodule add https://github.com/chaconinc/DbConnector
```

You should notice the new `.gitmodules` file. This is a configuration file that stores the mapping between the project’s URL and the local subdirectory you’ve pulled it into.

Since the URL in the `.gitmodules` file is what other people will first try to clone/fetch from, make sure to use a URL that they can access if possible. You can overwrite this value locally with git config for your own use. When applicable, a relative URL can be helpful.

```bash
git config --file=.gitmodules submodule.yourSubmodule.url ssh://username@gerrit.com/path/to/submodule
```

When you clone such a project, by default you get the directories that contain submodules, but none of the files within them yet. You must run two commands: `git submodule init` to initialize your local configuration file, and `git submodule update` to fetch all the data from that project and check out the appropriate commit listed in your superproject.

There is another way to do this which is a little simpler, however. If you pass `--recurse-submodules` to the `git clone` command, it will automatically initialize and update each submodule in the repository.

If you want to check for new work in a submodule, you can go into the directory and run `git fetch` and `git merge` the upstream branch to update the local code. Now if you go back into the main project and commit you will **lock** the submodule into having the new code when other people update.

There is an easier way to do this as well, if you prefer to not manually fetch and merge in the subdirectory. If you run `git submodule update --remote`, Git will go into your submodules and fetch and update for you.

