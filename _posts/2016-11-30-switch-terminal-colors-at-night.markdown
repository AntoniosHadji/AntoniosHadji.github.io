---
layout: post
title:  "Change Gnome Terminal Colors by Time of Day"
date:   2016-11-30 11:08:00 -0500
modified: 2016-11-30 11:08:45 -0500
categories: technology
tags: linux, terminal
comments: true
---

As of Ubuntu 16.04 (maybe earlier) the Gnome Terminal has built in support for [Solarized][1] Dark and Light themes.  I also run [Redshift][2] on my machine to reduce the blue light that can interfere with sleep. ([f.lux][3] is another popular app for the same purpose).

I typically use Solarized Dark during the day.  However, when Redshift starts working in the evening, the screen gets very difficult to see.  I had been using a [bash script][4] in combination with ``.bashrc`` to change the colors of the default profile.  This stopped working when I updated to Ubuntu 16.04.  So it was back to roughing it, and making the change manually.

Today I discovered a new way to make this happen.  I found that the gnome terminal is actually started by a perl script, ``/usr/bin/gnome-terminal.wrapper``.

I created a copy of this file and placed it in my ``$HOME/bin``.  Then I added the following code to the top of the file:

~~~perl
($sec, $min, $hour) = localtime();
if ($hour ge 17)
{
  push(@args, '--profile=Solarized-Light');
}
~~~

I alread created a Gnome Terminal Profile called Solarized-Light. On the Colors tab in the profile preferences, I selected the Solarized light built-in scheme and the Solarized palette.

Since this is a system file it will likely be updated in the future.  To make my system use my file instead of the system file I used the update-alternatives system.

~~~bash
sudo update-alternatives --install /usr/bin/x-terminal-emulator x-terminal-emulator $HOME/bin/gnome-terminal.wrapper 10
sudo update-alternatives --config x-terminal-emulator
~~~


Now when I launch a terminal with Ctrl-Alt-T shortcut my custom wrapper will start my default Solarized-Dark profile before 5pm and Solarized-Light after 5pm.

This has the added benefit of automatically reminding me that the work day is over and it is time to wrap up.  

[1]: http://ethanschoonover.com/solarized
[2]: http://jonls.dk/redshift/
[3]: https://justgetflux.com/
[4]: https://gist.github.com/codeforkjeff/1397104#file-solarize-sh
