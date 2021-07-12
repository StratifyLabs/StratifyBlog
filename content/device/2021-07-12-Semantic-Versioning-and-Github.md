---
categories:
- Device Tips
date: "2021-07-12"
layout: post
draft: false
tags:
- microcontroller
- cpp
- programming
title: Semantic Versioning on Github
---

I have been using Github for over 8 years and I finally figured out how to implement [semantic versioning](https://semver.org/) in a way that works for me. This is my approach:

- Manage changes in a `Changes.md` file
- Use main for the next feature release
- Create a branch for each feature set (e.g. v4.0.x)
- Create a tag for each patch release (e.g. v4.0.1)
- Minimize changes to bug fixing commits
- Use `git cherry-pick` to apply bug fixes from main to feature branches

This approach doesn't start until you have a stable release. Anyone who uses your code will use your release branches and won't want to get hit with API breaking changes. Each user can decide if they want to jump to the next feature release or major release. They should be able to switch to the feature release without any modifications to their code.

You basic setup will look like this:

![Github Semantic Branches](/images/github-semantic-branches.svg)

## The `Changes.md` File

If you don't have a stable release yet, you will say so in the `Changes.md` file. 

```
# Pre-Release

This project is currently in a pre-release status. The API is subject to change.
```

Once you get a stable release, your `Changes.md` file will look something like:

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

The `v1.0` branch `Changes.md` file will never get synced up with the `main` branch. It will look like this:

```
# Version v1.0.1

## Bug Fixes

- Description of bug fix

# Version v1.0.0

Initial stable release.
```

Once you are ready to release `v1.0.1`, tag the branch using Github and create a new section at the top for `v1.0.1`

## Bug Fixing Commits

Any commits that are dedicated to bug fixing need to have minimal changes. This will make it easier to merge the changes to other feature release branches.

When you commit bug fixes, you need to:

1. Not make any feature changes or modification to `Changes.md` within the same commit
2. Note that the commit needs to be cherry-picked. I usually add this to the commit message so I can see on Github which commits need to be applied retroactively.

Here is an example from [Stratify OS](https://github.com/StratifyLabs/StratifyOS). I made a commit to the `main` branch that was a simple bug fix. In the Github commits, I can see that it needs to be applied to v4.0.

![cherry-pick-commit](/images/cherry-pick-commit.png)

I can then use `git cherry-pick` to apply the change:

```
git clone https://github.com/StratifyLabs/StratifyOS
git checkout v4.0
git cherry-pick 1cb40bb86321773185ee23a067d96f5ea59e850b
```

Then I updated the `Changes.md` file:

```
# Version 4.0.2

## Bug Fixes

- Emit a fatal event if system memory is too small

...
```

Then I use git or Github Desktop to commit and push the changes.

## Good Luck

It only took me nine years to settle on an approach that works for me. Hopefully with this information, you can find a workflow that works for you.

