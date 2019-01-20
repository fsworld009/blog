title: Issue with pacman
date: 2019-01-20 22:01:45
tags: [Archlinux, linux]
---
Been struggling to get my Arch Linux instance to do the upgrade for the past
two days.

First off, one of the PGP Key on my local is expired, so every pacman command
will return
```
error: pkgbuilder: signature from "Chris Warrick <kwpolska@gmail.com>" is unknown trust
```
and I cannot update `archlinux-keyring` to get the latest, renewed key because
I don't have a valid key, it's a infinite loop.

Later on I figured out that I need to temporarily disable pkgbuilder upgrades,
described in [here](../../../../2017/12/29/pacman/).


<!--more-->
After the upgrade is done, I cannot open VS Code or Google Chrome anymore.
It looks like an issue with chromium based Apps.

`code --verbose` returns
```
/var/run/dbus/system_bus_socket: No such file or directory
shared memfd open() failed: Function not implemented
```

and `google-chrome-stable --verbose` returns
```
Failed to move to new namespace: PID namespaces supported, Network namespace supported, but failed: errno = Permission denied
```


I managed to resolve d-bus issue by
```bash
sudo mkdir /var/run/dbus
sudo dbus-daemon --config-file=/usr/share/dbus-1/system.conf
```

and the namespace issue by opening bash with Administrator, but I'm not able to
resolve that memfd open() error.

Turns out this is a regression introduced by udev and libudev1.
[(source)](https://github.com/WhitewaterFoundry/WLinux/issues/285#issuecomment-453153832)


Now I'm stuck here because pacman basically forcing every package to be on the
latest version. Yes there is a way to downgrade certain packages, but that means
all dependencies will be downgraded, too, so I can't garantee that would work
as well.

At this point I am thinking about giving up on Arch Linux and pacman, almost
every time there is some issue when doing system upgrades.

Now thinking about it, the way pacman handle packages is unstable to me.

Sometimes I just need to upgrade one package, but pacman will upgrade everything
to the latest, which means the whole system will be at risk of breaking.

It has its advantages, but I don't think I have enough knowledge and patience
to handle these upgrades issues.

I think it's time to try other distros :thinking_face:
