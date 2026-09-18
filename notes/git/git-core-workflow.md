# Git Core Workflow

Git is a version-control system. It records changes to files in a repository and allows me to inspect, preserve, compare, and share revisions.

## Repository and Working Directory

A Git repository contains the project's files plus Git's history and metadata.

The working directory is the version of those files I am currently working on.

Useful command:

    git status

`git status` tells me about the current state of the working directory and staging area.

It can show files that are:

    untracked  → Git has not started tracking the file
    modified   → a tracked file has changed
    staged     → a change is selected for the next commit

Git tracks files, not empty directories.

This is why creating a directory with:

    mkdir documentation

does not by itself give Git something to track.

## The Staging Area

The staging area contains the changes selected for the next commit.

Stage a specific file:

    git add PATH

Example:

    git add notes/git/git-core-workflow.md

Staging does NOT create a commit.

Mental model:

    working directory
           ↓ git add
    staging area
           ↓ git commit
    repository history

This gives me a chance to decide exactly which changes belong in a commit.

## Inspect Before Committing

See unstaged changes:

    git diff

See staged changes:

    git diff --cached

Check the overall state:

    git status

A good habit is:

    inspect → stage deliberately → inspect staged diff → commit

## Commits

A commit records the staged changes as a new point in the repository's history.

Create a commit:

    git commit -m "Describe the change"

A useful commit message should describe what the change accomplishes.

After committing, verify the repository state:

    git status

Ideally, after all intended changes have been committed:

    nothing to commit, working tree clean

## Viewing History

View recent commits:

    git log --oneline

This gives a compact history containing commit IDs and messages.

Example:

    fb62c26 Initialize IT career portfolio
    66d1ed0 Add skills evidence ledger

Each commit has a unique identifier.

A commit is stored in the local Git repository. It does not automatically mean the commit has been uploaded to GitHub.

Mental model:

    commit → record locally
    push   → send commits to a remote repository

## Local and Remote Repositories

My local repository exists on my Fedora computer.

A remote repository is another Git repository that Git can communicate with, such as one hosted on GitHub.

Show configured remotes:

    git remote -v

`origin` is the conventional name Git commonly uses for the primary remote repository.

Example:

    origin

`main` is the branch we are currently using in the curriculum repositories.

Push local commits to the remote:

    git push

Mental model:

    working files
        ↓
    staging area
        ↓
    local commit
        ↓
    push
        ↓
    remote repository

## My Normal Git Workflow

Before doing anything, inspect the repository:

    git status

After making changes, inspect what changed:

    git diff

Stage only the files that belong in the commit:

    git add PATH

Check what is staged:

    git status
    git diff --cached

If the staged changes are correct, commit them:

    git commit -m "Clear description of change"

Verify:

    git status
    git log --oneline

Then, when appropriate, send the commits to the remote:

    git push

Mental model:

    inspect
      ↓
    make changes
      ↓
    inspect
      ↓
    stage deliberately
      ↓
    inspect staged changes
      ↓
    commit
      ↓
    verify
      ↓
    push

## Avoid Blind Staging

This command stages changes throughout the current directory:

    git add .

It is useful, but it can also stage files I did not intend to include.

While learning and when working carefully, prefer staging specific files:

    git add PATH

Then inspect:

    git diff --cached

The goal is to know what I am committing rather than simply committing everything that happens to be present.

## Removing Files

Remove a tracked file through Git:

    git rm PATH

This removes the file and stages its deletion.

As with other changes, inspect the result before committing:

    git status
    git diff --cached

## Remember

- `git status` tells me the current repository state.
- `git diff` shows unstaged changes.
- `git add` stages selected changes.
- `git diff --cached` shows what is staged.
- `git commit` records staged changes in local history.
- `git log --oneline` shows compact commit history.
- `git remote -v` shows configured remotes.
- `git push` sends local commits to a remote.
- `origin` is commonly the name of the primary remote.
- Git tracks files, not empty directories.
- A commit is local until it is pushed.
- Do not stage or commit changes blindly.

