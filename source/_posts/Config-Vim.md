---
title: Config Vim
top: false
cover: false
toc: true
mathjax: true
date: 2022-01-20 10:24:34
password:
summary:
description: Vim Configuration
categories:
  - Programming
  - Vim
tags:
  - Vim
---

# 安装 anaconda

本来安装 anaconda 应该问题不大，但是却发现了一个问题。
`./Anaconda3-2021.11-Linux-x86_64.sh: 489: [[: Exec format error` 这个错，是因为
直用./Anaconda.sh 启动安装默认用的是 sh 改成了 bash Anaconda.sh 就不会报错了.

# 一些碎的 Linux 知识

## 查找包的安装位置

一些软件用 apt install 了，但是不知道安装位置在哪里 `dpkg -L xxx`显示包的安装位
置．

## 编译错误定位

编译的时候,打印一些 log 但是并不详细一般会有更加详细的编译 log 文件，在里面可以
更精确的定位错误发生的原因．

## linux 的环境变量可能是两个

`LIBRARY_PATH`编译的时候用到的库的搜索路径 `LD_LIBRARY_PATH`程序加载时库的搜索路
径

## 遇到一个问题明明再搜索路径下有动态库，却报错所无法找到

okular: error while loading shared libraries: libQt5Core.so.5: cannot open
shared object file: No such file or directory 用 strip 处理完之后解决 sudo strip
--remove-section=.note.ABI-tag /usr/lib/x86_64-linux-gnu/libQt5Core.so.5

# 编译 vim

apt 安装的 vim 不能随意的控制 vim 的特性，比如在 latex 反向搜索的时候需要的
+clietservice。 apt-cache search libc-dev ln -s libXtst.so.6 libXtst.so 综合两篇
博客。 https://www.jianshu.com/p/aa5ea81bbc72
https://toutiao.io/posts/runvgs/preview 尽量多的保留特性，最终得到的 config 命令
是

```python
./configure --with-features=huge \
    --enable-multibyte \
    --enable-rubyinterp=yes \
    --enable-python3interp=yes \
    --enable-perlinterp=yes \
    --enable-cscope \
    --enable-fontset \
    --enable-largefile \
    --enable-fail-if-missing \
    --prefix=/path-to-install
```

`--enable-fail-if-missing` 用于显示错误信息。

## 有点搞笑的是编译了很久没有成功，最后 apt 安装解决了

编译的时候 feature 用了 hug，但是还是没有增加+clientserver。我估计是缺少库。查
src/auto/cofig.log 也没有发现很相关的信息。最后`sudo apt-get install vim-gtk`成
功安装了+clientserver 的 vim。

# Why lua instead of vimL(vim script)

## Introduction

Neovim has an embedded lua 5.1 runtime which is used to create faster and more
powerful extentions of you favorite efitor.

1. VimL is a slow interpreted language with almost no optimizations. Much of the
   time spent in vim startup and in actions from plugins that can block the main
   loop in the editor is in parsing and executing vim script.

## How to use lua in command line or vimL file

see `:h lua`.

- From the vim command line, you can run `lua <you code>`. This is useful for
  keybindings, commands, and other one-off execution cases.
- Inside of a vimL file, you can demarcate lua code with the follow code
  fencing:

```vim
lua << EOF
-- your lua code
EOF
```

- Inside of a vimL file you can use the `lua` keyword to execute commands
  similar to the first example,i.e., `lua <your code>`.

One important note here is that Neovim will look for lua code in the
`runtimepath` you've set in your settings. Additionally, it will append your
runtimepath with `/lua/?.lua` and `lua/?/init.lua` so it is common practice to
see a `/lua` sub-directory inside `nvim`. For more detailed information about
where Neovim looks for lua code, check out `:h lua-require`.

