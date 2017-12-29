---
title: "Getting Started With ZSH"
date: 2017-12-20T14:14:32Z
draft: true
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

With `zsh` enabled, you can now start to get acquainted. One of the strengths o
