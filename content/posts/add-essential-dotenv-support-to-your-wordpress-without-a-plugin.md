---
title: Add essential dotenv support to your wordpress without a plugin
description:
date: 2022-10-16 
draft: false
tags:  php #wordpress #en #software #dotenv
---


You may want to start using Environment variables to configure your WordPress application. I did because I needed to install a development environment to my local and did not want to keep separate wp-config.php files.

So, how did I that?
<!--more-->
I am already using `direnv` to manage environment variables dependent on folders. When I work on X project, it automatically loads the required env variables. You can check out at [direnv.net](direnv.net) 

First, I created a `.envrc` file with that content;

```
dotenv .env
```

It will use variables inside the .env file to configure my environment. This is an example content of the `.env` file.

```
APP_ENV=dev
WP_DEBUG=true
WORDPRESS_DB_NAME=eresbiotech
WORDPRESS_DB_USER=moodif
WORDPRESS_DB_PASSWORD=1542bilim
WORDPRESS_DB_HOST=db
```

It's a simple key-value file. 

After that, I allowed the direnv to load that environment with the `direnv allow` command. 

Then, I updated my `wp-config.php` file to fetch variables from the environment.
I've just added database-related configs. You can add salt keys and other configs like this. 

```php
define('DB_NAME', getenv('WORDPRESS_DB_NAME'));
/** MySQL database username */
define('DB_USER', getenv('WORDPRESS_DB_USER'));

/** MySQL database password */
define('DB_PASSWORD', getenv('WORDPRESS_DB_PASSWORD'));

/** MySQL hostname */
define('DB_HOST', getenv('WORDPRESS_DB_HOST'));
```

You see,  easy-peasy. Not much. I expected it to work like a charm, but it didn't because I was using PHP-FPM and Nginx together. 

There are different ways to allow php-fpm to fetch system environment files, but I loved dotenv too much. There are tons of different scenarios in which you cannot access PHP or server configs (like using hosting)

Anyway, I decided to write this simple snippet (there is a [library](https://github.com/vlucas/phpdotenv) to do that, but I do not want to add a library to WordPress yet) 

```php
<?php
if(!getenv('APP_ENV') && file_exists(ABSPATH.'.env')){
    $content = file_get_contents(ABSPATH.'.env');
    $envs = explode("\n",$content);
    foreach($envs as $env){
        if(empty($env)){continue;}
        putenv($env);
    }
}
```
Then add that file to the `wp-load.php` file. After the first `if` condition;

```php
if ( ! defined( 'ABSPATH' ) ) { //this is the first if condition in the file
        define( 'ABSPATH', __DIR__ . '/' );
}
include(ABSPATH.'/dotenv.php'); //this is our code
```

Then it will be ready to use. `dotenv.php` file reads `.env` file content in your local/prod server, then it will load values. `wp-config.php` file will use that values to configure the WordPress.

Let me tell you one more thing before I finish the post. You have to achieve one more step if you are using `wp-cli` in your server. `wp-cli` is using `wp-config.php` to configure the tool itself, but this file does not know how to load `.env` file. We can add this snippet to the top of the `wp-config.php` file to prevent issues.

```php
if(!getenv('APP_ENV')){ //for wp-cli
  include './dotenv.php';
}
```

You did it!

You can have separate installations for your WordPress now without changing any code block or installing plugins. 

Of course, there is a downside to this approach. You have to care `wp-load.php` and `wp-config.php` files after every core update. But it's a tiny downside. I am using git to follow changes, I am just reverting the changes in this file after the update (or I am adding required lines to new versions of this file), and it's done.

keep safe kittens

> final notice; do not forget to configure the public access to `.env` file from outworld. no one wants to share db password with others. check http://yoursite.com/.env URL, it must return 404.  [for apache](https://www.google.it/search?q=how+to+deny+access+to+file+htaccess) , [for nginx](https://www.google.it/search?q=how+to+deny+access+to+file+nginx)

