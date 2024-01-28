# vim

## ~/.vimrc

    set encoding=utf-8
    set nocompatible
    set backspace=2
    filetype indent on
    filetype plugin on
    set autoread

    filetype off

    set rtp+=$HOME/.vim/bundle/Vundle.vim
    call vundle#begin()
    Plugin 'tpope/vim-fugitive'
    Plugin 'ascenator/L9', {'name': 'newL9'}
    Plugin 'VundleVim/Vundle.vim'
    Plugin 'flazz/vim-colorschemes'
    Plugin 'jelera/vim-javascript-syntax'
    Plugin 'pangloss/vim-javascript'
    Plugin 'nathanaelkane/vim-indent-guides'
    Plugin 'mattn/emmet-vim'
    Plugin 'heavenshell/vim-jsdoc'
    ""Plugin 'wesleyche/SrcExpl'
    ""Plugin 'wesleyche/Trinity'
    ""Plugin 'taglist.vim'
    call vundle#end()

    ""color
    colorscheme molokai-dark
    syntax enable
    set background=dark
    "  TabLineFill  tab pages line, where there are no labels
    hi TabLineFill term=none
    
    hi TabLineFill ctermfg=DarkGrey
    hi TabLineFill guifg=#777777
    "  TabLineSel   tab pages line, active tab page label
    hi TabLineSel term=inverse
    hi TabLineSel cterm=none ctermfg=199 ctermbg=Black
    hi TabLineSel gui=none guifg=#F92672 guibg=Black
    ""space tabs
    set tabstop=4
    set shiftwidth=4
    set expandtab
    set encoding=utf8

    "" UI
    set so=7
    set number
    set showcmd
    set ruler
    set cursorline
    set cursorcolumn
    set wildmenu
    set lazyredraw
    set showmatch

    ""fold
    set foldenable
    set foldmethod=manual
    set foldlevelstart=10

    ""search
    set hlsearch
    set incsearch
    set showmatch
    set mat=2


    set autoindent
    set smartindent

    set t_Co=256
    ""set nowrap

    ""shortcut
    nmap <silent> <C-l> <Plug>(jsdoc)
    ""nmap <F7> :TlistToggle<CR>
    ""nmap <F8>  :TrinityToggleAll<CR>
    ""nmap <F9>  :TrinityToggleSourceExplorer<CR>
    ""nmap <F10> :TrinityToggleTagList<CR>
    ""nmap <F11> :TrinityToggleNERDTree<CR>

    autocmd BufNewFile,BufRead *.md set filetype=markdown
    
    set guifont=Cascadia\ Mono\ PL:h10

## colors

[molokai-dark.vim](https://github.com/pR0Ps/molokai-dark/blob/master/colors/molokai-dark.vim)

## syntax

[cpp.vim](https://github.com/octol/vim-cpp-enhanced-highlight/tree/master/after/syntax/cpp.vim)

## font

[CascadiaCode](https://github.com/microsoft/cascadia-code/releases/download/v2102.03/CascadiaCode-2102.03.zip)
