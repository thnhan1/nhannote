# Install zsh for git bash window
Instruction to install zsh for git bash in Window Terminal.

## Install ZSH

Once Bash for Windows is set up, it is time to install ZSH:  
Download the latest MSYS2 zsh package from the MSYS2 package repository. The file will be named  
like: `zsh-#.#-#-x86_64.pkg.tar.zst`. You can find the latest version [here](https://packages.msys2.org/package/zsh).

### 1. Install zsh
Extract the contents of the zip file (which will include `etc` and `usr` directories) into your `Git  
Bash`  
installation directory. This will be in `C:\Program Files\Git`.
### 2. Verify installation
`zsh --version`

### 3. Set ZSH as the Default Shell

After installation, set ZSH as your default shell by appending the following to  
your  `C:\Users\<your user name>\.bashrc`  file:
```bash
if [ -t 1 ]; then
    exec zsh
fi
```
### 4. Fix Displaying of Unexpected Characters

Fix the displaying of garbled characters on terminal by appending this line in  
your  `C:\Users\<your user name>\.bashrc`  file:
```bash
/c/Windows/System32/chcp.com 65001 > /dev/null 2>&1
```
### 5. Install oh-my-zsh

```bash
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```
### 6. Install Nerd font
Choose a nerd font you like and set for terminal.
You can also install theme link powerlevel10k. But I live bare one.

## Install plugin for zsh
### zsh-auto-suggestions
```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

### zsh-syntax-highlighting

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```


### Add plugin
```bash
plugins( zsh-syntax-highlighting zsh-autosuggestion )
```

