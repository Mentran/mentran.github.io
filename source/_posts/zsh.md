title: zsh
author: Mentran
tags:
  - zsh
  - shell
  - 配置
categories: []
date: 2019-07-31 15:33:00
---
## zsh 环境准备
1. 查看当前 `shell` 环境
    ```bash
    echo $SHELL         #查看当前shell环境
    cat /etc/shells     # 查看是否已安装zsh
    ```
2.  安装zsh
    ```bash
    sudo apt-get install zsh    # ubuntu
    brew install zsh            # mac
    ```
3. 切换shell环境
    ```bash
    chsh -s /usr/bin/zsh
    ```
4. 下载安装 `oh-my-zsh`
    ```bash
    # wget
    sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
    
    # curl
    sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
    ```
<!-- more -->
***

## 配置 zsh 主题和插件
1. 下载 命令高亮 插件 `zsh-syntax-highlighting` 到 `zsh` 插件库中
    ```bash
    git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
    ```

2. 下载 自动提示补全 插件 `zsh-autosuggestions` 到 `zsh` 插件库中
    ```bash
    git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
    ```

3. **修改主题**：打开`~/.zshrc` 文件，将 `ZSH_THEME` 字段改为
    ```bash
    ZSH_THEME="ys"
    ```

4. **使用插件**：打开`~/.zshrc` 文件，将 `plugins` 字段改为
    ```bash
    plugins=(git z zsh-syntax-highlighting zsh-autosuggestions)
    ```

5. **使修改生效**
    ```bash
    source ~/.zshrc
    ```