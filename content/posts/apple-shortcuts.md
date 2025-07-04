---
title: Apple shortcuts
description:
date: 2022-08-28 
draft: false
tags:  quicknote #en #mac #macosx #apple #shortcuts
---


I have a MacBook as a backup computer. I love to use mac for non-development kinds of tasks, like surfing the internet, using ms office applications, writing blogs, etc. 

Apple announces an application named Shortcuts. I am a person who has an obsession with automation. And I loved it. I tried to use it for several purposes. 

I wanted to add a shortcut for translating selected text to Turkish. There is a built-in translate app in macOS but there is no Turkish support, unfortunately. So, I decided to create a new shortcut for that task.
<!--more-->
Logic is simple, get selected text, append to the google translate URL and open it. Not much. 

I reviewed a few shortcut snippets and finally got it how can I do that?

The magic part is getting a text from the selection. I used to use X clipboard to do that in Linux but I do not want to spend time finding an equivalent version on mac. There is an action in the Shortcut app as named Share under the Sharing category. 

I did use that as the entry point, limited it with text type, and use Quick Menu as the input source. Then I set a variable named $query and combine the query variable with the URL https://translate.google.com/?text=$query, and finally, I added Open action with that value. 

It is not easy to tell that in words. Let me share a screen. (Eventually, it's visual programming) 

![](http://historyofart.emre.xyz/wp-content/uploads/2022/08/Screen-Shot-2022-08-28-at-19.24.50.png)

Then I went to System Preferences -> Keyboard -> Shortcuts -> Services section. And find my shortcut and added keyboard shortcuts for the service.

Now I can select any text in any document then I need just press the option+command+T and it will open the correct URL with the selected word. 

