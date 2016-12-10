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

Another script to make this change is available here: https://github.com/Anthony25/gnome-terminal-colors-solarized  
Using this script, I can change from dark to light with `./set_light.sh Default --skip-dircolors`  `Default` is the name of my default gnome-terminal profile. `--skip-dircolors` is because I am using my own [`.dircolors`][6] file that I created from the default Ubuntu colors.

I added these cron lines to run these scripts when I want the color switch to occur.  The script uses `dconf` which requires the `DISPLAY` variable to be set.  This can be set in the crontab before the script is called.

```bash
DISPLAY=:0
0 17 * * * $HOME/dotfiles/gnome-terminal-colors-solarized/set_light.sh Default --skip-dircolors
0 6 * * * $HOME/dotfiles/gnome-terminal-colors-solarized/set_dark.sh Default --skip-dircolors
```

This will have all open terminals switch from dark to light at 5 pm.  This has the added benefit of being a reminder to wrap up my work day when the screen turns to the light color scheme.   My computer is set to wake at 6am so at 6am the color scheme changes back to dark.

For this to work correctly in Vim I added these lines to my `.vimrc`:

```vim
let hour = strftime("%H")
if hour >= 17
  set background=light
else
  set background=dark
endif

colorscheme solarized
```


## Another way to setup the color theme for newly launched terminals.

**NOTE:** This is no longer used on my machine because the above script changes my default profile.  To remove the alternative I ran the following commands:

```bash
sudo update-alternatives --remove x-terminal-emulator $HOME/bin/gnome-terminal-wrapper
```

___


I found that the gnome terminal is actually started by a perl script, ``/usr/bin/gnome-terminal.wrapper``.

I created a copy of this file and placed it in my ``$HOME/bin``.  Then I added the following code to the top of the file:

~~~perl
($sec, $min, $hour) = localtime();
if ($hour >= 17)
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
[5]: https://github.com/seebi/dircolors-solarized
[6]: https://github.com/AntoniosHadji/dotfiles/blob/master/dircolors
