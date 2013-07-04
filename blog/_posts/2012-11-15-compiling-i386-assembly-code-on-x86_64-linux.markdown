---
layout: blog
title: Compiling i386 Assembly Code on x86_64 Linux
location: NKU, China
post: true

excerpt: Using as with the --32 option and ld with --melf_i386.
---

I happened to write an i386 assembly program but unfortunately, I'm
currently working on x86\_64 platform Arch Linux. After reading man
pages of `as` and `ld`, I finally found a way to compile and run the
program.

Take the following hello world program as an example.

<script src="https://gist.github.com/leewings/5925252.js"></script>

We must select the word size of 32-bit for an i386 program while using
`as` by adding the command line argument `--32`:

    $ as --32 -o hello.o hello.s

For the same reason, `-melf_i386` must be added while using `ld` in
order to emulate the `elf_i386` linker:

    $ ld -melf_i386 -o hello hello.o

Run `file hello` to check if we do things right.

    $ file hello
    hello: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), statically linked, not stripped

Eventually, run the target program and enjoy :)

    $ ./hello
    hello, world
