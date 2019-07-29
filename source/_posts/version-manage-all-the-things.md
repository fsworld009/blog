title: Version manage all the things
date: 2019-07-29 22:37:41
tags: [Nodejs, Ruby, python]
---

Recently I've re-installed Nodejs, python, and Ruby on my work machine.
When installing Ruby via Homebrew I was having trouble using it after
installation, then my collegue figured out that `brew info ruby` outputs
this message:

```
By default, binaries installed by gem will be placed into:
  /usr/local/lib/ruby/gems/2.6.0/bin

You may want to add this to your PATH.

ruby is keg-only, which means it was not symlinked into /usr/local,
because macOS already provides this software and installing another version in
parallel can cause all kinds of trouble.

If you need to have ruby first in your PATH run:
  echo 'export PATH="/usr/local/opt/ruby/bin:$PATH"' >> ~/.zshrc

For compilers to find ruby you may need to set:
  export LDFLAGS="-L/usr/local/opt/ruby/lib"
  export CPPFLAGS="-I/usr/local/opt/ruby/include"
```

At this point I think it's probably just better avoid installing Ruby via
homebrew. I searched a bit and decided to go with version managers like nvm.

This article is a quick note on what I ended up using for each language
and how did I installed them.

<!--more-->
## Ruby
rbenv https://github.com/rbenv/rbenv.git
- I prefer installation with git https://github.com/rbenv/rbenv#basic-github-checkout

```bash
$ git clone https://github.com/rbenv/rbenv.git ~/.rbenv
# use a stable release instead of master
$ cd ~/.rbenv
$ git checkout {version_tag}

# install ruby-build which is required for installing anything
$ mkdir -p "$(rbenv root)"/plugins
$ git clone https://github.com/rbenv/ruby-build.git "$(rbenv root)"/plugins/ruby-build
$ cd "$(rbenv root)"/plugins/ruby-build
# use a stable release instead of master
$ git checkout {version_tag}

# update .bashrc, or manually edit your ~/.bashrc, ~/.bash_profile
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile

# install ruby and gem
rbenv install 2.6.3
# use this version globally
rbenv global 2.6.3
# install desired gem packages
gem install sass
gem install compass
```

I'd like to make my `.bashrc` more portable so I added some
checks to see if rbenv is installed before executing `rbenv init`:

```bash
# http://stackoverflow.com/questions/592620/check-if-a-program-exists-from-a-bash-script/3931779#3931779
command_exists () {
    type "$1" &> /dev/null ;
}

if [ -d "$HOME/.rbenv/" ]; then
  export PATH="$HOME/.rbenv/bin:$PATH"
  if command_exists rbenv; then
    eval "$(rbenv init -)"
  fi
fi
```

## python
pyenv https://github.com/pyenv/pyenv
- I prefer installation with git https://github.com/pyenv/pyenv#basic-github-checkout

The step is pretty much similar to rbenv in general
```bash
$ git clone https://github.com/pyenv/pyenv.git ~/.pyenv
# use a stable release instead of master
$ cd ~/.pyenv
$ git checkout {version_tag}


# update .bashrc, or manually edit your ~/.bashrc, ~/.bash_profile
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bash_profile
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bash_profile

# install ruby and gem
pyenv install 3.7.3
# use this version globally
pyenv global 3.7.3
# install desired pip packages
pip install pipenv
```

Similarly, I also added some checks before doing `pyenv init`, so that
it won't break if I'm in an environment without pyenv:
```bash
if [ -d "$HOME/.pyenv/" ]; then
  export PYENV_ROOT="$HOME/.pyenv"
  export PATH="$PYENV_ROOT/bin:$PATH"
  if command_exists pyenv; then
    eval "$(pyenv init -)"
  fi
fi
```



## Node.js
nvm https://github.com/nvm-sh/nvm
- I prefer installation with git https://github.com/nvm-sh/nvm#git-install

```bash
$ git clone https://github.com/nvm-sh/nvm.git ~/.nvm
# use a stable release instead of master
$ cd ~/.nvm
$ git checkout {version_tag}

# update .bashrc, or manually edit your ~/.bashrc, ~/.bash_profile
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion

# install latest nodejs v10 stable
$ nvm install 10
$ nvm use 10
# set default version to v10
$ nvm alias default 10

# install npm packages
$ npm install
```

Again, making `.bashrc` more general by checking file existence beforehand:
```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
```

- Note: to use nvm in fish shell, install [fisher](https://github.com/jorgebucaran/fisher)
and do `fisher add FabioAntunes/fish-nvm`