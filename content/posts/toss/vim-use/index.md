---
title: Vim 使用
date: 2019-12-26T20:52:00+08:00
lastmod: 2020-04-27T00:06:19+08:00
description: ""
featuredImage: ""
categories:
- toss
tags:
- vim
---

## 展示

![VIM](https://cdn.zggsong.cn/2020/04/25/b5b8a995cd6ce.png!webp)

配置文件如下 [github-vim](https://github.com/ZGGSONG/vim)

```vimscript
"    ______        __  _    __     _____ __  __
"   / ___\ \      / / | |   \ \   / /_ _|  \/  |
"   \___ \\ \ /\ / /  | |____\ \ / / | || |\/| |
"    ___) |\ V  V / |_| |_____\ V /  | || |  | |
"   |____/  \_/\_/ \___/       \_/  |___|_|  |_|
"
"=============
"  常规设置
"=============
let mapleader=" "	"让空格键变成 leader 键
syntax on		"代码高亮
set number		"显示行号
set ruler		"打开状态栏标尺
set relativenumber	"以当前行号向上下递加
set cursorline		"强调当前行
set wrap		"字不会超出窗体
set showcmd		"显示 cmd 命令
set wildmenu 		"命令行提示
set hlsearch		"搜索高亮
exec "nohlsearch"		
set incsearch		"边搜边高亮
set ignorecase		"搜索忽略大小写	
set smartcase		"智能大小写搜索
set nocompatible
set clipboard=unnamed   "终端下输入 vim --version \| grep clipboard，如果出现+clipboard 则可以使用---与系统共享剪贴板
filetype on		"识别不同的文件格式
filetype indent on
filetype plugin on
filetype plugin indent on
set mouse=a		"让鼠标有用
set encoding=utf-8	"设置格式 uhf-8
set tabstop=2		"缩进设置
set scrolloff=5 "上下余留 5 行"
set shiftwidth=2
set softtabstop=2
set backspace=indent,eol,start	"让光标可以由下一行退格到上一行末尾
setlocal noswapfile	"不要生成 swap 文件，当 buffer 被丢弃的时候隐藏它
set bufhidden=hide	"同上
set autowrite		"自动保存

"=============
"=自定义快捷键
"=============
noremap = nzz		"检索后查询高亮内容
noremap - Nzz
noremap <LEADER><CR> :nohlsearch<CR> 	"空格加回车取消高亮
noremap W 5w		"大写快速移动
noremap B 5b		"大写快速移动
noremap < <<		"取消缩进
noremap > >>		"缩进
map S :w<CR>			"大写 S 保存
map Q :q<CR>			"大写 Q 退出
map R :source $MYVIMRC<CR>	"重新加载
"向上下左右分屏：hjkl-左下上右
map sl :set splitright<CR>:vsplit 
map sh :set nosplitright<CR>:vsplit 
map sk :set nosplitbelow<CR>:split 
map sj :set splitbelow<CR>:split 
"空格+方向：hjkl 即转向相应分屏
map <LEADER>l <C-w>l
map <LEADER>h <C-w>h
map <LEADER>k <C-w>k
map <LEADER>j <C-w>j
map <LEADER><right> <C-w>l
map <LEADER><left> <C-w>h
map <LEADER><up> <C-w>k
map <LEADER><down> <C-w>j
"ctrl+上下左右调整分屏大小
map <C-up> :res +5<CR>
map <C-down> :res -5<CR>
map <C-left> :vertical resize -5<CR>
map <C-right> :vertical resize +5<CR>
"上下分屏改成左右分屏
map sv <C-w>t<C-w>H
map sn <C-w>t<C-w>K
"新标签：nt + 新文件名
map nt :tabe 
"跳转标签页快捷键大写 N 大写 P
nmap <S-N> :bn<CR>
nmap <S-p> :bp<CR>
"先安装 figlet---最上方艺术字
"map tx :r !figlet 
"placeholder
map <LEADER><LEADER> <ESC>/<++><CR>:nohlsearch<CR>c4l

"==============
"===Plug 插件===
"==============
call plug#begin('~/.vim/plugged')
Plug 'vim-airline/vim-airline'
Plug 'vim-airline/vim-airline-themes'
Plug 'w0ng/vim-hybrid'		"hybrid 主题
Plug 'scrooloose/nerdtree'  "NERDTree
Plug 'ryanoasis/vim-devicons'	"nerd 图标
Plug 'tiagofumo/vim-nerdtree-syntax-highlight'	"nerdtree 高亮
Plug 'luochen1990/rainbow'  "彩虹括号插件
Plug 'jiangmiao/auto-pairs' "自动括号补全
Plug 'junegunn/vim-easy-align'  "符号对齐
Plug 'mattn/emmet-vim'  "HTML 拓展
Plug 'mhinz/vim-startify' "启动页面美化
Plug 'jceb/vim-orgmode'	"vim 版的 emacs org mode
Plug 'junegunn/fzf', { 'do': { -> fzf#install() } }
Plug 'junegunn/fzf.vim'
Plug 'Yggdroot/indentLine'		"缩进线
Plug 'itchyny/vim-cursorword'	"相同的单词高亮
Plug 'neoclide/coc.nvim', {'branch': 'release'}	"coc 代码补全
call plug#end()	

"=============
"Airline 配置
"=============
let g:airline#extensions#tabline#enabled = 1
"设置电力线字体启用
let g:airline_powerline_fonts = 1
"永远显示状态栏
set laststatus=2
"在 windows 中用 xshell 连接打开 vim 可以显示色彩
set t_Co=256     
"设置 airline 主题
let g:airline_theme='bubblegum'
"VIM 主题配置
colorscheme hybrid
set background=dark		" Setting dark mode

"=============
"NERDTree 配置
"=============
"自动打开 NERDTree
"autocmd vimenter * NERDTree
"设置 NERDTree 打开快捷键为 tt
map tt :NERDTreeToggle<CR>
"窗口大小
let NERDTreeWinSize=20
"是否默认显示行号
let NERDTreeShowLineNumbers=0
"是否默认显示隐藏文件
let NERDTreeShowHidden=1
" 显示书签列表
let NERDTreeShowBookmarks=0

"=============
" fzf 配置文件
"=============
" Empty value to disable preview window altogether
let g:fzf_preview_window = ''
" Always enable preview window on the right with 60% width
let g:fzf_preview_window = 'right:60%'
" Ctrl p 显示查找内容
nnoremap <silent> <c-p> :Files <CR>

"==============
" Coc 配置文件
"==============
" TextEdit might fail if hidden is not set.
set hidden
" Some servers have issues with backup files, see #649.
set nobackup
set nowritebackup
" Give more space for displaying messages.
set cmdheight=2
" Having longer updatetime (default is 4000 ms = 4 s) leads to noticeable
" delays and poor user experience.
set updatetime=300
" Don't pass messages to |ins-completion-menu|.
set shortmess+=c
" Always show the signcolumn, otherwise it would shift the text each time
" diagnostics appear/become resolved.
set signcolumn=yes
" Use tab for trigger completion with characters ahead and navigate.
" NOTE: Use command ':verbose imap <tab>' to make sure tab is not mapped by
" other plugin before putting this into your config.
inoremap <silent><expr> <TAB>
      \ pumvisible() ? "\<C-n>" :
      \ <SID>check_back_space() ? "\<TAB>" :
      \ coc#refresh()
inoremap <expr><S-TAB> pumvisible() ? "\<C-p>" : "\<C-h>"

function! s:check_back_space() abort
  let col = col('.') - 1
  return !col || getline('.')[col - 1]  =~# '\s'
endfunction
" Use <c-space> to trigger completion.
inoremap <silent><expr> <c-space> coc#refresh()
" Use <cr> to confirm completion, `<C-g>u` means break undo chain at current
" position. Coc only does snippet and additional edit on confirm.
if exists('*complete_info')
  inoremap <expr> <cr> complete_info()["selected"] != "-1" ? "\<C-y>" : "\<C-g>u\<CR>"
else
  imap <expr> <cr> pumvisible() ? "\<C-y>" : "\<C-g>u\<CR>"
endif
" Use `[g` and `]g` to navigate diagnostics
nmap <silent> [g <Plug>(coc-diagnostic-prev)
nmap <silent> ]g <Plug>(coc-diagnostic-next)
" GoTo code navigation.
nmap <silent> gd <Plug>(coc-definition)
nmap <silent> gy <Plug>(coc-type-definition)
nmap <silent> gi <Plug>(coc-implementation)
nmap <silent> gr <Plug>(coc-references)
" Use K to show documentation in preview window.
nnoremap <silent> K :call <SID>show_documentation()<CR>
function! s:show_documentation()
  if (index(['vim','help'], &filetype) >= 0)
    execute 'h '.expand('<cword>')
  else
    call CocAction('doHover')
  endif
endfunction
" Highlight the symbol and its references when holding the cursor.
autocmd CursorHold * silent call CocActionAsync('highlight')
" Symbol renaming.
nmap <leader>rn <Plug>(coc-rename)
" Formatting selected code.
xmap <leader>f  <Plug>(coc-format-selected)
nmap <leader>f  <Plug>(coc-format-selected)
augroup mygroup
  autocmd!
  " Setup formatexpr specified filetype(s).
  autocmd FileType typescript,json setl formatexpr=CocAction('formatSelected')
  " Update signature help on jump placeholder.
  autocmd User CocJumpPlaceholder call CocActionAsync('showSignatureHelp')
augroup end
" Applying codeAction to the selected region.
" Example: `<leader>aap` for current paragraph
xmap <leader>a  <Plug>(coc-codeaction-selected)
nmap <leader>a  <Plug>(coc-codeaction-selected)
" Remap keys for applying codeAction to the current line.
nmap <leader>ac  <Plug>(coc-codeaction)
" Apply AutoFix to problem on the current line.
nmap <leader>qf  <Plug>(coc-fix-current)
" Introduce function text object
" NOTE: Requires 'textDocument.documentSymbol' support from the language server.
xmap if <Plug>(coc-funcobj-i)
xmap af <Plug>(coc-funcobj-a)
omap if <Plug>(coc-funcobj-i)
omap af <Plug>(coc-funcobj-a)
" Add `:Format` command to format current buffer.
command! -nargs=0 Format :call CocAction('format')
" Add `:Fold` command to fold current buffer.
command! -nargs=? Fold :call     CocAction('fold', <f-args>)
" Add `:OR` command for organize imports of the current buffer.
command! -nargs=0 OR   :call     CocAction('runCommand', 'editor.action.organizeImport')
" Add (Neo)Vim's native statusline support.
" NOTE: Please see `:h coc-status` for integrations with external plugins that
" provide custom statusline: lightline.vim, vim-airline.
set statusline^=%{coc#status()}%{get(b:,'coc_current_function','')}
" Mappings using CoCList:
" Show all diagnostics.
nnoremap <silent> <space>a  :<C-u>CocList diagnostics<cr>
" Manage extensions.
nnoremap <silent> <space>e  :<C-u>CocList extensions<cr>
" Show commands.
nnoremap <silent> <space>c  :<C-u>CocList commands<cr>
" Find symbol of current document.
nnoremap <silent> <space>o  :<C-u>CocList outline<cr>
" Search workspace symbols.
nnoremap <silent> <space>s  :<C-u>CocList -I symbols<cr>
" Do default action for next item.
nnoremap <silent> <space>j  :<C-u>CocNext<CR>
" Do default action for previous item.
nnoremap <silent> <space>k  :<C-u>CocPrev<CR>
" Resume latest coc list.
nnoremap <silent> <space>p  :<C-u>CocListResume<CR>

"===================
"====新文件标题=====
"===================
"新建。c,.h,.sh,.java 文件，自动插入文件头 
autocmd BufNewFile *.cpp,*.[ch],*.sh,*.rb,*.java,*.py exec ":call SetTitle()" 
""定义函数 SetTitle，自动插入文件头 
func SetTitle() 
	"如果文件类型为。sh 文件 
	if &filetype == 'sh' 
		call setline(1,"\#!/bin/bash") 
		call append(line("."), "") 
    elseif &filetype == 'python'
        call setline(1,"#!/usr/bin/env python")
        call append(line("."),"# coding=utf-8")
	    call append(line(".")+1, "") 

    elseif &filetype == 'ruby'
        call setline(1,"#!/usr/bin/env ruby")
        call append(line("."),"# encoding: utf-8")
	    call append(line(".")+1, "")

"    elseif &filetype == 'mkd'
"        call setline(1,"<head><meta charset=\"UTF-8\"></head>")
	else 
		call setline(1, "/*************************************************************************") 
		call append(line("."), "	> File Name: ".expand("%")) 
		call append(line(".")+1, "	> Author: ".expand("SWJ")) 
		call append(line(".")+2, "	> Mail: ".expand("zggsong@foxmail.com")) 
		call append(line(".")+3, "	> Created Time: ".strftime("%c")) 
		call append(line(".")+4, " ************************************************************************/") 
		call append(line(".")+5, "")
	endif
	if expand("%:e") == 'cpp'
		call append(line(".")+6, "#include<iostream>")
		call append(line(".")+7, "using namespace std;")
		call append(line(".")+8, "")
	endif
	if &filetype == 'c'
		call append(line(".")+6, "#include<stdio.h>")
		call append(line(".")+7, "")
	endif
	if expand("%:e") == 'h'
		call append(line(".")+6, "#ifndef _".toupper(expand("%:r"))."_H")
		call append(line(".")+7, "#define _".toupper(expand("%:r"))."_H")
		call append(line(".")+8, "#endif")
	endif
	if &filetype == 'java'
		call append(line(".")+6,"public class ".expand("%:r"))
		call append(line(".")+7,"")
	endif
	"新建文件后，自动定位到文件末尾
endfunc 
autocmd BufNewFile * normal G

"===================================
"===C,C++,java,python 按 F5 编译运行===
"===================================
map <F5> :call CompileRunGcc()<CR>
func! CompileRunGcc()
	exec "w"
	if &filetype == 'c'
		exec "!gcc % -o %<"
		exec "!time ./%<"
	elseif &filetype == 'cpp'
		exec "!g++ % -std=c++11 -o %<"
		exec "!time ./%<"
	elseif &filetype == 'java' 
		exec "!javac %" 
		exec "!time java %<"
	elseif &filetype == 'sh'
		:!time bash %
	elseif &filetype == 'python'
		exec "!time python2.7 %"
    elseif &filetype == 'html'
        exec "!firefox % &"
    elseif &filetype == 'go'
"        exec "!go build %<"
        exec "!time go run %"
    elseif &filetype == 'mkd'
        exec "!~/.vim/markdown.pl % > %.html &"
        exec "!firefox %.html &"
	endif
endfunc

```

## vim 键位图

![vim 键位图](https://cdn.zggsong.cn/2020/03/21/59e35de779eb5.gif)

## vim 系统剪贴板

- 首先，查看 vim 版本是否支持 clipboard
```shell
vim --version | grep "clipboard"
```
clipboard 前面有一个小小的减号，说明不支持。

- 如果不支持的话，需要安装图形化界面的 vim，或者重新编译 vim

```shell
sudo apt-get install vim-gnome
```
 - 安装完成后再次执行：
```shell
vim --version | grep "clipboard"
```
已经变成+clipboard
- vim 的寄存器

打开 vim 输入：reg 查看 vim 的寄存器，当支持 clipboard 之后，会多出"+寄存器，表示系统剪切板，在 vim 中进入 visual 视图后使用"Ny(N 表示特定寄存器编好），将内容复制到特定的剪切板，那么我们的目的是要复制到系统剪切板则需要选中内容后输入命令：
```shell
"+y
```
粘贴到特定的寄存器也是同理。例如"+p 将系统剪切板的内容拷贝到 vim 中（非编辑模式下）。

## vim 使用

### 1、常用快捷键
| Key | mean |
| :---| :--- |
| i | 插入 |
| I | 行首插入 |
| a | 光标后一位插入 |
| A | 行尾插入 |
| o | 下一行插入 |
| O | 上一行插入 |
| v | 视图模式（以光标处单词开始选中） |
| V | 视图模式（以光标所在行开始选中） |
| 0 | 移动光标至行首 |
| $ | 移动光标至行尾 |
| f |	查找光标后的单词 |
| F | 查找光标前的单词 |
| / | 搜索后高亮 |
| L | 屏幕底行 |
| -、= | 查找高亮内容 |
| Space+Enter | 取消高亮 |
| q | 定义宏 |
|x, X|	在一行字当中，x 为向后删除一个字符 （相当于 [del] 按键）， X 为向前删除一个字符（相当于 [backspace] 亦即是退格键） （常用）|
| nx	|n 为数字，连续向后删除 n 个字符。举例来说，我要连续删除 10 个字符， 『10x』。|
| dd | 删除游标所在的那一整行（常用）|
| ndd | n 为数字。删除光标所在的向下 n 行，例如 20dd 则是删除 20 行 （常用）|
|d1G	| 删除光标所在到第一行的所有数据|
|dG	| 删除光标所在到最后一行的所有数据|
|d$	| 删除游标所在处，到该行的最后一个字符|
|d0	| 那个是数字的 0 ，删除游标所在处，到该行的最前面一个字符|
|yy	| 复制游标所在的那一行（常用）|
|nyy	| n 为数字。复制光标所在的向下 n 行，例如 20yy 则是复制 20 行（常用）|
|y1G	| 复制游标所在行到第一行的所有数据|
|yG	| 复制游标所在行到最后一行的所有数据|
|y0	| 复制光标所在的那个字符到该行行首的所有数据|
|y$	| 复制光标所在的那个字符到该行行尾的所有数据|
|p, P | p 为将已复制的数据在光标下一行贴上，P 则为贴在游标上一行！ 举例来说，我目前光标在第 20 行，且已经复制了 10 行数据。则按下 p 后， 那 10 行数据会贴在原本的 20 行之后，亦即由 21 行开始贴。但如果是按下 P 呢？ 那么原本的第 20 行会被推到变成 30 行。 （常用）|
|J | 将光标所在行与下一行的数据结合成同一行|
|c | 重复删除多个数据，例如向下删除 10 行，[ 10cj ]|
|u | 复原前一个动作。（常用）|
|[Ctrl]+r | 重做上一个动作。（常用）这个 u 与 [Ctrl]+r 是很常用的指令！一个是复原，另一个则是重做一次～ 利用这两个功能按键，你的编辑，嘿嘿！很快乐的啦
|. | 不要怀疑！这就是小数点！意思是重复前一个动作的意思。 如果你想要重复删除、重复贴上等等动作，按下小数点『.』就好了！ （常用）|

### 2、常用命令

- 录制宏
 - 在 normal 模式下，按`q`加一个字母开始录制。例如按下`qa`，将该宏注册为 a。
 - 宏内容
 - 按下 q`完成录制。
 - `@宏名称`进行使用，例如`@a`, 并且`10@a`则执行 10 次
- 大小写：可视模式选中内容，按`~`即可
- 快速替换：输入`<++>`, 普通模式双击空格即可替换此内容
- 纵向添加删除内容：可视模式选中需要操作的行，`Ctrl`+`q`，按`I`输入内容后 ESC 即可
- `Ctrl` + `方向键`调整窗口大小
- `空格` + `方向键`选中窗口
- `s` + `h、j、k、l`建立不同方向的分屏
- `s`+`n、v`左右分屏和上下分屏互换
- `>`：缩进、`<`：取消缩进
