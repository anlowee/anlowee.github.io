---
layout:     post                   
title:      2019 Fall 3rd CSAPP Homework            
subtitle:   7.6, 7.10, 7.12
date:       2019-11-30              
author:     EDDY                    
header-img:
catalog: true               
tags:   
    - study
---

---
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>
<script type="text/x-mathjax-config">
    MathJax.Hub.Config({ tex2jax: {inlineMath: [['$', '$']]}, messageStyle: "none" });
</script>

# CSAPP Homework 
**Name: 魏啸冲**

**Student ID: 2018302060342**

**Date: 11/20/2019**

---

## 7.6
**swap.c:**
~~~C
extern int buf[];   // extern
int *bufp0 = &buf[0];   // non-static global var and initialized
static int *bufp1;  // static var cannot be used in another file
static void incr()  // static function cannot be used in another file
{
    static int count = 0;   // static local var and initialized
    count++;
}
void swap() // function name
{
    int temp;   // just a local var
    incr(); 
    bufp1 = &buf[1];
    temp = *bufp0;
    *bufp0 = *bufp1;
    *bufp1 = temp;
}
~~~

**answer:**
| Symbol  | swap.o .symtab entry? | Symbol type | Module where defined | Section |
| ------- | --------------------- | ----------- | -------------------- | ------- |
| `buf`   | yes                   | extern      | m.o                  | .data   |
| `bufp0` | yes                   | global      | swap.o               | .data   |
| `bufp1` | yes                   | local       | swap.o               | .bss    |
| `swap`  | yes                   | global      | swap.o               | .text   |
| `temp`  | no                    | /           | /                    | /       |
| `incr`  | yes                   | local       | swap.o               | .text   |
| `count` | yes                   | local       | swap.o               | .bss    |

---

## 7.10
**file depending relationship:**
~~~
A. 
p.o → libx.a → p.o
B.
p.o → libx.a → liby.a and liby.a → libx.a
C.
p.o → libx.a → liby.a → libz.a and liby.a → libx.a → libz.a
~~~

**answer:**
In each operation, we have three sets, `E(empty)`, `U(undefined)`, `D(defined)`. At begining, `E` is a empty file set(which can only be put in `.o` or `.a` files), `U` and `D` are both empty symbol sets, what they store are just as their name.

First step, if there should not be .c files in the instruction, so **compiler** will compile them into `.o` files. For this question, there are no `.c` files, so we can skip this step.

Then **linker** start to work, it scan each file from left to right of the instruction. For example, if the current file is a `.o` file, it will do three things step by step:
+ put this file in `E`
+ put its **defined** symbols in `D`
+ put its **undefined** symbols in `U`

Else, if it is a `.a` file, there is some difference. We know that there may be lots of `.o` files packed in `.a` files. Assume that the first one is `m.o`, then linker will do following things:
+ **check** each symbol in `U` if it is in `m.o`'s symbol table
+ if it cannot find any in `U`, drop this file and scan the next, else keep going
+ put the **defined** symbols of `m.o` in `D`
+ put the **undefined** symbols of `m.o` in `U` 
+ scan next `.o` file
+ when all `.o` files are scanned, drop those are not in `E`

When linker scan all files by above rules, if `U` is empty, link is successful else failed.

So, we just need to put the depended files behind depending files.

**A.** `gcc p.o libx.a p.o`

**B.** `gcc p.o libx.a liby.a libx.a`

**C.** `gcc p.o libx.a liby.a libx.a libz.a`

---

## 7.12
**relocation entry:**
~~~
r.offset = 0xa 
r.symbol = swap 
r.type = R_X86_64_PC32
r.addend = -4
~~~

**answer:**
**A:**
$refaddr = ADDR(s) + r.offset = ADDR(.text) + r.offset = 0x4004e0 + 0xa = 0x4004ea$
$*refptr = (unsigned)(ADDR(r.symbol) + r.addend - refaddr) = 0x4004f8 - 4 - 0x4004ea = 0xa$

**B:**
$refaddr = ADDR(s) + r.offset = ADDR(.text) + r.offset = 0x4004d0 + 0xa = 0x4004da$
$*refptr = (unsigned)(ADDR(r.symbol) + r.addend - refaddr) = 0x400500 - 4 - 0x4004da = 0x22$

---

[👉HOME👈](http://www.iamwxc.com/)

---

