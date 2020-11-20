# git-integrate

This adds a new subcommand to git to build integration branches. It does this
by doing a sequence of commands:

1. Fetch all updates from all remote repositories configured.
2. Create or reset the branch passed in using `origin/master` as the base.
3. Merge each branch that has a pull request that is labeled with the label
   passed to the command. This is done in the order in which the pull requests
   have been created from oldest to newest.

If you encounter a merge conflict; resolve it, then run `git commit --no-edit`,
and finally re-run `git integrate $LABEL $BRANCH` again. When done merging
everything the tool should be on the resulting branch ready to be pushed.

For easier and more reliable results enable git-rerere. This will help reduce
the number of merge conflicts that have to be fixed.

## Installation
Installing git-integrate is straightforward!

1. [Install rust for your system if you have not done so](https://www.rust-lang.org/tools/install)
2. Ensure Cargo is added to your $PATH and restart your terminal
3. Install git-integrate by running
```bash
cargo install --force --path .
```
4. Then follow the steps below in Configuration

## Configuration

Get a GitHub token and select `repo - Full control of private repositories` for
the scope if you want to use it with private repositories. Then either modify
your `~/.gitconfig` file and add the following:

```
[integrate]
  github-token = <insert token here>
```

Or you can just run the following command:

```bash
git config --global integrate.github-token <insert token here>
```

git-integrate uses [git rerere](https://git-scm.com/docs/git-rerere) to resolve merge conflicts. To enable rerere, add the following to your project's config in `<project_root>/git/config`:

```
[rerere]
  enabled = true
```

Or run the following command:

```bash
git config --local rerere.enabled true
```

To add the setting globally instead, run the following command:

```bash
git config --global rerere.enabled true
```

## Usage

Simple scenario:
```bash
# Parameters
git integrate MILESTONE BRANCH

# Example
git integrate 1024 release-candidate/1986-06-14-000
git push --force origin deploy/staging
```

Merge Conflict:
```bash
git integrate 1024 release-candidate/1986-06-14-000

# Fix merge conflict here

git commit --no-edit
git integrate 1024 release-candidate/1986-06-14-000
git push --force origin deploy/staging
```
