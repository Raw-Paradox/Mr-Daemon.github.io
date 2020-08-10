---
layout: post
title: "Start with "hello world""    
subtitle:   
date: 2020-08-11
author: "Mr Daemon"
catalog: true
tags:
    - C
    - os
---

### Start with "hello world"

```c
#include <stdio.h>

int main(int argc, char const* argv[]) {
    printf("hello world\n");
    return 0;
}
```

Above is the `hello world` program written in c, quite easy isn’t it? This is a good example to tell how a text file `*.c` be executed on machine.

In this post I will explain this lil program line by line, word by word. Even a tiny program is very complex when you try to fully understand what happened in your computer when you use `gcc -o main main.c && ./main` to run this program.

First line is `#include <stdio.h>`, that means we include a header file called `<stdio.h>` in our program, actually this line is not going to be executed, after preprocess, this line will be replaced by entire content in `<stdio.h>`, which includes plenty of macro define and function declare. In this case, we use this line to import the `printf()`, we must declare the function before we use it. And it is worth to mention that there is no real `printf()` in `<stdio.h>`, the header file only contain the function signature, I will later talk about how this function be included.

The second line is a function define, which called `main` and has two parameters `argc:int` and `argv:char const* []`. To understand this two parameters, we need to think back how we start this program — `./main`. And we can pass parameters when run this program like `./main arg1 arg2`. The `argc` is the total number of the args and argv is a list contains pointers that point to the args. And don’t forget that `./main` itself is a parameter in the `argv` list.

The third line is the core part to our `hello world`, it print a message to terminal! But why terminal? All thing in linux is abstract to the file concept. Every process start with three file opened — 0 for `stdin`, 1 for `stdout` and 2 for `stderr`. The 0,1,2 are file descriptors, every process maintain a descriptor table, every descriptor in this table point to a entry in `file table` which is shared with all processes. The file descriptor may not uniquely point to a entry in `file table`, we can even use `dup2` to redirect a file descriptor. And `printf` only print content to the file descriptor 1, maybe not `stdout`. But in `stdout` case, you will see you message in terminal.

The forth line is the end of `main`. When your program terminated, you need to tell the operate system which load your program about the termiated state of your program, and `0` means `EXIT_SUCCESS`.

So how this text file named `main.c` run on your computer? You may recall that `gcc -o main main.c`. This shell cmd convert the text file `main.c` to executable file `main`. And there are four main procedures in that process: preprocess, compile, assemble, link:

- Preprocessor deal with the line start with `‘#’` like `#include<>`, `#define` and etc.
- Compiler compile your c sure code to assemble code depends on your machine.
- Assembler translate the assemble code to binary machine code, and the output of this procedure is already a `elf` file.(executable and linkable).
- The last procedure is link. Linker link your current `elf` files and the library, the `printf()` is really included in this step.