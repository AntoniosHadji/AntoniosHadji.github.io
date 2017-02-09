---
layout: post
title:  "Change Gnome Terminal Colors by Time of Day"
date:   2016-11-30 11:08:00 -0500
modified: Sun 01 Jan 2017 17:13:12 EST
categories: technology
tags: linux, terminal, cron
comments: true
---

As of Ubuntu 16.04 (maybe earlier) the Gnome Terminal has built in support for [Solarized][1] Dark and Light themes.  I also run [Redshift][2] on my machine to reduce the blue light that can interfere with sleep. ([f.lux][3] is another popular app for the same purpose).

I typically use Solarized Dark during the day.  However, when Redshift starts working in the evening, the screen gets very difficult to see.  I had been using a [bash script][4] in combination with ``.bashrc`` to change the colors of the default profile.  This stopped working when I updated to Ubuntu 16.04.  So it was back to roughing it, and making the change manually until I found another [Gnome terminal solarized script][7] to script this change.  

However, I ran into another problem with this script.  It would not run correctly from cron since it uses `dconf` (on Ubuntu 16.04) and the `$DBUS_SESSION_BUS_ADDRESS` is not available in the limited cron environment.

I finally found this great [answer on stackoverflow][8] that explain how to get a correct `$DBUS_SESSION_BUS_ADDRESS` with cron enviroment.  I then created the following short script to change the only three parameters that I care about, `background-color`, `foreground-color`, and `bold-color` in my Default profile.

The first three lines are the important part for getting the script to run from cron.

```bash

#!/bin/bash
# inspired by https://github.com/Anthony25/gnome-terminal-colors-solarized
# cron script to switch terminal colors in the evening

# Set up environment if necessary
export DISPLAY=:0
# -z True if the length of string is zero.
if [ -z $DBUS_SESSION_BUS_ADDRESS ]; then
  # http://stackoverflow.com/questions/10374520/gsettings-with-cron
  PID=$(pgrep gnome-session)
  export DBUS_SESSION_BUS_ADDRESS=$(grep -z DBUS_SESSION_BUS_ADDRESS /proc/$PID/environ|cut -d= -f2-)
fi
[ -z $DBUS_SESSION_BUS_ADDRESS ] && exit

# http://ethanschoonover.com/solarized
# light
base3='#fdfdf6f6e3e3'  # background
base00='#65657b7b8383' # body text/default code/primary content
base01='#58586e6e7575' # optional emphasized content
# dark
base03='#00002b2b3636' # background
base0='#838394949696'  # body text/default code/primary content
base1='#9393a1a1a1a1'  # optional emphasized content

profile_id="$(dconf read /org/gnome/terminal/legacy/profiles:/default|sed s/\'//g)"
# echo $profile_id >> /home/antonios/terminal-color.log
# http://www.tldp.org/LDP/abs/html/functions.html
set_dark() {
  dconf write /org/gnome/terminal/legacy/profiles:/:$profile_id/background-color "'$base03'"
  dconf write /org/gnome/terminal/legacy/profiles:/:$profile_id/foreground-color "'$base0'"
  dconf write /org/gnome/terminal/legacy/profiles:/:$profile_id/bold-color "'$base1'"
}

set_light() {
  dconf write /org/gnome/terminal/legacy/profiles:/:$profile_id/background-color "'$base3'"
  dconf write /org/gnome/terminal/legacy/profiles:/:$profile_id/foreground-color "'$base00'"
  dconf write /org/gnome/terminal/legacy/profiles:/:$profile_id/bold-color "'$base01'"
}

# http://www.tldp.org/LDP/abs/html/comparison-ops.html
# quotes needed here, otherwise error when no arguments
if [ "$1" = "light" ]; then
  set_light
elif [ "$1" = "dark" ]; then
  set_dark
else
  # http://www.tldp.org/LDP/abs/html/quotingvar.html
  # quotes not needed in this case, because value will always be single word (number)
  if [ "$(date +%H)" -ge 17 ]; then
    set_light
  else
    set_dark
  fi
fi
```
[This file on Github][9]

I then added the following line via `crontab -e`

```bash
0 17 * * * ~/dotfiles/term-color.sh light
```

This setup switches all open terminals from dark to light at 5 pm.  This has the added benefit of being a reminder to wrap up my work day when the screen turns to the light color scheme.  This only works while I am logged in.  Anh that is OK since I only need the switch when I am on my computer.  I also run this script when I login to make sure that the terminal is the correct color for the time of day.  That part is set up in the graphical Startup Applications app in Ubuntu.  

One more step is required for this to work correctly in Vim solarized colorscheme.  I added these lines to my `.vimrc`:

```vim
let hour = strftime("%H")
if hour >= 17
  set background=light
else
  set background=dark
endif

colorscheme solarized
```

This doesn't make vim change immediately at 5 pm.  However, I can see a thin outline of the terminal color outside of vim in gnome-terminal.  This is enough for a reminder it's time to wrap up.  And if I exit and restart vim it will be in light solarized color scheme.

If using neovim, there is a timer available to trigger this change within neovim.  I've been testing the code below and it works.

```vim
let hour = strftime("%H")
let minute = strftime("%M")
let second = strftime("%S")
if hour >= 17
  set background=light
else
  set background=dark
endif

if has('nvim')
  if hour < 17
    func ColorHandler(timer)
      set background=light
    endfunction
    let timer = timer_start((17-hour)*60*60*1000-(minute*60*1000)-(second*1000), 'ColorHandler')
  endif
endif
colorscheme solarized
```

You may be thinking that running a timer is too much overhead for this.  I did not notice any performance hit.  This has also encouraged me to make the switch to using neovim as my primary editor.

If this post was interesting or useful to you I would love to hear your comments below.  

[1]: http://ethanschoonover.com/solarized
[2]: http://jonls.dk/redshift/
[3]: https://justgetflux.com/
[4]: https://gist.github.com/codeforkjeff/1397104#file-solarize-sh
[5]: https://github.com/seebi/dircolors-solarized
[6]: https://github.com/AntoniosHadji/dotfiles/blob/master/dircolors
[7]: https://github.com/Anthony25/gnome-terminal-colors-solarized
[8]: http://stackoverflow.com/questions/10374520/gsettings-with-cron/19666729#19666729
[9]: https://github.com/AntoniosHadji/dotfiles/blob/master/term-color.sh
