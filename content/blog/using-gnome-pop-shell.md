---
date: "2021-07-09"
title: "Using GNOME Pop Shell on Fedora"
tags: ["linux"]
draft: false
---

# References
- [Pop Shell](https://github.com/pop-os/shell)
- [Unite Shell](https://github.com/hardpixel/unite-shell)

# Background
2021 is the year of the Linux desktop.

Recently, I learned about [Pop!_OS](https://pop.system76.com) and intrigued at how
much work the engineers at system76 have done to make the GNOME desktop environment
even more usable than it already is.

Of these customizations, their window manager, Pop Shell, layers in a window manager
on top of GNOME that simulates the functionality found in popular window managers such as i3 and Sway.

Even better, you do not need to have Pop!_OS to take advantage of this functionality as the 
tool is open source and widely available.

This post walks through the steps required to install and setup Pop Shell on a non-Pop!_OS system 
that uses the GNOME desktop environment. For this guide, I am using Fedora 34 and GNOME 40.

# Install
### Install Pop Shell
```bash
sudo dnf install gnome-shell-extension-pop-shell
```


### Install Unite Shell
If your Linux distribution does not use Wayland, skip this part.

The Pop Shell extension allows you to disable the title bars that adorn each window in the GNOME environment.
This is nice because it provides you with more screen real estate,
which allows you to get the most benefit of using a window manager.

However, as Fedora 34 uses Wayland by default and as of this writing,
the Pop Shell extension cannot disable window title bars. We can get
around this by downloading Unite Shell, which is a GNOME extension
that provides similar functionality.

 - Go to https://github.com/hardpixel/unite-shell/releases
 - Download the latest release
 - Extract the `zip` file to `~/.local/share/gnome-shell/extensions`

### Config
At this point, once both Pop Shell and Unite Shell (optional) are installed, 
reboot your machine.

Once the machine has been rebooted, open the GNOME Extensions application and enable 
the Pop Shell and Unite Shell extensions.

Click the gear icon next to the Unite Shell menu entry, and click the button next to "Hide window titlebars".

Both extensions allow you to customize the behavior and appearance of the desktop environment as you see fit,
but both are set to sensible options, which I have found to be adequate.

### Enjoy
At this point, your custom Pop Shell window manager has been set up! 
Refer to https://support.system76.com/articles/pop-keyboard-shortcuts
for making the most out of your desktop experience.