Use [Vundle](https://github.com/gmarik/vundle) to manage your extensions.

The following Bundles might be useful:

```vim
" Bundles (make sure vundle manages vundle)
Bundle 'gmarik/vundle'
" Color scheme
Bundle 'altercation/vim-colors-solarized'
" Git integration
Bundle 'tpope/vim-fugitive'
" File navigation
Bundle 'scrooloose/nerdtree'
" Syntax fixes
Bundle 'scrooloose/syntastic'
" Less (CSS) highlighting
Bundle 'lunaru/vim-less'
" Better searching
Bundle 'mileszs/ack.vim'
" Quick-jump to files
Bundle 'git://git.wincent.com/command-t.git'
" JS linting/hinting
Bundle 'walm/jshint.vim'
" Good autocomplete (see installation instructions below)
Bundle 'Valloric/YouCompleteMe'
" Syntax highlighting for puppet
Bundle 'rodjek/vim-puppet'
```

Go-specific plugins and configurations can be found at [[ Vim with Go ]]

To install YCM (autocomplete):
```bash
git clone https://github.com/Valloric/YouCompleteMe.git ~/.vim/bundle/YouCompleteMe
cd ~/.vim/bundle/YouCompleteMe
git submodule update --init --recursive
./install.sh --clang-completer --gocode-completer --omnisharp-completer
vim +BundleInstall +qall
```

If you're using `ctags`, [this configuration](https://gist.github.com/visualmotive/7916585) for your git is likely useful to automatically update ctags on every merge/rebase/commit/etc. 

### Line length

To make vim help you handle our 100 character line length rule, add this to your `.vimrc`:

```vim
" set where text will be wrapped
set textwidth=100
" highlight the 100th column, making it easy to see
set colorcolumn=100
```

Once you have `textwidth` set, you can use the `gq` operation on some amount of text (e.g. `gq$` or `gq}`) to wrap that text to the correct width. It will do cool things like join short lines together so that all lines (except the last) are the long as possible without going over 100 characters.

Some other options you might find useful when dealing with long lines:

```vim
set wrap
" q: allow formatting of comments with "gq"
" r: automatically insert the current comment leader after hitting <enter>
" n: recognize numbered lists when formatting text
" 1: don't break a line after a one-letter word
" j: remove the comment leader when joining lines
set formatoptions=qrn1j
set linebreak
```