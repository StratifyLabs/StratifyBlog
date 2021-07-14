---
categories:
- Device Tips
date: "2021-07-12"
layout: post
draft: false
tags:
- cpp
- programming
- git
title: Semantic Versioning on Github
---

I have been using Github for over 8 years and I finally figured out how to implement [semantic versioning](https://semver.org/) in a way that works for me. 

> In case you are unfamilar, ***semantic versioning*** is using `x.y.z` as the release number. 
> - `x` is a major release (non API compatible). 
> - `y` is a new feature release (API backwards compatible). 
> - `z` is a patch (for bug fixing).

This is how my Github workflow is:

- Manage changes in a `CHANGES.md` file
- Use main for the next feature release
- Create a branch for each feature release (e.g. v4.0.x)
- Create a tag for each patch release (e.g. v4.0.1)
  - Minimize changes to bug fixing commits
  - Use `git cherry-pick` to apply bug fixes from main to feature branches

This approach allows users of the code to access stable releases without and without bug fixes. They also have the option to pull in new features without breaking their build.

My release branches look like this:

![Github Semantic Branches](/images/github-semantic-branches.svg)

## The `CHANGES.md` File

If you don't have a stable release yet, you will say so in the `CHANGES.md` file. 

```
# Pre-Release

This project is currently in a pre-release status. The API is subject to change.
```

Once you get a stable release, your `CHANGES.md` file will look something like:

```
# Version v1.0

Initial stable release.
```

When you get to this point, you will create a branch in your repository called `v1.0` (or similar). You will also tag the new `v1.0` branch as `v1.0.0`.

### `main` branch changes

The `main` branch is now the pre-release `v1.1` branch. So it will have feature updates and bug fixes. You will add them to your `Changes.md` file at the top.

```
# Version v1.1.0

## New Features

- Description of new feature

## Bug Fixes

- Description of bug fix

# Version v1.0.0

Initial stable release.
```

### `v1.0` branch changes

The `v1.0` branch `CHANGES.md` file will never get synced up with the `main` branch. It will look like this:

```
# Version v1.0.1

## Bug Fixes

- Description of bug fix

# Version v1.0.0

Initial stable release.
```

Once you are ready to release `v1.0.1`, tag the branch using Github and create a new section at the top for `v1.0.1`

### `v1.0` vs `main` CHANGES.md

On the left is `CHANGES.md` in the `v4.0` branch of Stratify OS while the right is the same file in the `main` branch. These two files are always maintained separately. The `v4.0` branch is updated with patches (`v4.0.1`, `v4.0.2`, etc) so the changes only include bug fixes.

![Changes-comparison](/images/changes-comparison.png)


## Bug Fixing Commits

Any commits that are dedicated to bug fixing need to have minimal changes. This will make it easier to merge the changes to other feature release branches. If a bug fix is too extensive and won't easily merge, you should consider omitting it from previous releases.

When you commit bug fixes, you need to:

1. Not make any feature changes or modification to `CHANGES.md` within the same commit
2. Note that the commit needs to be cherry-picked. I usually add this to the commit message so I can see on Github which commits need to be applied retroactively.

Here is an example from [Stratify OS](https://github.com/StratifyLabs/StratifyOS). I made a commit to the `main` branch that was a simple bug fix. In the Github commits, I can see that it needs to be applied to v4.0.

![cherry-pick-commit](/images/cherry-pick-commit.png)

I can then use `git cherry-pick` to apply the change:

```
git clone https://github.com/StratifyLabs/StratifyOS
git checkout v4.0
git cherry-pick 1cb40bb86321773185ee23a067d96f5ea59e850b
```

Then I updated the `CHANGES.md` file:

```
# Version 4.0.2

## Bug Fixes

- Emit a fatal event if system memory is too small (1cb40bb86321773185ee23a067d96f5ea59e850b)

...
```

I also like to note the git commit hash in the `CHANGES.md` file so that it is easy to go and review the code if needed. Once `CHANGES.md` is updated, I commit and push (using either git or Github desktop).

## Using the Code

Now that the code has a nice release workflow, it is easy for anyone to use the code in their projects.

If I want to create a project using Stratify OS `v4.0.x`. I use:

```
git clone --branch=v4.0 https://github.com/StratifyLabs/StratifyOS
```

Then whenever I do `git pull`, it will apply patches that won't break my code. When version `v4.1.0` is release, I can switch to that branch.

```
git checkout v4.1
```

If I just want to stick to a single patch release:

```
git clone --depth=1 --branch=v4.0.1 https://github.com/StratifyLabs/StratifyOS
```

> `--depth=1` is optional and pulls just the tag rather than the repo history.

## Good Luck

It only took me nine years to settle on an approach that works for me. Hopefully with this information, you can find a workflow that works for you.

