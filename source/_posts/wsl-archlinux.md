title: Setup Arch Linux in WSL
date: 2018-06-24 16:37:11
tags: [Windows, WSL, Archlinux]
---

Recently I have tried to install Arch Linux under WSL (Windows Subsystem for Linux). So far I have positive experience and I actually prefer WSL over VirtualBox as my working environment under Windows 10.

Of course WSL is not perfect, compare to installing Arch under VirtualBox:
- Pros
  - Faster startup time (just as fast as open a cmd window)
  - Share files bet
  - Running GUI apps via XServer is much more responsive, I think this is mostly because we don't need to run GUI via SSH with this setup.
- Cons
  - No "save machine state" functionality from VirtualBox
  - No audio support
  - There will always be a chance of running into compatibility issues, especially GUI apps.
  - You cannot edit any file under WSL data folder from Windows side, the file may become corrupted in WSL.
  - WSL won't see any new file created (except share folders under `/mnt/`), so if you want to copy some files into WSL, you have to copy it in WSL shell via `/mnt/` folder

I prefer WSL mainly because of better GUI experience, as I use VS Code for development most of the time.
  <!--more-->

## Download and install
- Go to https://github.com/yuk7/ArchWSL and download the installer, follow the installation steps in README
- Once you open the Arch.exe, the location for this WSL instance will be recorded in registry, so it's better to decide where you'd like to put it before starting the installation.

## Create user
1. Open `Arch.exe`, you should be prompted as `root` user
2. run `visudo` and remove comment (`#`) for the line 85:
```
%wheel ALL=(ALL) NOPASSWD: ALL
```
3. https://wiki.archlinux.org/index.php/users_and_groups
    1. `useradd -m -G wheel -s /bin/bash user1V`
    2. `passwd user1`

4. Change default login user to the user you created:
    1. exit Arch.exe and goback to cmd
    2. `Arch.exe config --default-user user1`

## Run GUI apps

1. Use one of the following to run XServer on Windows:
    - [https://sourceforge.net/projects/vcxsrv/](VcXsrv)
    - [https://sourceforge.net/projects/xming/](Xming)
    - [https://mobaxterm.mobatek.net/](MobaXterm)
    - [https://token2shell.com/x410/](X410)
      - This one is not free, but is the best of what I have tried so far
2. Generally there are 2 modes for X-Server"
    - Multi-windowed mode: use this if you only want to run individual GUI apps and have better "transparent" experience across Windows and Linux apps.
    - One-windowd (One large window) mode: use this if you want to run a Linux desktop environment (GNome, XFCE4, KDE...), and then run GUIs from environment.
- I personally prefer multi-windoed mode, but I also installed XFCE4 in my Arch instance, in case of unsupported app or I need to test out GUI apps without XServer (For example, fcitx cannot be properly configured without entering XFCE4).
3. In WSL shell run `export DISPLAY=:0`
4. Run GUI apps with `&`, for example `konsole &`
    - Note that closing the shell will also close all GUI executed from this shell.


### Configure XWindow
- Install `lxappearance` to configure appearance, cursor theme...etc.


### Example setup
This is my current setup:
- Open MobaXterm and start XServer (because this is the only one that supports VS Code)
- Use ConEmu to run Arch.exe
- `export DISPLAY=:0`
- Run Konsole: `konsole &`
- Run GUI from this ConEmu Tab, run other commands from Konsole



### Other references / tutorials:
- https://blog.ropnop.com/configuring-a-pretty-and-usable-terminal-emulator-for-wsl/
- https://nickjanetakis.com/blog/using-wsl-and-mobaxterm-to-create-a-linux-dev-environment-on-windows
- {% post_link archlinux  My previous post%} about other topics related to GUI (such as IME).


## Share files with Windows
- By default all windows drives are mount on `mnt`, this is considered as  share folders between Windows and WSL.
- You can turn this off by `Arch.exe config --mount-drive off`
- **DO NOT**  edit any file in WSL folder (even your home folder) with Windows tools, the files might become corrupted.
- Also do not copy data into WSL data folder from Windows, WSL won't detect it. Instead use share folder and copy files from WSL side.
- It is safe to edit files under `/mnt/` share folders from both side.


## Backup / move instance
- Registry location: `[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Lxss\{(WSLID)}`
- Update the `BasePath` key after moving to preserve the WSL instance (not tested yet).
- When moving this Arch instance to another Windows 10 installation, copy all data and registry to the new installation (not tested yet)


## Sound support
Use pulseaudio for sound

On Windows side:
- download pulseaudio from https://github.com/kitor/wsl/
- run `pulseaudio.exe` to start server (could launch it inside WSL)
- it seems that this version will not shut down properly, would have to do `killall pulseaudio.exe` in bash or kill it via Task Manager
On Linux side:
- `pacman -Syu pulseaudio`
- Also need `xfce4-pulseaudio-plugin` for Xfce4
- `sudo vim /etc/pulse/client.conf` and add this line:
```
default-server = tcp:localhost
```
- Restart xfce4


Could also check [the guide](https://token2shell.com/howto/x410/enabling-sound-in-wsl-ubuntu-let-it-sing/) from X410 official site


## Script
Since it requires some setup to properly launch desktop environment, it is
easier to put everything in a launch script. Here is
[my example](https://github.com/fsworld009/dotfiles/blob/master/wsl/startxfce4.sh).


## Troubleshooting notes

### fakeroot-tcp
`fakeroot` is not supported in WSL, so this Arch instance replaced it with `fakeroot-tcp`.

Sometimes when installing packages, you'll get prompted if you'd like to replace `fakeroot-tcp` with `fakeroot`, be sure to choose NO. If you happened to replace it, you'll be stuck with a broken system. As `fakeroot` is not working but a working `fakeroot` is required in order to reinstall `fakeroot-tcp` from AUR again. 

The solution is to build a `fakeroot-tcp` package from a working Arch linux system and copy the .tar file, or download it from https://davidtw.co/writings/2017/archlinux-on-the-windows-subsystem-for-linux, and install it using `pacman -U`. (This article also explains this issue better)

### Cannot run QT5 apps
- When running QT5, I got this error:
```
error while loading shared libraries: libQt5Core.so.5: cannot open shared object file: No such file or directory
```
- Workaround: `sudo strip --remove-section=.note.ABI-tag /usr/lib/libQt5Core.so.5.10.1`
  - https://github.com/Microsoft/WSL/issues/3023

### Disable PATH from Windows
- By default `$PATH` included all PATHs from Windows, this can be changed by `Arch.exe config --append-path off`
- For me I got `HRESULT:0x80004005` error when trying to do so, I solved by going to registry (refer above for registry location) and changed the `Flags` key to 5.
  - You can see how to configure this field [here](https://docs.microsoft.com/en-us/previous-versions/windows/desktop/api/wslapi/ne-wslapi-wsl_distribution_flags).
  - If nothing changed after you edited the flag, try reboot Windows.

### Reboot WSL instance
- `sudo killall -r '.*'`
- https://superuser.com/questions/1126721/rebooting-ubuntu-on-windows-without-rebooting-windows

### Cursor icon is too big and not rendered properly when running GUI via XServer
- Solution: switch to a cursor theme that is smaller, such as `xcursor-simpleandsoft`
- https://wiki.archlinux.org/index.php/Cursor_themes
