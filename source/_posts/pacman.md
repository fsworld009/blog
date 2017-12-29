title: Pacman package manager cheatsheet
date: 2017-12-29 17:21:33
tags: [Archlinux, linux]
---
Pacman notes
<!--more-->

Usage | Command
------|--------
Package upgrade | pacman -Syu
Sync package DB | pacman -Sy
Search package | pacman -Ss package_name
Install package | pacman -S package_name
Remove package and unused dependencies | pacman -Rs package_name

- Always do `pacman -Syu` before you install packages

## Tips and notes
- https://wiki.archlinux.org/index.php/Pacman/Tips_and_tricks

### Unlnown public key error
```bash
pacman -S libmng
libmng-2.0.3.tar.xz ... FAILED (unknown public key F54984BFA16C640F)
```
- `gpg --recv-keys F54984BFA16C640F`.
- https://wiki.archlinux.org/index.php/Makepkg#Signature_checking

### lib32 packages
- Required by some 32bit applications, or when you want to build 32bit applications from gcc.
- **NOT RECOMMAND** unless required by other packages, see below error note
- uncomment the following in `/etc/pacman.conf`:
```
[multilib]
Include = /etc/pacman.d/mirrorlist
```
- `gcc-multilib` packages will replace gcc packages when installed. (I don't think they can coexist)

#### breaks dependency error when doing pacman installs
```bash
$  sudo pacman -Syu
:: Synchronizing package databases...
...
error: failed to prepare transaction (could not satisfy dependencies)
:: gcc-libs-multilib: installing lib32-gcc-libs (7.2.1-2) breaks dependency 'lib32-gcc-libs=7.2.0-3'
```
- At the time of writing, this is because gcc has upgraded to 7.2.1 while gcc-multilib is still on 7.2.0-3. Since I'm not using gcc-multilib anymore I just revert back to gcc by doing `pacman -S gcc`
- https://forum.manjaro.org/t/cannot-apply-the-latest-update-due-to-dependency-conflict/25283/9

### Cleanup
- `sudo pacman -Rns $(pacman -Qtdq)`
  - https://wiki.archlinux.org/index.php/System_maintenance#Clean_the_filesystem

- Use `makepkg -RdLf` for a "re-package". Re-packaging is useful when the process failed in package() and you don't want to run the long build part again.

### 3rd party AUR package manager
- [pkgbuilder](https://github.com/Kwpolska/pkgbuilder)
1. add the following to `/etc/pacman.conf`
```
[pkgbuilder]
Server = https://pkgbuilder-repo.chriswarrick.com/
```
2. Run
```bash
pacman-key -r 5EAAEA16
pacman-key --lsign 5EAAEA16
pacman -Syyu
```

3. basically the usage is the same as `pacman`, for example `pkgbuilder -S package_name` and `pkgbuilder -Syu`

### Build AUR packages manually
```bash
pkgbuilder -F {package_name}
nvim PKGBUILD
makepkg -sCLf
sudo pacman -U *.pkg.tar.xz
```
- Or download PKGBUILD file manually from AUR pages if you don't have AUR package managers.