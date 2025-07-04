---
title: Slaying code with linux the tools i use to dominate in development
description:
date: 2023-04-25 
draft: false
tags:  linux #en #devenv #personal
---


As a developer, I've often been asked about my development environment. I love using Linux and have found it to be a powerful tool for my work. In this article, I will share my setup in simple terms and provide links to the tools I use.
<!--more--> 

Let's start with the most important thing for a developer: an editor. While IDEs provide many features, I prefer to use Linux as my IDE and stick with a text editor. Yes, you read that right. I'm addicted to [VIM](https://www.vim.org/)! It's a powerful editor that's included in all Linux distros and macOS. I prefer to use VIM with minimal plugin dependencies, so I only use [COC.vim](https://github.com/neoclide/coc.nvim) for the LSP client. That's it!

VIM already has great support for all my needs, and with COC, it's working like a charm. All I need to do is install the COC plugins specific to my language. For example, I use [intelephense](https://github.com/bmewburn/intelephense-docs/blob/master/installation.md) for PHP, [haskell-language-server-wrapper](https://haskell-language-server.readthedocs.io/en/latest/installation.html) for Haskell, and [pyright](https://github.com/fannheyward/coc-pyright) for Python.

As for my terminal, I use [Alacritty](https://github.com/alacritty/alacritty) as the emulator and [BASH](https://www.gnu.org/software/bash/) as the shell. I use [Tmux](https://github.com/tmux/tmux) as the terminal multiplexer to manage multiple terminal sessions. For window management, I use [i3](https://i3wm.org/) as the window manager, and [i3status-rs](https://github.com/greshake/i3status-rust) as the status bar.

For network management, I rely on [nmtui](https://linux.die.net/man/1/nmtui). [Docker](https://www.docker.com/) is my go-to container engine, and [docker-compose](https://docs.docker.com/compose/) helps me with container management. [GNUMake](https://www.gnu.org/software/make/) serves as my task manager, and I use [direnv](https://direnv.net/) for environment management.

These are all the tools I use in my Linux environment. I find this setup to be efficient for my workflow. If you're new to Linux or just getting started with developing, I highly recommend giving this setup a try.

> reviewed by chatgpt

