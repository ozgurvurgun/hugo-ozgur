---
title: Mysterious php errors
description:
date: 2019-07-04 
draft: false
tags:  errors #php #debug #en
---


Sometimes you get an error like that: 

`container extension liip_imagine not registered`

You double-checked all configurations and you are absolutely sure nothing is wrong.

But code not works, why?

Because Php can't convert `i` to `I` if os language is `tr_TR`. Your code searches Liipİmagine and can't find that.
Change os's lang code to en_US.UTF-8 for fix that.

Or add this line to .bashrc or .profile file.

`export LANG=en_US.UTF-8`

bb.

