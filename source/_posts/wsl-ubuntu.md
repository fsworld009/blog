title: Install Ubuntu instance in WSL
date: 2019-01-20 22:44:05
tags: [linux, Ubuntu, WSL, Windows]
---

There is already one when you enable WSL, but I'd like to install it on
different drives, and not need to worry about messing it up, so I discovered
a WSL Management tool [LxRunOffline](https://github.com/DDoSolitary/LxRunOffline)

<!--more-->


## Download and install
1. follow the quick start [here](https://github.com/DDoSolitary/LxRunOffline/wiki)
to download the Ubuntu for MS image, then install it with 

  ```bash
LxRunOffline i -n Ubuntu -d F:\WSL\Ubuntu -f <path_to_ubuntu_image> -s
  ```

2. To start ubuntu, run
  ```bash
  LxRunOffline run bash -n Ubuntu
  ```

  Or you can create a shortcut as well
  ```
  LxRunOffline s -n Ubuntu -f F:\Ubuntu.lnk
  ```

  You should be login as root

3. Create User, similar to what we do in Arch Linux install, we need to create
a user with ability to sudo without password
    1. run `visudo` and add a new user group
    ```
    %wheel ALL=(ALL) NOPASSWD: ALL
    ```
    2. create user: `useradd -m -G wheel -s /bin/bash user1`

4. run `id user1` to get UID, and run `LxRunOffline su <number> -n Ubuntu`,
   so next time you will be login as user1 on launch.

5. configure [WSL_DISTRIBUTION_FLAGS](https://docs.microsoft.com/en-us/windows/desktop/api/wslapi/ne-wslapi-wsl_distribution_flags),
I set to 5 so that it won't append %PATH% from Windows
    - `LxRunOffline se 5 -n Ubuntu`


## Package Manager

This time I'd like to try [Linuxbrew](http://linuxbrew.sh/)
for installing packages for developer works (like nodejs).
For system packages I'll try to stick with `apt`. This way it should be
safer to do upgrades for my work environment.

I chose Linuxbrew mainly because my current work laptop is Mac, so it is more
consistent for me to switch between work laptop and my Windows PC.

The other reason is that apt prefers stable over latest version, so it might be
difficult to get what I need for work.

1. Following their guide to do
`sudo sh -c "$(curl -fsSL https://raw.githubusercontent.com/Linuxbrew/install/master/install.sh"`,
I get "Don't run this as Root!" error, but without `sudo` it won't
have permission to create or write to `/home/linuxbrew/` when not using root.

2. Solution: create that folder beforehand and set owner to `user1`
```bash
cd /home/
sudo mkdir linuxbrew
sudo chown -R user1 linuxbrew/
```
  Then you should be able to run the script without `sudo`

3. The output message contains suggestion on following installation steps:
  ```text
  - Install the Linuxbrew dependencies if you have sudo access:
    Debian, Ubuntu, etc.
      sudo apt-get install build-essential
    Fedora, Red Hat, CentOS, etc.
      sudo yum groupinstall 'Development Tools'
    See http://linuxbrew.sh/#dependencies for more information.
  - Add Linuxbrew to your ~/.profile by running
      echo 'eval $(/home/linuxbrew/.linuxbrew/bin/brew shellenv)' >>~/.profile
  - Add Linuxbrew to your PATH
      PATH="/home/linuxbrew/.linuxbrew/bin:$PATH"
  - We recommend that you install GCC by running:
      brew install gcc
  - After modifying your shell profile, you may need to restart your session
    (logout and then log back in) if the brew command isn't found.
  - Run `brew help` to get started
  - Further documentation:
      https://docs.brew.sh
  Warning: /home/linuxbrew/.linuxbrew/bin is not in your PATH.
  ```

  So we do
  ```bash
  sudo apt-get update
  sudo apt-get install build-essential
  echo 'eval $(/home/linuxbrew/.linuxbrew/bin/brew shellenv)' >>~/.profile
  brew install gcc
  ```

4. Prepend `/home/linuxbrew/.linuxbrew/bin` into `$PATH`:
  - LxRunOffline has option to set default environment variables but I'm not
    sure how to edit multiple lines.
  - So I open `regedit` and go to
    `Computer\HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Lxss\<Distro ID>`
    and edit `DefaultEnvironment` to
    ```
    HOSTTYPE=x86_64
    LANG=en_US.UTF-8
    PATH=/home/linuxbrew/.linuxbrew/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
    TERM=xterm-256color
    ```

5. now you should be able to install packages, like `brew install git`

## Backup instance configs
1. export registry at
`Computer\HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Lxss\<Distro ID>`,
you could check `DistributionName` and find out which one is the correct ID.

2. LxRunOffline has export config option, but I haven't tried importing it back
yet: `LxRunOffline ec -n Ubunto -f Ubuntu.xml`

## Probably don't need to bother with running GUI app anymore

[The color issue on WSL console is resolved in October update (1809)](https://github.com/Microsoft/console/issues/136#issuecomment-433151087) and [ConPTY support is available on VS Code insiders build](https://github.com/Microsoft/vscode/issues/45693#issuecomment-449495252),
the best workflow on Windows (for me at least) now is use VS Code on Windows,
and launch Ubuntu terminals inside VS Code Terminal emulator.

Just change your settings.json to the following:
```json
{
  "terminal.integrated.shell.windows": "F:\\wsl\\LxRunOffline\\LxRunOffline.exe",
  "terminal.integrated.shellArgs.windows": ["run", "bash", "-n", "Ubuntu"],
  "terminal.integrated.windowsEnableConpty": true
}
```

Previously with Winpty, red bg will be rendered as yellow, and a lot of
black text get rendered as white, as below:

![Terminal with Winpty](../../../../images/vscode_wsl_terminal_winpty.png)


Now with Conpty:

![Terminal with Conpty](../../../../images/vscode_wsl_terminal_conpty.png)

Not too bad right? The font rendering is much better with Conpty as well.