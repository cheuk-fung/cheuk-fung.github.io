---
layout: computer-matters
title: Patch for Blahtex
location: NKU, China
post: true

excerpt: "error: ‘isatty’ was not declared in this scope"
---

If you want to enable [Maruku]’s support of inline LaTeX-style math[^1]
using [blahtex], then this patch would help.

[Maruku]: http://maruku.rubyforge.org
[blahtex]: http://gva.noekeon.org/blahtexml

When compiling blahtex, I met the following error:

    $ make blahtex-linux
    mkdir -p bin-blahtex
    g++ -I. -ISource -ISource/BlahtexCore -ISource/BlahtexXMLin -O2 -c
    Source/main.cpp -o bin-blahtex/main.o
    Source/main.cpp: In function ‘int main(int, char* const*)’:
    Source/main.cpp:552:21: error: ‘isatty’ was not declared in this scope
    make: *** [bin-blahtex/main.o] Error 1

According to the man page of `isatty`, it should has `unistd.h`
included. Another file `mainPng.cpp` has the same problem. We can fix it
just by including `unistd.h` in both files.

The patch file is available [here][gist]. For Arch Linux users, you can build
from [aur][].

[gist]: https://gist.github.com/4050492
[aur]: https://aur.archlinux.org/packages/blahtex

[^1]: More information about math support in maruku: <http://maruku.rubyforge.org/math.xhtml>
