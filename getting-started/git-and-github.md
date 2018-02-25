# Git and Github

As we mentioned before, Git is a tool used to manage software over time. Git
keeps track of changes to files, and allows developers to attach messages to
changes they've made to let others know what's changed. Git also supports a
workflow that enables large teams to collaborate on a complex codebase without
treading on each others toes.

For example, because Git preserves all of the changes in your codebase, if you
discover a fatal bug, you can revert a specific file or the entire project back
to an earlier bug-free state.

Git is a very powerful tool. We will only scratch the surface of what Git
can do. If you're interested in software development, it is well worth the
time to learn how to use Git productively.

Github is a separate website used to manage Git-based projects. It provides many
convenient features, such as a visual interface to view code changes and browse
the codebase.

## A Brief Overview of Git

Git tracks changes you make to the codebase and bundles them into *commits*.
Each commit includes data keeping track of the changes made to the codebase, as
well as a message describing the changes that were made. Thus, a commit is a
snapshot of the codebase at a particular point in time, along with a message
describing what has changed since the last commit.

Each commit occurs on a particular *branch*. A branch is a lightweight copy of a
project (from a particular commit) where you can experiment without having to
worry about clobbering or interfering with other people's changes.

The idea is that each time you want to build a new feature, you can create a
new branch, make commits on this new branch, and then *merge* this branch
with the codebase.

This workflow allows multiple people do develop different features in parallel.

A Git-based project is called a *repository*.

## Creating a Github Account

Visit [Github](https://github.com/) and create an account if you don't already
have one. Heron Mk II has a organization-wide Github account which hosts all
subsystem code.

Let Siddharth, Ali or Dylan know what your account name is, and we'll make sure
to add you to the organization. Unless you've been added to the organization,
you won't be able to access any of the codebase via Git.

## Setting Up Git

To make sure each commit you create is labelled correctly, Git needs to know
your name and email address. You can set your name and email address using
the following commands:

```
$ git config --global user.name "Your Name"
$ git config --global user.email "your@email.com"
```

## Git Workflow and Common Commands

    - clone a repo
    - checkout a branch
    - list branches
    - the staging area and adding files to the staging area
    - creating a commit
    - pushing changes
    - creating pull requests on github
    - deleting branches
    - setting up remote and upstream branches
    - stashing

Address some common issues which come up, like setting an upstream branch,
merging remote changes, stashing changes, switching between branches without
losing your work, ohshitgit, creating commits

## Fixing Mistakes and Troubleshooting

    - git amend
    - git rebase
    - fixing merge conflicts
    - reverting to an older commit
    - culling updates from master on an aux branch

Git is easy to fuck up. This section should cover some common problems: fixing
merge conflicts, reverting commits, reverting to an older state, merging recent
changes to master into your local branch which is 20 commits behind, etc.
