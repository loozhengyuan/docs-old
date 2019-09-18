# Installing Oh My Zsh on MacOS

`zsh` is another flavour of shell, a common alternative to `bash` which is found in most UNIX systems. They are already available on MacOS, and will be the default shell from MacOS Catalina onwards.

## Prerequisites

* oh-my-zsh
* Powerline fonts

## Step 1: Setup oh-my-zsh

Firstly, run the one-liner command to install oh-my-zsh.

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

_NOTE: When asked whether to set zsh to the default shell, set to `Y`_

Though not necessary, you may default to the more command `agnoster` theme by editing the `~/.zshrc` file.

```bash
nano ~/.zshrc
```

```bash
# ~/.zshrc
...
ZSH_THEME="agnoster"
...
```

## WIP

