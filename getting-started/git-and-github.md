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

A Git-based project is called a *repository*, or repo for short.

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

## Learning Git

To learn Git, work through the following tutorials: the [official Git
tutorial](https://git-scm.com/docs/gittutorial) and the [Git everyday
tutorial](https://git-scm.com/docs/giteveryday#STANDALONE).

From now on, we expect that you have some basic familiarity with Git commands
and how they are used.

## Reviewing Key Git Concepts: The Working Tree and The Index

We briefly summarize some key concepts from the tutorials above. This
terminology is ubiquitous and important to understand.

A single Git repository can keep track of an arbitrary number of branches. The
current branch that is being used, and all of the commits that were made on this
branch, is called the *working tree*.

When you *check out* a new branch, you're replacing the contents of the current
working tree with the contents of the last commit on the new branch. The last
commit on a branch is often called the branch *HEAD*. The HEAD keyword can be
used in many Git commands, see the documentation for more details.

The *index*, or *staging area*, consists of changes to files you wish to include
in your next commit. When you run commands such as `git add file1 file 2`,
you're modifying the index to include the changes made to `file1` and `file2` in
the next commit. As you probably know by now, you can inspect which files in the
working tree have changed since the last commit, and which are staged in the
index, by running `git status`.

## Git Best Practices

The following are a list of best practices which will help you and the team use
Git effectively.

### Use Descriptive Names

When creating new branches, tags, etc. try to use descriptive names. Long,
descriptive names are preferable to short, uninformative ones.

### Never Push Directly to `master`

In general, it is not a good idea to push directly onto the `master` remote
branch. As a rule, `master` should always hold a version of the project which
compiles without errors or warnings, and on which all tests pass. Pushing
directly to `master`, especially without testing, can compromise this.

More generally, be wary when pushing to a remote branch. If you rebase a branch
and then `git push -f` it onto a remote branch, you could be ruining the branch
history for others working on the same branch. This is considered bad practice,
and your teammates will not appreciate it.

### Create Good Commits

A good commit consists of:

1. A set of related changes to a group of related files.
2. An informative, well-crafted commit message.   

Creating good commits requires discipline.

Each commit should be a self-contained snippet of progress towards a certain
goal. Trying to combine multiple unrelated changes in a single commit is bad
practice. For example, if you're implementing two completely different features,
they should each have their own commits. If these features require multiple
commits, they should be developed on their own branches.

Every commit message should contain a short title summarizing *all* changes,
followed by a more detailed summary of the changes in bullet point form, or a
paragraph of descriptive text. Correct spelling, grammar and punctuation are
expected. The commit message should allow other developers to quickly and easily
identify whether the commit in question contains changes they care about.

If you create commits which glob unrelated changes together and have useless
commit messages your commits will not be merged.

Read about `git amend` and `git rebase` to learn how to tidy up a branch's
commit history.

### Merge Changes Using Pull Requests

As mentioned above, you should never be pushing directly onto `master`.

Instead, create a pull-request on the project's Github page, and request a code
review from one of the leads. Branches merged directly to `master` without
review will be reverted.

## Git Cheat Sheets

The following Git cheat sheets are quite useful: [cheat sheet written by
Github](https://education.github.com/git-cheat-sheet-education.pdf), [cheat
sheet written by
Atlassian](https://www.atlassian.com/git/tutorials/atlassian-git-cheatsheet).

## Additional Resources

To learn more about Git, or read about the many features we have not mentioned,
run `git help`, `git help <command>` or read the documentation
[online](https://git-scm.com/).
