---
date: 2012-05-02 19:34:33+00:00
layout: post
title: vim复制插件
---

vim复制的时候不发复制到c-v的复制缓冲区,很麻烦,自己动手,丰衣足食

```vim
nnoremap y :set operatorfunc=Pbcopyg@
vnoremap y y:call Pbcopy()
function! s:Pbcopy()
    execute 'call system("pbcopy", getreg("\""))'
    execute 'call system("xsel --clipboard --input", getreg("\""))'
endfunction
```

将代码保存再.vim/plugin/copy.vim的文件即可
ubuntu环境下 默认得安装一下xsel这个东西
