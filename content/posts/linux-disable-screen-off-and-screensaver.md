---
title: Disabling Screen Off and Screensaver on Debian / Linux
description:
date: 2024-07-25
draft: false
tags:  en
---

If you're using a Linux system and want to prevent your screen from turning off or your screensaver from activating, you can use the `xset` command to adjust your Display Power Management Signaling (DPMS) settings and screensaver settings. Here’s how you can do it:
<!--more-->

## Disable DPMS Settings

DPMS controls power-saving features such as standby, suspend, and off. To disable these features, run the following command:

```sh
xset -dpms
```

Alternatively, you can set each DPMS setting to zero, which effectively disables them:

```sh
xset dpms 0 0 0
```

## Disable the Screensaver

To disable the screensaver, you can use the `xset s` command. Setting the timeout to `0` will turn off the screensaver completely:

```sh
xset s off
```

Or you can set the screensaver timeout to `0`:

```sh
xset s 0 0
```

## Verify Your Settings

After running these commands, you can verify the changes by executing:

```sh
xset q
```

This will display your current settings, allowing you to confirm that DPMS and the screensaver are disabled.

By using these commands, you can ensure that your screen remains on and your screensaver does not activate, providing an uninterrupted experience on your Linux system.
