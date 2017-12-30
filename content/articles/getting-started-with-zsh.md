---
title: "Getting Started With ZSH"
date: 2017-12-20T14:14:32Z
draft: false
tags: ["ZSH", "Terminal"]
categories: ["Guide"]
---

The following is a small story of my switch to zsh, followed up by my dotfiles management. I decided I needed to
upgrade my terminal experience and make it consistent between my main machine and any other machine I regularly use
such as the servers at University. To start, I decided to take the full plunge and give my terminal a makeover
starting with ripping out bash and replacing it altogether.

## What is ZSH?

`zsh` is a shell similar to `bash` with many powerful plugins and features. Getting started is easy. It it available on
homebrew, or through most package managers. Now, to use it as your default shell either:

### a) edit `/etc/shells` and `chsh -s`

```bash
echo $(which zsh) >> /etc/shells
chsh -s $(which zsh)
```
    
### b) add zsh to your `.profile` 

This script checks for the existence of zsh and runs it
meaning it can be synced across machines and run when zsh is available.

```bash
cd ~
echo "[ -x "$(command -v zsh)" ] && exec zsh -l" >> .bash_profile
```    

## Packages

With `zsh` enabled, you can now start to get acquainted. One of the great things of zsh are the many package managers
available for it, some examples of which being `oh-my-zsh`, `prezto` and `antigen`. My personal favorite is antigen,
because of how easy it is to define packages, and how unobtrusive it is. To get started with antigen you simply have to
download the script and source it from your newly created `.zshrc`.

```bash
curl -L git.io/antigen > antigen.zsh
echo "source PATH_TO_ANTIGEN/antigen.zsh" >> ~/.zshrc
```

Then you can start adding packages.