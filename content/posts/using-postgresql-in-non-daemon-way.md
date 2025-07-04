---
title: Using postgresql in non daemon way
description:
date: 2021-12-12 
draft: false
tags:  en #postgresql
---


I have a preference against installing servers locally and running them as systemd services, which is why I'm a big fan of Docker.

However, there are times when I simply want to use servers as specific apps, without having to go through a system-wide installation, even with docker.

Take #postgresql as an example. A typical installation creates a user, installs files in your root directories, changes ownerships, creates service files, and so on.
<!--more-->
Today, I aimed to run PostgreSQL without turning it into a systemd service. It's actually simpler than you might think. Here are the steps:

1. Download the executable (I used nix for this).

2. Create a directory for PostgreSQL data.

3. Apply the appropriate permissions to the folders.

4. Run it!

```
adduser postgres #create user from ui in macos
su postgres
mkdir ./var/data
./psql/initdb ./var/data -U postgres -W
#write password
./bin/postgres -D var/pgsql/data -k .
./bin/psql -h 127.0.0.1
#its ok now, let create a db
./bin/createdb hedefim -h 127.0.0.1
#you can create another super user
./bin/createuser -s hedefim_user -W -h 127.0.0.1
#write new password

#you can now connect the database with your new user
./bin/psql -U hedefim_user -W hedefim -h 127.0.0.1
```

how its easy, right.

talk soon. 

