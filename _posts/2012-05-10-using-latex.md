---
layout: post
title: "Using Latex"
description: "Using Latex to typeset Scientific Articles"
category: general
tags: [latex]
---
{% include JB/setup %}

Using Latex to typeset Scientific Articles
==========================================

LaTeX is a system for setting characters ('types'). Actually, it is a wrapper of macros around TeX, which has a famous [history](http://www.tug.org/whatis.html). LaTeX is most widely used In technical writing, especially mathematics, physics, and computer science, because it can typeset beautiful mathematical formulae.




System Setup on Debian/Ubuntu
-----------------------------

Install LaTeX:

    sudo apt-get install \
      texlive              \
      texlive-doc-de       \
      texlive-latex-extra  \
      texlive-lang-german  \
      texlive-science         

Install vim, the Gnome version:

    sudo apt-get install \
      vim-gnome \
      vim-latexsuite \

Now you can edit and compile TeX files.



Basic editing and compiling
---------------------------


Create file `~/.vim/ftplugin/tex.vim` and insert

    " indentation
    set sw=2
 
    " compiler options
    let g:Tex_DefaultTargetFormat='dvi'
    let g:Tex_MultipleCompileFormats='dvi,pdf'
    let g:Tex_FormatDependency_pdf = 'dvi,ps,pdf'
    let g:Tex_CompileRule_dvi = 'latex --interaction=nonstopmode $* --src-specials'
    let g:Tex_CompileRule_ps = 'dvips -Ppdf -o $*.ps $*.dvi'
    let g:Tex_CompileRule_pdf = 'ps2pdf $*.ps'


Add this to your `~/.vimrc`:

    " Switch on syntax
    syntax on
    " REQUIRED. This makes vim invoke Latex-Suite when you open a tex file.
    filetype plugin on
    "
    " IMPORTANT: win32 users will need to have 'shellslash' set so that latex
    " can be called correctly.
    set shellslash
    
    " IMPORTANT: grep will sometimes skip displaying the file name if you
    " search in a singe file. This will confuse Latex-Suite. Set your grep
    " program to always generate a file-name.
    set grepprg=grep\ -nH\ $*
    
    map ,l :!latex %
    :map \ld :execute '!xdvi -editor "vim --servername 'v:servername' --remote +\%l \%f" -sourceposition ' . line(".") . expand("%") . " '" . expand(Tex_GetMainFileName(':r')) . ".dvi' >/dev/null&" <CR><CR>
    
    " Spelling:
    autocmd FileType tex setlocal spell spelllang=en_us


Now you can edit and compile `.tex` files from within gvim. Load a [template document](https://www.dropbox.com/sh/lppr2bhmrm0wpfc/KEokhp0usL) from my Dropbox.

    gvim technical_report.tex

In vim, press `\ll` to compile to dvi format and then `\ld` to view the document in xdvi. Change something and compile again, press `\ld` again or reload (`Ctrl+R`) the viewer.


Gvim can interact with xdvi
--------------------------

If one clicks at some place in xdvi (usually Ctrl+Button1), vim automatically jumps to the corresponding line in the LaTeX source file ("reverse search"). Also, from inside Vim, one can jump to the corresponding line in xdvi which becomes highlighted ("forward search").

First of all, the latex document need to tell the latex compiler to set marks in the dvi file. Only put:

    \usepackage{srcltx}

in the preamble of your latex document, or compile with:

    latex --src [your_file.tex]

In your `.vimrc` file, add at the end:

    :map \ld :execute '!xdvi -editor "vim --servername 'v:servername' --remote +\%l \%f" -sourceposition ' . line(".") . expand("%") . " '" . expand(Tex_GetMainFileName(':r')) . ".dvi' >/dev/null&" <CR><CR>

and in your `.bashrc`:

    alias vim='gvim --servername vimtex'

The first allows the forward search by `\ld` in normal mode.

For things to work you must, in order:

1. Open the tex file with gvim, 
2. Open the dvi file with the command \\ld 
3. reverse-search using Ctrl + left-mouse-click.

