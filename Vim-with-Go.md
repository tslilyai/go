
If you're using Go, add these plugins to your `.vimrc` where you put your vundle plugins:
```vim
" Syntax highlighting, gofmt on save, linting, and more
Plugin 'fatih/vim-go'
" Works with vim-go, shows gotags of the source code in a sidebar
Plugin 'majutsushi/tagbar'
" Compiles Go in the background, reports errors to the QuickFix window
Plugin 'rjohnsondev/vim-compiler-go'
" Adds godef support to vim
Plugin 'dgryski/vim-godef'
```
**Read the notes for `vim-go` [here](https://github.com/fatih/vim-go/wiki/Usage) to see all the cmds for tools like oracle**

You can add the following to your `.vimrc` to be able to toggle the tagbar on and off:
```vim
" Map Tagbar for Go to F8
nmap <F8> :TagbarToggle<CR
```
To enable gofmt (and goimport) on save, add the following:
```vim
let g:go_fmt_autosave=1
let g:go_fmt_command = "goimports"
```

