title: Install Ubuntu instance in WSL
date: 2019-01-20 22:44:05
tags: [linux, Ubuntu, WSL, Windows]
---

There is already one when you enable WSL, but I'd like to install it on
different drives, and not need to worry about messing it up, so I discovered
a WSL Management tool [LxRunOffline](https://github.com/DDoSolitary/LxRunOffline)

<!--more-->

(Updated 2020/06/26: Found a note I wrote back in Sep 2019 when I was trying
to install WSL in a new laptop, so added a troubleshoot section below. Also
added a section for upgrading to WSL2)

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

  Or use `wsl` command

  ```
  wsl -d Ubuntu
  ```

  You can also set default wsl version so that you only need to type `wsl`
  ```bash
  wsl --set-default Ubuntu
  wsl
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

  # This one is optional, can also edit $PATH from wsl, see step 4
  echo 'eval $(/home/linuxbrew/.linuxbrew/bin/brew shellenv)' >>~/.profile

  brew install gcc
  ```

4. (Only required if you skipped echo 'eval... command above)
   Prepend `/home/linuxbrew/.linuxbrew/bin` into `$PATH`:
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


## Troubleshoot

### visudo command not found
https://askubuntu.com/questions/1103038/cant-run-visudo-within-docker-sudoers-file-does-not-exist

### visudo: no editor found (editor path = /usr/bin/editor)  

```bash
apt install vim
```

###  useradd: group 'wheel' does not exist

```bash
addgroup wheel
```


### /home/linuxbrew/.linuxbrew/Homebrew/Library/Homebrew/brew.sh: line 4: warning: setlocale: LC_ALL: cannot change locale (en_US.UTF-8): No such file or directory       

https://askubuntu.com/questions/114759/warning-setlocale-lc-all-cannot-change-locale

```bash
sudo apt install -y locales
sudo vim /etc/locale.gen
# uncomment en_us.UTF-8 UTF-8 in locale.gen, then save
sudo locale-gen
```

## Upgrade to WSL 2

(2020/06/26) Note that if your project is put on Windows file system and
shared to WSL distro, currently changing files from Windows won't trigger
file change notifications on Linux side
(see https://github.com/microsoft/WSL/issues/4739)

1. Download amnd install WSL 2 Kernel from
   https://docs.microsoft.com/en-us/windows/wsl/wsl2-kernel
2. Enable the 'Virtual Machine Platform' optional component
   - https://docs.microsoft.com/en-us/windows/wsl/install-win10
   - Open Powershell as Administrator and run:
      ```powershell
      dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
      ```
3.
  ```bash
  wsl --set-version Ubuntu 2
  ```

Prefer setting default wsl version to 2 after this
```bash
wsl --set-default-version 2
```
