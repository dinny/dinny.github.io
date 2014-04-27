---
layout: post
title: "Notify Your Commandline - Growl and libnotify"
date: 2013-04-06 21:00
comments: true
categories: linux-shell
---

When using shell, it's a pain to monitor a script with a long execution time. I have to switch between the terminal and other applications, back and forth. If there's a way to send out notifications(here I mean, a valid way that could catch my attention) when some scripts are completed, I can save a piece of time and do a short reading.

On Mac OS X, we have the **Growl** notification service. In the commanline, you can use Growl's commandline tool, `growlnotify` to send notification to the daemon. For various scripting languages, it has [supporting libraries](http://growl.info/documentation/developer/bindings.php), like **[ruby_gntp](https://github.com/snaka/ruby_gntp)**.

On Linux system, we have **[libnotify](https://developer.gnome.org/libnotify/)**. In the commandline, you can use `notify-send` command to send notifications to the daemon. On most Linux distributions, this command is packaged in *libnotify-bin* package.


## Using Growl (Mac OS X)


Using Growl is very easy. Purchase/install Growl(including commandline tools) and launch it. Then you can send a notification as below:

``` bash
growlnotify --appIcon "time machine" \
            --title "Data Backup"  \
            --message "Your data backup process has been completed."
```
It will look like this.

![image](https://www.evernote.com/shard/s116/sh/7a6e977b-7620-4b92-b6cc-eaa6d00cf764/120cdf1bc1f3c8306345be45bc4b37c9/res/f9645c72-abc0-4c03-a65f-67517272b1e5/skitch.png?resizeSmall&width=832)

In this example, we use some options:
 
- --appIcon specifies an app icon to use for the notification.
- --title specifies the title for the notification.
- --message specifies the text to be the content of the notification.

`growlnotify` supports some other options. You can use `growlnotify --help` to get the full list. The `-w` / `--wait` option is useful if you don't want Growl to close the notification automatically.


## Using Libnotify (Linux)


On Linux, we can use **libnotify**. If you haven't installed it yet, install it first:

``` bash
# debian/ubuntu
sudo apt-get install libnotify-bin

```

Then you can give `notify-send` a try:

``` bash
notify-send -t 10000 -i baterry-full \
            "Battery Charge Done" \
            "\nYour battery charging process is done."
```

In this example, the options are:

- -t specifies the expire time(in *milliseconds*).
- -i specifies the icon to show.

It has some other options. You can check with `man notify-send`.

> Note: In this example, I just use **-i battery-full** to specify the icon, not its full path. It can auto-expand the path(*/usr/share/icons/gnome/32x32/status/battery-full.png*). If you want to specify your own icon file, you should use the full path of your image file. On most Linux distributions, the icon files are stored in **/usr/share/icons/**.


## When to Use, When Not


I **STRONGLY** suggest you add the notification function into your shell library, esp. if you are working on some time-consuming scripts in a graphical desktop environment. I found it very useful when I was working on building Android project(typically 20-30 mins). And later, when I was creating a customized Debian live system, it helped me a lot as well.

But keep in mind, DON'T use it in a loop. Using it in a loop is just like shooting your own head. You will get a lot of output on your screen and it will make you angry.

And it can't replace logging if your scripts are running in the background, esp. if they are run as cron jobs. In that case, use logging, **output redirection** and **tee**.
