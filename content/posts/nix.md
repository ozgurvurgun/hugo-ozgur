---
title: Nix
description:
date: 2020-08-01 
draft: false
tags:  nix #en #nixos #devops
---


Nix is a package management language that has a massive ecosystem about solving DevOps problems.

Nix, is a functional and domain-specific programming language. Nix also is a package manager for Linux and Unix based systems like aptitude and brew.
<!--more-->
But, not just for a single system. It's an independent package manager. So you can use Nix to manage system packages or your language packages. You can install Nginx with Nix, and you can install `is_array` npm package, or you can install `HTTPBundle` for Symfony, or you can manage your Python packages. Also, you can install different versions of the same package to the same environment. 

There is a ton of usage area of Nix. Also, Nix has a great ecosystem for developers. You can use NixOS if you want to manage your whole system via Nix, and you can use NixOps if you are going to build your applications to a network.

Today I will show you a simple introduction about Nix. I planned a Nixops introduction and "[how you can deploy a real Symfony application via Nixops]()" titled article. I'll share it soon.


As I said, you can manage your system packages with Nix. Just install Nix your Linux or macOS; (You have to root access for install the nix.)

```
curl -L https://nixos.org/nix/install | sh
```

That command installs the `nix-env` tool to your system. You can use nix like `brew` or `apt` with that command. 

You can get an installable packages list with `nix-env -qa` command; also, you can check an [online tool](https://nixos.org/nixos/packages.html) for searching packages.

You can install packages with `nix-env -i hello`  command. `-i` argument for `install`

Check your package installed directory via `which hello` command. It will produce a result like that on Linux: `/home/delirehberi/.nix-profile/bin/hello`

You'll get a `Hello, World!` respond when you run `hello` command. You can also install firefox with `nix-env -i firefox` command.

Nix is a purely functional programming language. So you have only pure functions. And all your functions just gave a single argument. There is an example Nix function; `x: x*2` is a function that gets an integer and returns the product with 2.  I will not give a language lesson. You can read [nix-pills](https://nixos.org/nixos/nix-pills/basics-of-language.html) for it.

You can write your build script with Nix language. Nix repository contains a lot of derivations for a ton of packages. Every time you want to install a package, a derivation will work and build that application in your system with precisely valid dependencies. You will not see dependency related errors. If an application needs the` curl` library, it will install as independently. 

You can read about Nix in the [official](https://nixos.org/) web site. Also, I'll write different Nix and Nixops documents for the PHP ecosystem in this blog.

Keep yourself healthy!

