---
title: Composer is not a builder
description:
date: 2019-07-29 
draft: false
tags:  composer #nix #php #symfony #sylius #en #nixops #composer2nix
---


Composer is an installer and a package management tool. So please stop writing build scripts
for Composer, building anything inside your package folder. 

Last few days, I try to deploy a Symfony project with Nixops to Google Cloud. I found the composer2nix package after some research. 
<!--more-->
Composer2nix is a nix-expression builder. It parses `composer.lock` file and generates nix expressions for every composer packages independently. After that, you can use generated files in your deployment file like that:

```nix
  pkgs = import <nixpkgs>{};
  my_app = import /var/www/app/source{
    inherit pkgs;
  };
```

Nix builds your application and system when you run `nixops deploy`. 

But NixOps generates a hash folder and installs your package inside this `stateless` and `read-only` folder. After that, it makes relations all required packages and your project.

composer2nix  generated expression works the same way, e.g., your composer.lock file if had `theofidry/alice-data-fixtures` package. composer2nix generates expression like that:

```nix
"theofidry/alice-data-fixtures" = {
      targetDir = "";
      src = composerEnv.buildZipPackage {
        name = "theofidry-alice-data-fixtures-79913820cf6965cd6ea204cc5882079486f8262e";
        src = fetchurl {
          url = https://api.github.com/repos/theofidry/AliceDataFixtures/zipball/79913820cf6965cd6ea204cc5882079486f8262e;
          sha256 = "108862vlq2j10x69h5i4f4vqh63rfizvkc5kl57ymssk79ajifz2";
        };
      };
    };
```

Nix uses this expression for fetching wanted package and version in the given URL, and it fetches only once if version not changed in the `composer.lock` file.

So, as you have seen, there is no need for `composer install`. After the package installation process, we need the dumping `autoload.php` file. Nix makes that and runs `composer install` for checking everything is ok.

This roughly explains how composer2nix works and how to build your PHP project with nix.

But I faced a problem when I run `nixops deploy` command. Seems a package wants to modify a file in `vendor/ocramius/package-versions/src/PackageVersions/Versions.php`.  

I said below; nix independently install packages in separate `read-only` folders. Ok. 
What can I do? I created an issue on the GitHub. Ocramius gently responded it and said it works if I disable running composer scripts with `--no-scripts` parameter. Ok, problem solved for now. But I have many scripts like `assets:install` or `cache:clear` 

Now, I must manually clear caches all of the backends.

Someone should be saying, "Why did you use this package and not another package?" Sorry guys, it is not about me, Sylius depends on these packages.

I decided to write this blog. Forget the Nix. It is not only about nix deployment.

I want to ask some questions to PHP package developers.

Why you are building something after package install inside the package folder. Composer not for that. 

It is not only about composer scripts. Why do you modify files inside your package folder? Why `vendor/<package>` is not `stateless`?

Please don't do that. Don't touch your package folder after install.

ps: if you curios about our conversation with @ocramius about this situation, you can read from [github issue](https://github.com/Ocramius/PackageVersions/issues/106)

