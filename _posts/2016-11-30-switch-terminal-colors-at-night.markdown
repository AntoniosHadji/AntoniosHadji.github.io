---
layout: post
title:  "Change Gnome Terminal Colors by Time of Day"
date:   2016-11-30 11:08:00 -0500
modified: 2016-12-15 17:31 -0500
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
export DISPLAY=:0

# http://stackoverflow.com/questions/10374520/gsettings-with-cron
PID=$(pgrep gnome-terminal)
export DBUS_SESSION_BUS_ADDRESS=$(grep -z DBUS_SESSION_BUS_ADDRESS /proc/$PID/environ|cut -d= -f2-)

profile_id="$(dconf read /org/gnome/terminal/legacy/profiles:/default|sed s/\'//g)"

# http://ethanschoonover.com/solarized
# light
base3='#fdfdf6f6e3e3'  # background
base00='#65657b7b8383' # body text/default code/primary content
base01='#58586e6e7575' # optional emphasized content
# dark
base03='#00002b2b3636' # background
base0='#838394949696'  # body text/default code/primary content
base1='#9393a1a1a1a1'  # optional emphasized content

# http://www.tldp.org/LDP/abs/html/comparison-ops.html
if [ "$1" = "light" ]; then
  dconf write /org/gnome/terminal/legacy/profiles:/:$profile_id/background-color "'$base3'"
  dconf write /org/gnome/terminal/legacy/profiles:/:$profile_id/foreground-color "'$base00'"
  dconf write /org/gnome/terminal/legacy/profiles:/:$profile_id/bold-color "'$base01'"
elif [ "$1" = "dark" ]; then
  dconf write /org/gnome/terminal/legacy/profiles:/:$profile_id/background-color "'$base03'"
  dconf write /org/gnome/terminal/legacy/profiles:/:$profile_id/foreground-color "'$base0'"
  dconf write /org/gnome/terminal/legacy/profiles:/:$profile_id/bold-color "'$base1'"
else
  echo "Requires parameter of light or dark"
fi
```
[This file on Github][9]

I then added the following two lines via `crontab -e`

```bash
0 17 * * * ~/dotfiles/term-color.sh light
0 6 * * * ~/dotfiles/term-color.sh dark
```

This setup switches all open terminals from dark to light at 5 pm.  This has the added benefit of being a reminder to wrap up my work day when the screen turns to the light color scheme.   My computer is set to wake at 6am so at 6am the color scheme changes back to dark.

For this to work correctly in Vim solarized colorscheme I added these lines to my `.vimrc`:

```vim
let hour = strftime("%H")
if hour >= 17
  set background=light
else
  set background=dark
endif

colorscheme solarized
```

This doesn't make vim change immediately at 5 pm.  However, I can see a thin outline of the terminal color outside of vim in gnome-terminal.  

[1]: http://ethanschoonover.com/solarized
[2]: http://jonls.dk/redshift/
[3]: https://justgetflux.com/
[4]: https://gist.github.com/codeforkjeff/1397104#file-solarize-sh
[5]: https://github.com/seebi/dircolors-solarized
[6]: https://github.com/AntoniosHadji/dotfiles/blob/master/dircolors
[7]: https://github.com/Anthony25/gnome-terminal-colors-solarized
[8]: http://stackoverflow.com/questions/10374520/gsettings-with-cron/19666729#19666729
[9]: https://github.com/AntoniosHadji/dotfiles/blob/master/term-color.sh
