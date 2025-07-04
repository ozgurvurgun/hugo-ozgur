---
title: Get current file path in vim
description:
date: 2019-07-09 
draft: false
tags:  tips #en #vim #git
---


You can get file path in vim with % (percent) symbol. Sometimes you need to run a command with the current file path, for example `git add filepath` or `sh filepath`.

You can add the current file to git with this command in vim: 

`:!git add %`

or you can run any file with this command:

`:!%:p`

For example, you write a bash script and saved it. But you don't want to close vim or you don't want to go to another terminal screen. Just you need to write `:!%:p` and vim run the file for you.

