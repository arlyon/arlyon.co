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

With antigen added to your zshrc, you can start using antigen packages. Antigen lets you download packages straight from
github, simply by running the command `antigen bundle USER/REPO`. Additionally, antigen can use all the packages from 
oh-my-zsh as well such as git, heroku or pip. For example, to load a github bundle try running the following code:

    antigen bundle MichaelAquilina/zsh-you-should-use
    
or, to use an oh-my-zsh package:

    # Load the oh-my-zsh's library.
    antigen use oh-my-zsh
    
    # Bundles from the default repo (robbyrussell's oh-my-zsh).
    antigen bundle git
    antigen bundle heroku
    antigen bundle pip

The package will download (unless already cached) and will be applied immediately. To automate this when we start the
terminal, it makes sense to add these declarations to the `.zshrc` file as well. Here are some useful zsh packages that 
antigen can install for you:

### [MichaelAquilina/zsh-you-should-use](https://www.github.com/MichaelAquilina/zsh-you-should-use)

This plugin is fairly straight-forward. It simply reminds you when there is an already existing alias for the command
you are typing.

### [zsh-users/zsh-autosuggestions](https://www.github.com/zsh-users/zsh-autosuggestions)

This plugin adds nifty autosuggestion to your prompt, which suggests a completion for the current command based on your
history. Pressing the left arrow will then fill the command for you, saving time for repeated commands (such as in my
case `source venv/bin/activate`).

### [zsh-users/zsh-syntax-highlighting](https://www.github.com/zsh-users/zsh-syntax-highlighting)

The last plugin on in the list brings syntax highlighting to the terminal which (amongst other things) lets you visually
see if a command exists before even running it. Very useful.

## Wrapping Up

A final feature antigen boasts is the ability to apply themes, through the `antigen theme` command. The theme I use is
[spaceship](https://github.com/denysdovhan/spaceship-zsh-theme) which is a fairly robust 2-line command line with
a whole load of features. Its quite straight forward, and needs a bit of tweaking and testing to see what you like. When
you have chosen your configuration of choice, the last command to run is `antigen apply`.

Here is my complete configuration:
    
    # --- ANTIGEN ---
    source $HOME/antigen.zsh
    
    # Load the oh-my-zsh's library.
    antigen use oh-my-zsh
    
    # Bundles from the default repo (robbyrussell's oh-my-zsh).
    antigen bundle git
    antigen bundle heroku
    antigen bundle pip
    
    # Bundles from elsewhere in git.
    antigen bundle zsh-users/zsh-autosuggestions # autosuggests
    antigen bundle chrissicool/zsh-256color # sets the $TERM correctly
    # antigen bundle MichaelAquilina/zsh-you-should-use # reminds you that there is an alias
    antigen bundle zsh-users/zsh-syntax-highlighting # Syntax highlighting
    
    # Load the theme.
    antigen theme https://github.com/denysdovhan/spaceship-zsh-theme spaceship
    
    # Tell Antigen that you're done.
    antigen apply

To start heading down the rabbit hole, have a look at [awesome-zsh-plugins](https://github.com/unixorn/awesome-zsh-plugins)
which includes an exhaustive list of plugins for zsh, and lastly, happy zsh-ing.