title: Package Managers for Windows and Mac
date: 2018-06-18 11:16:48
tags: [Windows, Mac]
---

## Cheetsheet

Usage | Choco | Brew
------|-------|-------
Upgrade manager  | choco upgrade chocolatey | brew update
List installed packages | choco list --local-only | brew list
Search package | choco search pkg --by-id-only | brew search pkg1
Install package | choco install pkg1 pkg2 | brew install pkg1 pkg2
Check outdated packages | choco outdated | brew outdated
Update all packages | choco upgrade all | brew upgrade
Update specific packages | choco upgrade pkg1 pkg2 | brew upgrade pkg1 pkg2
<!--more-->

## Windows
- [Chocolatey](https://chocolatey.org/)
- Automatically agree licenses and agreements for one command
  - add `-y` flag
- Automatically agree licenses and agreements globally
  - `choco feature enable -n allowGlobalConfirmation`
- `choco update` is deprecated

## Mac
- [Homebrew](https://brew.sh/)
- [homebrew-cask-fonts](https://github.com/Homebrew/homebrew-cask-fonts) for installing fonts
- Add `-v` to have verbose output (sp that you know it is not crashed)