[More Information](https://github.com/nanotee/nvim-lua-guide)

# A Practice for `Neovim-from-scratch`

## Install

1. Plugins are defined in `~/.config/nvim/lua/user/plugins.lua`. The plugins
   will fail to be installed due to the internet of China Mainland. Thus, git is
   asked to be configured with ssh. Then the path of plugins can be changed by
   add `git@github.com:` before the original path.
2. `treesitter` is a plugin for syntax-hight. It will install a series of
   language parsers. They are defined in
   `~/.config/nvim/lua/user/treesitter.lua`. By setting
   `ensure_installed = {'astro', 'xxx'}`, we can define the parsers to be
   download. Also, download errors may be occurred due to the internet. The path
   of these language parsers are defined in
   `~/.local/share/nvim/site/pack/packer/start/nvim-treesitter/lua/nvim-treesitter/parsers.lua`,
   which may be changed by download with ssh.

## Analysis

1. There are 21 files required in `init.lua`. They are exactly the 21 files in
   `lua/user` folder. About 34 plugins are defined in `plugins.lua`. The lua
   files are all the configuration file of the plugins defined in `plugins.lua`,
   except for `keymaps.lua`, `option.lua`, `autocommand.lua`. `plugins.lua`
   itself is the configuration file of `packer.nvim`, a plugin manager.
2. Here gives a brief look at the plugins.

   1. `packer.nvim` is a plugin manager.
   2. `Alpha-vim` is a plugin used by Neovim-from-scratch. It allow one to
      custom the greeter for neovim
   3. `popup.nvim`, An implementation of the Popup API from vim in neovim.
   4. `plenary.nvim`, implement some useful window management items for neovim.
   5. `nvim-autopais` A super powerful auto pair plugin for neovim that supports
      multiple characters.
   6. `Comment.nvim` Smart and Powerful commenting plugin for neovim.
   7. `nvim-web-devicons` A lua fork of `vim-devicons`. This plugin provides the
      same icons as well as colors for each icon
   8. `nvim-tree-lua` A file explorer for neovim written in lua.
   9. `bufferline.nvim` A snazzy buffer line (with tabpage integration) for
      neovim built using lua.
   10. `vim-bbye` Bbye allows you to do delete buffers (close files) without
       colosing you windows or messing up your layout.
   11. `lualine.nvim` A blazing fast and easy to configure neovim status line
       written in lua.
   12. `toggleterm.nvim` A neovim plugin to persist and toggle multiple
       terminals during an editing session.
   13. `project.nvim` an all in one neovim plugin written in lua that provides
       superior project management.
   14. `impatient.nvim` speed up loading lua modules in neovim to improve start
       up time.
   15. `indent-blankline.nvim` adds indentation guides to all lines (including
       empty lines.)
   16. `FixCursorHold.nvim` This is needed to fix lsp doc highlight.
   17. `which-key.nvim` a lua plugin that displays a popup with possible key
       bindings of the command you started typing.
   18. `darkplus.nvim` color themes
   19. `nvim-cmp` The completion plugin
   20. `cmp-buffer` buffer completions
   21. `cmp-path` path completions
   22. `cmp-cmdline` cmdline completions
   23. `cmp_luasnip` snippet completions.
   24. `cmp-nvim-lsp` nvim-cmp source for neovim's built-in language server
       client.
   25. `LuaSnip` snippet engin
   26. `friendly-snippets` a bunch of snippets to use
   27. `nvim-lspconfig` A collection of common configurations for neovim's
       built-in language server clinet. Features: go-to-definition,
       find-references, hover, completion, rename, format, refactor.
   28. `nvim-lsp-installer` allows you to seamlessly install LSP server
       locally(inside `:echo stdpaht("data")`)
   29. `nlsp-settings.nvim` A plugin to configure LSP using json/yaml files like
       `coc-setting.json`
   30. `null-ls.nvim` for formatters and linters, use neovim as a language
       server to inject LSP diagnostic, code actions, and more via lua
   31. `telescope.nvim` a highly extendable fuzzy finder over lists.
   32. `nvim-treesitter` both provide a simple and easy way to use the interface
       for `tree-sitter` in neovim and to provide some basic functionality such
       as highlighting based on it.
   33. `nvim-ts-context-commentstring"` setting `comment string` option based on
       the cursor location in the file. The location is checked via treesitter
       queries.
   34. `gitsigns.nvim` Git

3. The plugins are installed in `~/.local/share/nvim/site/pack/packer/start`,
   which is defined in the `install_path` in `plugins.lua`.
