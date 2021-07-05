---
layout: post
title:  "install-python-prog-env-and-config-vscode"
date:   2021-07-05 8:47:00 +0800
categories: [Python vscode]
tags: [Python, vscode]
---

# windows 中安装 Python 以及配置 vscode 的 Python 环境

## 1.安装

- 去 Python 官网和 vscode 官网下载安装包，并安装

- 检查 Python 是否安装完成

  - 打开 cmd 输入 python --version
  - 打开 cmd 输入 pip list

- 安装 Python 拼写检查工具 flake8 和 Python格式化工具 yapf

  - 打开 cmd 输入 

    ```powershell
    pip install flake8
    pip install yapf
    ```

- 将 Python 脚本工具目录添加至环境变量

## 2.配置 vscode

- 安装 vscode 官方 Python 插件
  - 打开VScode，按下快捷键Ctrl+Shift+X，进入插件管理页面。
  - 在搜索栏输入python。
  - 选择插件，点击安装。
- 在 vscode 中选择 flake8 和 yapf
  - 按“Ctrl + ，”进入设置。
  - 搜索“python.linting.flake8Enabled”设置项并勾选，在右下角弹出窗口中选择 flake8
  - 搜索“python.formatting.provider”设置项并选择“yapf”。
- 只按一次 F5 调试 Python 源文件
  - 点击左侧的 debug 按钮
  - 点击 create a launch.json file 字样，在弹出的选项中选择 Python，这时会在项目目录建立一个 .vscode/launch.json 文件
  - 在 .vscode/launch.json 文件中添加 【"stopOnEntry": false,】 字段，这时只按一次 F5 就可以快速调试 Python 程序了
- 格式化代码
  - 在想要格式化的文件中按 "Alt + Shift + f"



至此就可以高效的写 Python 代码了
