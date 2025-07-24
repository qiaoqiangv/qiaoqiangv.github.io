---
layout: post
title:  "Markdown Preview plugin for Nvim"
date:   2025-07-24 15:30:00 +0800
categories: [Article]
tags: [nvim, plugin, markdown]
---

1. 在 ~/.nvimrc 中添加如下内容：

    $ nvim .nvimrc
    ```
    " normal/insert
    <Plug>MarkdownPreview
    <Plug>MarkdownPreviewStop
    <Plug>MarkdownPreviewToggle

    " example
    nmap <C-s> <Plug>MarkdownPreview
    nmap <M-s> <Plug>MarkdownPreviewStop
    nmap <C-p> <Plug>MarkdownPreviewToggle

    function OpenMarkdownPreview (url)
      execute "silent ! firefox --new-window " . a:url
    endfunction
    let g:mkdp_browserfunc = 'OpenMarkdownPreview'
    ```
2. 在 .config/nvim/init.lua 文件中添加如下内容：

    $ nvim .config/nvim/init.lua
    ```
    require('plugins')
    ```
3. 在 .config/nvim/lua/plugins.lua 中添加如下内容：

    $ nvim .config/nvim/lua/plugins.lua
    ```

    -- This file can be loaded by calling `lua require('plugins')` from your init.vim

    -- Only required if you have packer configured as `opt`
    vim.cmd [[packadd packer.nvim]]

    return require('packer').startup(function(use)
      -- Packer can manage itself
      use 'wbthomason/packer.nvim'

      -- Simple plugins can be specified as strings
      use 'rstacruz/vim-closer'

      -- Lazy loading:
      -- Load on specific commands
      use {'tpope/vim-dispatch', opt = true, cmd = {'Dispatch', 'Make', 'Focus', 'Start'}}

      -- Load on an autocommand event
      use {'andymass/vim-matchup', event = 'VimEnter'}

      -- Load on a combination of conditions: specific filetypes or commands
      -- Also run code after load (see the "config" key)
      use {
        'w0rp/ale',
        ft = {'sh', 'zsh', 'bash', 'c', 'cpp', 'cmake', 'html', 'markdown', 'racket', 'vim', 'tex'},
        cmd = 'ALEEnable',
        config = 'vim.cmd[[ALEEnable]]'
      }

      -- Plugins can have dependencies on other plugins
      use {
        'haorenW1025/completion-nvim',
        opt = true,
        requires = {{'hrsh7th/vim-vsnip', opt = true}, {'hrsh7th/vim-vsnip-integ', opt = true}}
      }

      -- Plugins can also depend on rocks from luarocks.org:
    --  use {
    --    'my/supercoolplugin',
    --    rocks = {'lpeg', {'lua-cjson', version = '2.1.0'}}
    --  }

      -- You can specify rocks in isolation
      use_rocks 'penlight'
      use_rocks {'lua-resty-http', 'lpeg'}

      -- Local plugins can be included
      -- use '~/projects/personal/hover.nvim'

      -- Plugins can have post-install/update hooks
      use {'iamcco/markdown-preview.nvim', run = 'cd app && yarn install', cmd = 'MarkdownPreview'}

      -- Post-install/update hook with neovim command
      -- use { 'nvim-treesitter/nvim-treesitter', run = ':TSUpdate' }

      -- Post-install/update hook with call of vimscript function with argument
      use { 'glacambre/firenvim', run = function() vim.fn['firenvim#install'](0) end }

      -- Use specific branch, dependency and run lua file after load
      use {
        'glepnir/galaxyline.nvim', branch = 'main', config = function() require'statusline' end,
        requires = {'kyazdani42/nvim-web-devicons'}
      }

      -- Use dependency and run lua function after load
      use {
        'lewis6991/gitsigns.nvim', requires = { 'nvim-lua/plenary.nvim' },
        config = function() require('gitsigns').setup() end
      }

      -- You can specify multiple plugins in a single call
      use {'tjdevries/colorbuddy.vim', {'nvim-treesitter/nvim-treesitter', opt = true}}

      -- You can alias plugin names
      use {'dracula/vim', as = 'dracula'}
    end)
    ```
4. 打开 nvim 执行 :PackerInstall

5. 查看 .local/share/nvim/site/pack/packer/start/markdown-preview.nvim/ 目录，看看 markdown-preview.nvim 插件有没有正常安装
    $ ls .local/share/nvim/site/pack/packer/start/markdown-preview.nvim

6. 如果 5. 目录下有插件的文件，执行如下操作，检查插件是否正确运行：

    1. 使用 nvim 打开一个 .md 的文件： $ nvim test.md
    2. 在 nvim 中输入内置命令： :MarkdownPreview 回车，如果浏览器正常跳出来，就是成功了

[end]
