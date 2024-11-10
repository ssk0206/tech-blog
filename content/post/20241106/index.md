---
title: zsh から fish に乗り換えた
description: 前々から気になっていたfishに乗り換えてみました。その際に使用した、初期設定のコマンド集です。
slug: 20241106
date: 2024-11-06 00:00:00+0000
categories:
  - コマンド集
tags:
  - fish
  - 開発環境
weight: 1 # You can add weight to some posts to override the default sorting (date descending)
---

### 初期設定

```bash
$ brew install fish
$ which fish
/opt/homebrew/bin/fish
$ sudo vim /etc/shells
```

/etc/shells

```bash
/bin/bash
/bin/csh
/bin/dash
/bin/ksh
/bin/sh
/bin/tcsh
/bin/zsh
/opt/homebrew/bin/fish
```

```bash
$ chsh -s /opt/homebrew/bin/fish
```

ターミナル再起動

### Homebrew インストール

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

`~/.config/fish/config.fish`  に、PATH を通すためのコマンドを追加
fish 起動時に実行されるもの。

config.fish

```bash
if status is-interactive
    eval (/opt/homebrew/bin/brew shellenv)
end
```

ターミナルを再起動

### Starship インストール

```bash
$ brew install starship
```

`starship init fish | source` を `~/.config/fish/config.fish`に追加

### fisher インストール

```bash
curl -sL https://git.io/fisher | source && fisher install jorgebucaran/fisher
```

#### color theme 変更

https://github.com/Jomik/fish-gruvbox

```bash
fisher install jomik/fish-gruvbox
```

`theme_gruvbox dark hard` を `~/.config/fish/config.fish`に追加

#### VSCode の shell を fish に変更

setting.json

```json
{
  "terminal.integrated.profiles.osx": {
    "fish": {
      "path": "/opt/homebrew/bin/fish",
      "args": ["-l"]
    }
  },
  "terminal.integrated.defaultProfile.osx": "fish"
}
```

#### lazyvim

https://www.lazyvim.org/installation

最終的な `~/.config/fish/config.fish`

```bash
if status is-interactive
    eval (/opt/homebrew/bin/brew shellenv)
end

starship init fish | source

theme_gruvbox dark hard

alias vim="nvim"
```
