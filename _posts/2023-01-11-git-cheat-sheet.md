---
layout: post
title: Git cheat sheet
date: 2023-01-11 10:02 +1100
categories: [Cheat Sheets,Git]
tags: [documentation,cheatsheet,git]
---

# Commands
# initial set up
```zsh
# Set global username
git config --global user.name ashm1988
# Set global email address
git config --global user.email ashisgreat15@hotmail.com
# Set next instance of username and password to store ($HOME/.git-credentials)
git config --global credential.helper store
# Set default branch name to main (need at least git version 2.28)
git config --global init.defaultBranch main
# Set not to show untracked files. (useful for --bare / dotfile repositories)
git config --local status.showUntrackedFiles no
```
