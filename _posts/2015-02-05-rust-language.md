---
layout: post
title: "Rust programming language"
description: "Features of rust programming language"
tags: [programming language, rust, systems programming]
modified: 2015-02-05
image:
  feature: abstract-7.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

I hear a lot of good things about [Rust](https://www.rust-lang.org/) programming language. It currently sits in the top 20 in popularity. I wanted to checkout the language features and I find it rather refreshing. However, as with any new language, Rust has some issues as well.


## What is it all about?

There is a lot of talk about how Rust is superior to c/c++. Here is an example from [Dzmitry Malyshau](http://kukuruku.co/hub/rust/comparing-rust-and-cpp) that shows "A lost pointer to the Local Variable"

{% highlight c %}
#include <stdio.h>
int *bar(int *p) {
    return p;
}
int* foo(int n) {
    return bar(&n);
}
int main() {
    int *p1 = foo(1);
    int *p2 = foo(2);
    printf("%d, %d\n", *p1, *p2);
}
{% endhighlight %}

This will print

    2, 2

How ever in Rust an equivalent code will result in a compilation error.

{% highlight rust %}
fn bar<'a>(p: &'a int) -> &'a int {
    return p;
}
fn foo(n: int) -> &int {
    bar(&n)
}
fn main() {
    let p1 = foo(1);
    let p2 = foo(2);
    println!("{}, {}", *p1, *p2);
}
{% endhighlight %}

While compiling you will see

    demo:5:10: 5:11 error: `n` does not live long enough
    demo:5 bar(&n)
    ^
    demo:4:24: 6:2 note: reference must be valid for the anonymous lifetime #1 defined on the block at 4:23…
    demo:4 fn foo(n: int) -> &int {
    demo:5 bar(&n)
    demo:6 }
    demo:4:24: 6:2 note: ...but borrowed value is only valid for the block at 4:23
    demo:4 fn foo(n: int) -> &int {
    demo:5 bar(&n)
    demo:6 }

I agree that the pointer is lost there, but do we really write a piece of code like that in C in real life? In my opinion unlikely.

In any case I have enjoyed programming in Rust. :) [Here](https://github.com/tbs1980/Rust) is my collection of examples.
