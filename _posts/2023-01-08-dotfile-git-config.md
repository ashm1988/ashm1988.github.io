---
layout: post
title: Dotfile git config
date: 2023-01-08 15:02 +1100
categories: [Dev environment]
tags: [dotfiles, git]
---

# Description 
Set up for dotfiles across machines, managed via git. 
[YouTube Tutorial](https://www.youtube.com/watch?v=LI_Tv5dJkkk)

# Pre-requisites
Git installed on the machine
Set git configs to store username 
```zsh
git config --global user.name ashm1988
git config --global user.email ashisgreat15@hotmail.com
git config --global credential.helper store
```


# Inital Set up 
Create an alias for the git dotfiles command. This is to seperate the dotfiles command/tree from normal git.
```zsh
# --git-dir sets the git to look for the .git files etc in ~/.dotfiles instead of .git of whatever location you are in
# --work-tree sets the git directory it searches to $home i.e. when you do status etc it looks in the home dir
alias gdfiles='/usr/bin/git --git-dir=$HOME/.dotfiles/ --work-tree=$HOME'
```
Create a folder for the repository. 
```zsh
mkdir ~/.dotfiles
```
Initialise the git repo 
```zsh
# --bare creates the .git management files in .dotfiles instead of .git in the current dir
git init --bare .dotfiles
``` 
Set the new repository/alias not to show untracked files to get rid of the noise of every untracked file on the machine.
```zsh
gdfiles config --local status.showUntrackedFiles no
```
Set the github remote repo  
> Make sure you already have a repo set up on github.

```zsh
# Check the local brach name is main. if not change it with `gdfiles branch -m main`
gdfiles remote add main <repo url>
```

# Pulling on a new machine 
Same Pre-requisites  
Create a folder for the repository. 
```zsh
mkdir $HOME/.dotfiles
```
Clone the git repo 
```zsh
# --bare creates the .git management files in .dotfiles instead of .git in the current dir
git clone --bare <github repo url> $HOME/.dotfiles
``` 
Checkout the files from the repo
```zsh
# use -f if you need to over ride files
git --git-dir=$HOME/.dotfiles/ --work-tree=$HOME checkout
```
Close and open terminal to get all the changes including the gdfiles alias.  
Set the new repository/alias not to show untracked files to get rid of the noise of every untracked file on the machine.
```zsh
gdfiles config --local status.showUntrackedFiles no
```
