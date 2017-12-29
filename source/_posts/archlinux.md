title: Random notes for installing Archlinux & Xfce4 on Virtualbox
date: 2017-12-28 10:53:52
tags: [Archlinux, Linux]
---

Below are random notes I jotted down when I installed Archlinux and Xfce4 on virtualbox. It has been a while so there might be errors here and there.

## Guest System configuration
- EFI enabled
- GPT partition table for all disks
- GRUB bootloader
- Network setting: NAT

<!--more-->
## Install
- Basically follow this guide https://wiki.archlinux.org/index.php/installation_guide  
- Article for VirtualBox: https://wiki.archlinux.org/index.php/VirtualBox

Notes for installation steps:
### Partition the disks
1. use `gdisk` to create GPT partition table for `/dev/sda/`
2. create boot partition with `EF00` file system code
3. `mkswap /dev/sdb` create swap partition (I created another virtual disk with GPT table for swap only)

https://wiki.archlinux.org/index.php/EFI_System_Partition#GPT_partitioned_disks  
https://wiki.archlinux.org/index.php/Swap#Swap_partition  

### Boot loader
Install GRUB: https://wiki.archlinux.org/index.php/GRUB#Installation_2
1. `grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=grub`
2. create a script `/boot/startup.nsh` containing this line: `FS0:\EFI\grub\grubx64.efi`
  - https://wiki.archlinux.org/index.php/VirtualBox#Installation_in_EFI_mode


## Activate Numlock on boot
- https://wiki.archlinux.org/index.php/Activating_Numlock_on_Bootup#Extending_getty.40.service
- `sudo systemctl edit getty\@.service`
```
[Service]
ExecStartPre=/bin/sh -c 'setleds +num < /dev/%I'
```

## Enable internet on boot
1. `ping www.google.com`
2. If no internet, run `systemctl enable dhcpcd@enp0s3.service` and reboot
- https://unix.stackexchange.com/questions/218084/no-internet-connection-for-new-installation

## Create main user
- https://wiki.archlinux.org/index.php/users_and_groups
1. `useradd -m -G wheel -s /bin/bash fsworld009`
2. `passwd fsworld009`
3. add user to other groups: `sudo gpasswd -a fsworld009 group_name`
4. current group setup:
```shell
$ id fsworld009
uid=1000(fsworld009) gid=1000(fsworld009) groups=1000(fsworld009),10(wheel),92(audio),109(vboxsf)
```

## Xfce4
1. Install: `sudo pacman -S xorg xfce xfce-goodies`
2. start xfce4: `startxfce4`
- I didn't make xfce4 run on boot, just feel like it is better to login to shell only by default

Notes for xfce4 environment:
### Customize startup script 
~/.config/xfce4/xinitrc
```
export GTK_IM_MODULE="fcitx"
export QT_IM_MODULE="fcitx"
export XMODIFIERS="@im=fcitx"
imwheel
source /etc/xdg/xfce4/xinitrc
```
All configs need to be above the source call

### Audio
1. add user to `audio` group:
`sudo gpasswd -a fsworld009 audio`
2. pulseaudio is required for Firefox audio output, so I use pulseaudio as audio core
3. `sudo pacman -S alsa-utils pulseaudio`
4. detailed explanation: https://askubuntu.com/a/427036/776335

### Change default app
- https://www.linuxquestions.org/questions/slackware-14/change-terminal-for-thunar-%27open-terminal-here%27-custom-action-924097/
- `exo-preferred-applications` or Applications -> Settings -> Preferred Applications

### Change mouse scroll speed
- http://www.webupd8.org/2015/12/how-to-change-mouse-scroll-wheel-speed.html
1. `sudo pacman -S imwheel`
2. add `imwheel` to `~/.config/xfce4/xinitrc`
3. create `~/.imwheelrc`:
```
".*"
None,      Up,   Button4, 6
None,      Down, Button5, 6
```

### Chinese and Japanese Font
- I use Google Noto fonts
- `sudo pacman -S noto-fonts noto-fonts-cjk noto-fonts-emoji`

### IME
- `gcin` does not work properly in many applications (like Google Chrome), so I installed `fcitx` instead
1. `fcitx-chewing` for Traditional Chinese, `fxitc-mozc` for Japanese
2. you need `fcitx-configtool` package in order to config IMEs
3. add the following to `~/.config/xfce4/xinitrc`:
```
export GTK_IM_MODULE="fcitx"
export QT_IM_MODULE="fcitx"
export XMODIFIERS="@im=fcitx"
```


## VirtualBox related topics
### Install VirtualBox Guest addon
1. `sudo pacman -S virtualbox-guest-utils`
2. enable kernel module: `systemctl enable vboxservice.service`
3. Xfce4 should automatically load guest services, if not then do `VBoxClient --clipboard --draganddrop --seamless --display --checkhostversion` or `VBoxClient-all`
- **DO NOT** use hibernate from archlinux, use VirtualBox host's save state feature instead. Otherwise guest addon will break on resume and it can only be fixed by full reboot.

