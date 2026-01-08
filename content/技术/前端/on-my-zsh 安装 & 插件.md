---
draft: false
create_data: 2026-01-08 16:37
---
# Install

```jsx
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

// or gitee mirror

sh -c "$(curl -fsSL https://gitee.com/mirrors/oh-my-zsh/raw/master/tools/install.sh)"
```

## Theme

1. open `.zshrc`

```jsx
vim ~/.zshrc
```

1. change `ZSH_THEME`
    1. change to "ys" 
2. [more theme name](https://link.zhihu.com/?target=https%3A//github.com/robbyrussell/oh-my-zsh/wiki/Themes)

## Plugins

## git

内置，.zshrc 中可以看到已经配置

## zsh-syntax-highlighting

安装

```jsx
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

在 zsh 中添加插件

```jsx
# .zshrc

plugins=(git zsh-autosuggestions)
```

使新配置生效

```jsx
source ~/.zshrc
```

## zsh-syntax-highlighting

安装

```jsx
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

添加插件

```jsx
# .zshrc

plugins=(git zsh-syntax-highlighting)
```

生效

```jsx
source ~/.zshrc
```