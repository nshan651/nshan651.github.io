---
title: "The Worst Piece of Code I've Ever Seen"
date: 2023-03-05T17:16:20-06:00
draft: false
---

The other day I was on a long train ride back to Chicago, and I ended up getting sucked down the rabbit hole that is Bell Lab's [encyclopedia of things considered harmful](http://harmful.cat-v.org/). There is SO much here that I can't possibly cover in a single blog post, but I would definitely recommend having a look. Some of the highlights include [the basic laws of human stupidity](http://harmful.cat-v.org/people/basic-laws-of-human-stupidity/), [Sweden](http://harmful.cat-v.org/society/sweden), and [security theater](http://harmful.cat-v.org/security-theater/). But by far the most fleshed out and interesting section to me is on software. Their thesis, similarly to the folks at [suckless](https://suckless.org/) is this: all software sucks, but some software sucks less. They provide an enormous list of harmful stuff, and alternatives considered less harmful. Aside from the obvious memes of the Acme and ed text editors instead of Vim and Emacs, or `sed 11q` instead of `head`, the list shows that there is genuinely a lot of terrible software that's somehow still around.

Naturally, the first page I visited was on Java. Let me get one thing straight, I don't *hate* Java. Yes, it's verbose and downright ugly at times, and I'll be damned if I have to type `public static void main(String[] args)`  one more time, but there are some things to like (to not hate?) about it. The JVM was revolutionary for its ability to provide platform-independent applications. "Write once, run anywhere" was a huge selling point, along with it being "familiar" to anyone coming from C++, C, or JavaScript. Modern Java has also come a long way, with support for lambda expressions, streams, and optional values. The introduction of the `var` keyword in Java 10 also helped with reducing boilerplate. 

All told, I don't despise Java like some. Would it be my first choice to develop in? Hell no. But I do think it's become more bearable to work with over time. And the Java Collections documentation is still some of the best documentation I've used.

And with that, I present **[the worst piece of code I've ever seen](https://github.com/groovy/groovy-core/blob/master/src/main/org/codehaus/groovy/runtime/ArrayUtil.java)**.

```java
/**
* This is a generated class used internally during the writing of bytecode within the CallSiteWriter logic.
* This is not a class exposed to users, as is the case with almost all classes in the org.codehaus.groovy packages.
* The purpose is the reduction of the size of the bytecode. Consider creating a three element Object[] with null values:
* [...]
* The number of needed instructions is thus reduced from 15 to 4. For every entry we save 3 bytecode instructions.
* This allows better readable bytecode and it allows the JIT to see less bytecode to optimize, helping under the
* inlining threshold here or there.
* So even though the class is ugly, there are good reason to have this in Groovy, even if the class makes
* absolutely no sense in normal Java. But it is not used in normal Java, but from the bytecode. 
*/ 
public class ArrayUtil {
    private static final Object[] EMPTY = new Object[0]
            ;

    public static Object[] createArray() {
        return EMPTY;
    }

    public static Object[] createArray(Object arg0) {
        return new Object[]{
                arg0};
    }
    
    public static Object[] createArray(Object arg0, Object arg1) {
        return new Object[]{
                arg0, arg1};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2) {
        return new Object[]{
                arg0, arg1, arg2};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3) {
        return new Object[]{
                arg0, arg1, arg2, arg3};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5};
    }
    ...
```

You can probably see where this is going..but first, a couple qualifiers. First, this is the worst legitimate, production code I've seen. Obviously there are joke languages like [Brainfuck](https://en.wikipedia.org/wiki/Brainfuck) or [Malbolge](https://en.wikipedia.org/wiki/Malbolge) that are deliberately esoteric and hard to use. 

Not only is this code actually in production, it's part of the **groovy core library**. Look, I'm not claiming that Groovy's the most popular language out there, but this isn't some random CS Junior's pet project. Netflix, Oracle, Google, IBM, and JPMorgan Chase all use Groovy for one purpose or another. Let that sink in for a second.

It's legitimately so bad that the author had to put a comment justifying its ugliness:

> So even though the class is ugly, there are good reason to have this in Groovy, even if the class makes absolutely no sense in normal Java. But it is not used in normal Java, but from the bytecode.  

Okay, I know there is another comment stating that this is an internal class that isn't exposed to the user. I also acknowledge that this is meant to be an optimization aimed at reducing the size of the bytecode. But if you seriously have to write code like this, then there has to be severe foundational problems with the language. 

Anyway, it keeps going:

```java
    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41, Object arg42) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41, arg42};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41, Object arg42, Object arg43) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41, arg42, arg43};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41, Object arg42, Object arg43, Object arg44) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41, arg42, arg43, arg44};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41, Object arg42, Object arg43, Object arg44, Object arg45) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41, arg42, arg43, arg44, arg45};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41, Object arg42, Object arg43, Object arg44, Object arg45, Object arg46) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41, arg42, arg43, arg44, arg45, arg46};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41, Object arg42, Object arg43, Object arg44, Object arg45, Object arg46, Object arg47) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41, arg42, arg43, arg44, arg45, arg46, arg47};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41, Object arg42, Object arg43, Object arg44, Object arg45, Object arg46, Object arg47, Object arg48) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41, arg42, arg43, arg44, arg45, arg46, arg47, arg48};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41, Object arg42, Object arg43, Object arg44, Object arg45, Object arg46, Object arg47, Object arg48, Object arg49) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41, arg42, arg43, arg44, arg45, arg46, arg47, arg48, arg49};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41, Object arg42, Object arg43, Object arg44, Object arg45, Object arg46, Object arg47, Object arg48, Object arg49, Object arg50) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41, arg42, arg43, arg44, arg45, arg46, arg47, arg48, arg49, arg50};
    }
    
    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41, Object arg42, Object arg43, Object arg44, Object arg45, Object arg46, Object arg47, Object arg48, Object arg49, Object arg50, Object arg51) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41, arg42, arg43, arg44, arg45, arg46, arg47, arg48, arg49, arg50, arg51};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41, Object arg42, Object arg43, Object arg44, Object arg45, Object arg46, Object arg47, Object arg48, Object arg49, Object arg50, Object arg51, Object arg52) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41, arg42, arg43, arg44, arg45, arg46, arg47, arg48, arg49, arg50, arg51, arg52};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41, Object arg42, Object arg43, Object arg44, Object arg45, Object arg46, Object arg47, Object arg48, Object arg49, Object arg50, Object arg51, Object arg52, Object arg53) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41, arg42, arg43, arg44, arg45, arg46, arg47, arg48, arg49, arg50, arg51, arg52, arg53};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41, Object arg42, Object arg43, Object arg44, Object arg45, Object arg46, Object arg47, Object arg48, Object arg49, Object arg50, Object arg51, Object arg52, Object arg53, Object arg54) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41, arg42, arg43, arg44, arg45, arg46, arg47, arg48, arg49, arg50, arg51, arg52, arg53, arg54};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41, Object arg42, Object arg43, Object arg44, Object arg45, Object arg46, Object arg47, Object arg48, Object arg49, Object arg50, Object arg51, Object arg52, Object arg53, Object arg54, Object arg55) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41, arg42, arg43, arg44, arg45, arg46, arg47, arg48, arg49, arg50, arg51, arg52, arg53, arg54, arg55};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41, Object arg42, Object arg43, Object arg44, Object arg45, Object arg46, Object arg47, Object arg48, Object arg49, Object arg50, Object arg51, Object arg52, Object arg53, Object arg54, Object arg55, Object arg56) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41, arg42, arg43, arg44, arg45, arg46, arg47, arg48, arg49, arg50, arg51, arg52, arg53, arg54, arg55, arg56};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41, Object arg42, Object arg43, Object arg44, Object arg45, Object arg46, Object arg47, Object arg48, Object arg49, Object arg50, Object arg51, Object arg52, Object arg53, Object arg54, Object arg55, Object arg56, Object arg57) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41, arg42, arg43, arg44, arg45, arg46, arg47, arg48, arg49, arg50, arg51, arg52, arg53, arg54, arg55, arg56, arg57};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41, Object arg42, Object arg43, Object arg44, Object arg45, Object arg46, Object arg47, Object arg48, Object arg49, Object arg50, Object arg51, Object arg52, Object arg53, Object arg54, Object arg55, Object arg56, Object arg57, Object arg58) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41, arg42, arg43, arg44, arg45, arg46, arg47, arg48, arg49, arg50, arg51, arg52, arg53, arg54, arg55, arg56, arg57, arg58};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41, Object arg42, Object arg43, Object arg44, Object arg45, Object arg46, Object arg47, Object arg48, Object arg49, Object arg50, Object arg51, Object arg52, Object arg53, Object arg54, Object arg55, Object arg56, Object arg57, Object arg58, Object arg59) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41, arg42, arg43, arg44, arg45, arg46, arg47, arg48, arg49, arg50, arg51, arg52, arg53, arg54, arg55, arg56, arg57, arg58, arg59};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41, Object arg42, Object arg43, Object arg44, Object arg45, Object arg46, Object arg47, Object arg48, Object arg49, Object arg50, Object arg51, Object arg52, Object arg53, Object arg54, Object arg55, Object arg56, Object arg57, Object arg58, Object arg59, Object arg60) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41, arg42, arg43, arg44, arg45, arg46, arg47, arg48, arg49, arg50, arg51, arg52, arg53, arg54, arg55, arg56, arg57, arg58, arg59, arg60};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41, Object arg42, Object arg43, Object arg44, Object arg45, Object arg46, Object arg47, Object arg48, Object arg49, Object arg50, Object arg51, Object arg52, Object arg53, Object arg54, Object arg55, Object arg56, Object arg57, Object arg58, Object arg59, Object arg60, Object arg61) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41, arg42, arg43, arg44, arg45, arg46, arg47, arg48, arg49, arg50, arg51, arg52, arg53, arg54, arg55, arg56, arg57, arg58, arg59, arg60, arg61};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41, Object arg42, Object arg43, Object arg44, Object arg45, Object arg46, Object arg47, Object arg48, Object arg49, Object arg50, Object arg51, Object arg52, Object arg53, Object arg54, Object arg55, Object arg56, Object arg57, Object arg58, Object arg59, Object arg60, Object arg61, Object arg62) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41, arg42, arg43, arg44, arg45, arg46, arg47, arg48, arg49, arg50, arg51, arg52, arg53, arg54, arg55, arg56, arg57, arg58, arg59, arg60, arg61, arg62};
    }

```

...And going, and going, until we finally hit the bottom.

```java
    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41, Object arg42, Object arg43, Object arg44, Object arg45, Object arg46, Object arg47, Object arg48, Object arg49, Object arg50, Object arg51, Object arg52, Object arg53, Object arg54, Object arg55, Object arg56, Object arg57, Object arg58, Object arg59, Object arg60, Object arg61, Object arg62, Object arg63, Object arg64, Object arg65, Object arg66, Object arg67, Object arg68, Object arg69, Object arg70, Object arg71, Object arg72, Object arg73, Object arg74, Object arg75, Object arg76, Object arg77, Object arg78, Object arg79, Object arg80, Object arg81, Object arg82, Object arg83, Object arg84, Object arg85, Object arg86, Object arg87, Object arg88, Object arg89, Object arg90, Object arg91, Object arg92, Object arg93, Object arg94, Object arg95, Object arg96, Object arg97, Object arg98, Object arg99, Object arg100, Object arg101, Object arg102, Object arg103, Object arg104, Object arg105, Object arg106, Object arg107, Object arg108, Object arg109, Object arg110, Object arg111, Object arg112, Object arg113, Object arg114, Object arg115, Object arg116, Object arg117, Object arg118, Object arg119, Object arg120, Object arg121, Object arg122, Object arg123, Object arg124, Object arg125, Object arg126, Object arg127, Object arg128, Object arg129, Object arg130, Object arg131, Object arg132, Object arg133, Object arg134, Object arg135, Object arg136, Object arg137, Object arg138, Object arg139, Object arg140, Object arg141, Object arg142, Object arg143, Object arg144, Object arg145, Object arg146, Object arg147, Object arg148, Object arg149, Object arg150, Object arg151, Object arg152, Object arg153, Object arg154, Object arg155, Object arg156, Object arg157, Object arg158, Object arg159, Object arg160, Object arg161, Object arg162, Object arg163, Object arg164, Object arg165, Object arg166, Object arg167, Object arg168, Object arg169, Object arg170, Object arg171, Object arg172, Object arg173, Object arg174, Object arg175, Object arg176, Object arg177, Object arg178, Object arg179, Object arg180, Object arg181, Object arg182, Object arg183, Object arg184, Object arg185, Object arg186, Object arg187, Object arg188, Object arg189, Object arg190, Object arg191, Object arg192, Object arg193, Object arg194, Object arg195, Object arg196, Object arg197, Object arg198, Object arg199, Object arg200, Object arg201, Object arg202, Object arg203, Object arg204, Object arg205, Object arg206, Object arg207, Object arg208, Object arg209, Object arg210, Object arg211, Object arg212, Object arg213, Object arg214, Object arg215, Object arg216, Object arg217, Object arg218, Object arg219, Object arg220, Object arg221, Object arg222, Object arg223, Object arg224, Object arg225, Object arg226, Object arg227, Object arg228, Object arg229, Object arg230, Object arg231, Object arg232, Object arg233, Object arg234, Object arg235, Object arg236, Object arg237, Object arg238, Object arg239, Object arg240, Object arg241, Object arg242, Object arg243, Object arg244, Object arg245, Object arg246, Object arg247, Object arg248) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41, arg42, arg43, arg44, arg45, arg46, arg47, arg48, arg49, arg50, arg51, arg52, arg53, arg54, arg55, arg56, arg57, arg58, arg59, arg60, arg61, arg62, arg63, arg64, arg65, arg66, arg67, arg68, arg69, arg70, arg71, arg72, arg73, arg74, arg75, arg76, arg77, arg78, arg79, arg80, arg81, arg82, arg83, arg84, arg85, arg86, arg87, arg88, arg89, arg90, arg91, arg92, arg93, arg94, arg95, arg96, arg97, arg98, arg99, arg100, arg101, arg102, arg103, arg104, arg105, arg106, arg107, arg108, arg109, arg110, arg111, arg112, arg113, arg114, arg115, arg116, arg117, arg118, arg119, arg120, arg121, arg122, arg123, arg124, arg125, arg126, arg127, arg128, arg129, arg130, arg131, arg132, arg133, arg134, arg135, arg136, arg137, arg138, arg139, arg140, arg141, arg142, arg143, arg144, arg145, arg146, arg147, arg148, arg149, arg150, arg151, arg152, arg153, arg154, arg155, arg156, arg157, arg158, arg159, arg160, arg161, arg162, arg163, arg164, arg165, arg166, arg167, arg168, arg169, arg170, arg171, arg172, arg173, arg174, arg175, arg176, arg177, arg178, arg179, arg180, arg181, arg182, arg183, arg184, arg185, arg186, arg187, arg188, arg189, arg190, arg191, arg192, arg193, arg194, arg195, arg196, arg197, arg198, arg199, arg200, arg201, arg202, arg203, arg204, arg205, arg206, arg207, arg208, arg209, arg210, arg211, arg212, arg213, arg214, arg215, arg216, arg217, arg218, arg219, arg220, arg221, arg222, arg223, arg224, arg225, arg226, arg227, arg228, arg229, arg230, arg231, arg232, arg233, arg234, arg235, arg236, arg237, arg238, arg239, arg240, arg241, arg242, arg243, arg244, arg245, arg246, arg247, arg248};
    }

    public static Object[] createArray(Object arg0, Object arg1, Object arg2, Object arg3, Object arg4, Object arg5, Object arg6, Object arg7, Object arg8, Object arg9, Object arg10, Object arg11, Object arg12, Object arg13, Object arg14, Object arg15, Object arg16, Object arg17, Object arg18, Object arg19, Object arg20, Object arg21, Object arg22, Object arg23, Object arg24, Object arg25, Object arg26, Object arg27, Object arg28, Object arg29, Object arg30, Object arg31, Object arg32, Object arg33, Object arg34, Object arg35, Object arg36, Object arg37, Object arg38, Object arg39, Object arg40, Object arg41, Object arg42, Object arg43, Object arg44, Object arg45, Object arg46, Object arg47, Object arg48, Object arg49, Object arg50, Object arg51, Object arg52, Object arg53, Object arg54, Object arg55, Object arg56, Object arg57, Object arg58, Object arg59, Object arg60, Object arg61, Object arg62, Object arg63, Object arg64, Object arg65, Object arg66, Object arg67, Object arg68, Object arg69, Object arg70, Object arg71, Object arg72, Object arg73, Object arg74, Object arg75, Object arg76, Object arg77, Object arg78, Object arg79, Object arg80, Object arg81, Object arg82, Object arg83, Object arg84, Object arg85, Object arg86, Object arg87, Object arg88, Object arg89, Object arg90, Object arg91, Object arg92, Object arg93, Object arg94, Object arg95, Object arg96, Object arg97, Object arg98, Object arg99, Object arg100, Object arg101, Object arg102, Object arg103, Object arg104, Object arg105, Object arg106, Object arg107, Object arg108, Object arg109, Object arg110, Object arg111, Object arg112, Object arg113, Object arg114, Object arg115, Object arg116, Object arg117, Object arg118, Object arg119, Object arg120, Object arg121, Object arg122, Object arg123, Object arg124, Object arg125, Object arg126, Object arg127, Object arg128, Object arg129, Object arg130, Object arg131, Object arg132, Object arg133, Object arg134, Object arg135, Object arg136, Object arg137, Object arg138, Object arg139, Object arg140, Object arg141, Object arg142, Object arg143, Object arg144, Object arg145, Object arg146, Object arg147, Object arg148, Object arg149, Object arg150, Object arg151, Object arg152, Object arg153, Object arg154, Object arg155, Object arg156, Object arg157, Object arg158, Object arg159, Object arg160, Object arg161, Object arg162, Object arg163, Object arg164, Object arg165, Object arg166, Object arg167, Object arg168, Object arg169, Object arg170, Object arg171, Object arg172, Object arg173, Object arg174, Object arg175, Object arg176, Object arg177, Object arg178, Object arg179, Object arg180, Object arg181, Object arg182, Object arg183, Object arg184, Object arg185, Object arg186, Object arg187, Object arg188, Object arg189, Object arg190, Object arg191, Object arg192, Object arg193, Object arg194, Object arg195, Object arg196, Object arg197, Object arg198, Object arg199, Object arg200, Object arg201, Object arg202, Object arg203, Object arg204, Object arg205, Object arg206, Object arg207, Object arg208, Object arg209, Object arg210, Object arg211, Object arg212, Object arg213, Object arg214, Object arg215, Object arg216, Object arg217, Object arg218, Object arg219, Object arg220, Object arg221, Object arg222, Object arg223, Object arg224, Object arg225, Object arg226, Object arg227, Object arg228, Object arg229, Object arg230, Object arg231, Object arg232, Object arg233, Object arg234, Object arg235, Object arg236, Object arg237, Object arg238, Object arg239, Object arg240, Object arg241, Object arg242, Object arg243, Object arg244, Object arg245, Object arg246, Object arg247, Object arg248, Object arg249) {
        return new Object[]{
                arg0, arg1, arg2, arg3, arg4, arg5, arg6, arg7, arg8, arg9, arg10, arg11, arg12, arg13, arg14, arg15, arg16, arg17, arg18, arg19, arg20, arg21, arg22, arg23, arg24, arg25, arg26, arg27, arg28, arg29, arg30, arg31, arg32, arg33, arg34, arg35, arg36, arg37, arg38, arg39, arg40, arg41, arg42, arg43, arg44, arg45, arg46, arg47, arg48, arg49, arg50, arg51, arg52, arg53, arg54, arg55, arg56, arg57, arg58, arg59, arg60, arg61, arg62, arg63, arg64, arg65, arg66, arg67, arg68, arg69, arg70, arg71, arg72, arg73, arg74, arg75, arg76, arg77, arg78, arg79, arg80, arg81, arg82, arg83, arg84, arg85, arg86, arg87, arg88, arg89, arg90, arg91, arg92, arg93, arg94, arg95, arg96, arg97, arg98, arg99, arg100, arg101, arg102, arg103, arg104, arg105, arg106, arg107, arg108, arg109, arg110, arg111, arg112, arg113, arg114, arg115, arg116, arg117, arg118, arg119, arg120, arg121, arg122, arg123, arg124, arg125, arg126, arg127, arg128, arg129, arg130, arg131, arg132, arg133, arg134, arg135, arg136, arg137, arg138, arg139, arg140, arg141, arg142, arg143, arg144, arg145, arg146, arg147, arg148, arg149, arg150, arg151, arg152, arg153, arg154, arg155, arg156, arg157, arg158, arg159, arg160, arg161, arg162, arg163, arg164, arg165, arg166, arg167, arg168, arg169, arg170, arg171, arg172, arg173, arg174, arg175, arg176, arg177, arg178, arg179, arg180, arg181, arg182, arg183, arg184, arg185, arg186, arg187, arg188, arg189, arg190, arg191, arg192, arg193, arg194, arg195, arg196, arg197, arg198, arg199, arg200, arg201, arg202, arg203, arg204, arg205, arg206, arg207, arg208, arg209, arg210, arg211, arg212, arg213, arg214, arg215, arg216, arg217, arg218, arg219, arg220, arg221, arg222, arg223, arg224, arg225, arg226, arg227, arg228, arg229, arg230, arg231, arg232, arg233, arg234, arg235, arg236, arg237, arg238, arg239, arg240, arg241, arg242, arg243, arg244, arg245, arg246, arg247, arg248, arg249};
    }
}
```

If this doesn't inspire a righteous anger in you, then I don't know what will. 251 `createArray` methods and 1312 lines of pure torture. Be honest, how many times have you encountered a function with 249 parameters??? I haven't been programming for long, but I'm not sure when I'll encounter anything that can top this absolute abomination.

I want to end it here by stating that I do not mean for this to be an attack on the Groovy developers. They're a really talented bunch who've worked really hard on this. Groovy is no doubt behind a lot of the cool things that the above companies have produced. Heck, I've never programmed in Groovy before, so maybe I'd like it!