### Mount shared folder

1. Users have to be in `vboxsf` group (created after installing `virtualbox-guest-utils`):   
`sudo gpasswd -a fsworld009 vboxsf`
2. `cd /;sudo mkdir shared`
3. Manual mount:  
`mount -t vboxsf shared_folder_name mount_point_on_guest_system`
4. Mount in fstab:  
`shared /shared/ vboxsf  uid=1000,gid=1000,rw,dmode=700,fmode=600,noauto,x-systemd.automount`

- https://blog.gtwang.org/tips/virtualbox-shared-folder/
- https://wiki.archlinux.org/index.php/VirtualBox#Enable_shared_folders

## Mount other disks in fstab
- https://wiki.archlinux.org/index.php/fstab
1. Run `lsblk -f` to get UUIDs
2. edit `/etc/fstab`
```
# /dev/sdc1
UUID=enter_uid_here       mount_point      ext4    defaults,nofail,x-systemd.device-timeout=1      0       2

```
- all options: https://help.ubuntu.com/community/Fstab#Options


## SSH server
- Run a ssh server so that host machine can connect via SSH.
1. setup port forwarding in VM setting (Network -> Advanced): for example TCP, host 2222 to guest 22
2. `sudo pacman -S openssh`
3. enable the following options in `/etc/ssh/sshd_config`:
```
ListenAddress 0.0.0.0
Protocol 2
PermitRootLogin no
ChallengeResponseAuthentication no
UsePAM yes
Subsystem sftp /usr/lib/ssh/sftp-server
```
4. add `sshd:ALL` to `/etc/hosts.allow`
5. `sudo systemctrl start sshd.socket`, I created another user to authorize the service, not sure if this is required.
6. check sshd log: `journalctl /usr/bin/sshd`
7. test ssh server locally: `ssh fsworld009@0.0.0.0:22`
    - If you don't have ssh keys yet you need to generate one, refer to GitHub guides: [Check fot existing SSH keys](https://help.github.com/articles/checking-for-existing-ssh-keys/), [Generating a new SSH key and adding it to the ssh-agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)
8. Ssh from host machine: `ssh 0.0.0.0 -p 2222`
- Other refs: http://smalldd.pixnet.net/blog/post/24627330-arch-linux-%E5%AE%89%E8%A3%9D-openssh (Traditional Chinese)

## Xorg over SSH 
**NOT RECOMMEND** as the performance is generally worse and there are many UI bugs when running over SSH
- In SSH sessions, run GUI applications with `&` at the end to start the application in another process, for example: `firefox &`

- enable the following settings in `/etc/ssh/sshd_config`:
```
X11Forwarding yes
X11DisplayOffset 10
X11UseLocalhost yes
```
### Windows

1. You need a X Server such as `VcXsrv`
2. Use `putty` to connect to the guest machine, with the following setting enabled:
  - Connection -> SSH -> X11 -> Enable X11 forwarding
  - X display location: `localhost:0`
3. If `$DISPLAY = localhost:10.0` after you login via ssh, then you are all set.
```
$  echo $DISPLAY
localhost:10.0
```
- Other notes:
  - If you want to use ssh from msys2 or Bash on Windows in windows 10, refer to below. (I believe you still need to run VcXsrv as X Server)
  - The change IME short cut for `fcitx` may conflict with Windows host

### Unix based OS (not tested )
```bash
export DISPLAY=0.0.0.0:0
ssh -X fsworld009@0.0.0.0 -p 2222
firefox &
```

- http://cypresslin.web.fc2.com/Memo/M-SSH.html
- https://askubuntu.com/questions/432255/what-is-display-environment-variable


## Pacman and AUR packages
Refer to my other article.

## Enable Hibernate
**DO NOT USE** systemd hibernate when running as a VM guest, use VirtualBox host's save state feature instead. Otherwise guest addon will break on resume and it can only be fixed by full reboot.

1. Edit `/etc/default/grub`: add resume kernel parameter
    - have seen many reports on /dev/sdx not working, but I didn't try.
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet resume=UUID=swap_uuid"
```
2. `grub-mkconfig -o /boot/grub/grub.cfg`
3. Edit `/etc/mkinitcpio.conf`: add `resume` to HOOKS option
```
HOOKS=(base udev autodetect modconf block filesystems keyboard fsck resume)
```
4. `sudo mkinitcpio -p linux`
5. `xfce4-power-manager-settings`: change sleep mode to hibernate

Other refs:
- https://wiki.archlinux.org/index.php/kernel_parameters
- https://wiki.archlinux.org/index.php/Power_management#Sleep_hooks
- https://wiki.archlinux.org/index.php/Power_management/Suspend_and_hibernate#Configure_the_initramfs

