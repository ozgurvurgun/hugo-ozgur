---
title: How to run commands as synchronous all tmux panes
description:
date: 2019-07-04 
draft: false
tags:  tmux #tips
---


You can run the same command in all active Tmux panes at the same time. 

You need to activate command mode in Tmux with Ctrl+B keys and update `synchronize-panes` setting to on.

`:setw synchronize-panes on`

To disable it, set as off.

`:setw synchronize-panes off`


You can toggle synchronize-panes setting without use on-off parameters.

`:setw synchronize-panes`

