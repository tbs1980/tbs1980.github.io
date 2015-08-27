---
layout: post
title: "OpenACC based scientific computing"
description: "Scope of OpenACC in scientific computing"
tags: [scientific computing, OpenACC, compilers, c, c++, PGI, CAPS, OpenUH, accULL, OpenARC]
modified: 2015-04-19
---

OpenACC is getting a lot of attention recently and especially in the application to scientific computing. The main reason I believe is that these are compiler directives and hence reasonably fast to learn as long as you have pieces of code that are parallelisable.  I wanted to see what compilers are publicly available for OpenACC.

## Compiler support

The following compilers support the OpenACC directives (as of today)

1. [PGI](https://www.pgroup.com/resources/accel.htm)
2. [CAPS](http://exxactcorp.com/index.php/software/prod_list/5)
3. [OpenUH](http://web.cs.uh.edu/~openuh/)
4. [accULL](https://accull.wordpress.com/)
5. [OpenARC](http://ft.ornl.gov/research/openarc/)

OpenUH and accULL are open source and are available for download now. On a linux platform you can get these to build and check sone cool features of OpenACC.

{% highlight c %}
/*
 * Simple test
 *  allocates two vectors, fills the vectors, does axpy a couple of times,
 *  compares the results for correctness.
 *  This is the same as version s1.cpp with 'int' instead of 'double'
 */

#include <iostream>
#include <cstdio>
#include <openacc.h>

/* Depending on the GCC version you have installed, this cause warnings */
extern "C" int atoi(const char*);

static void
axpy( int* y, int* x, int a, int n ){
    #pragma acc parallel loop present(x[0:n],y[0:n])
    for( int i = 0; i < n; ++i )
        y[i] += a*x[i];
}

static void
test( int* a, int* b, int n ){
    #pragma acc data copy(a[0:n]) copyin(b[0:n])
    {
    axpy( a, b, 2, n );

    for( int i = 0; i < n; ++i ) b[i] = 2;
    #pragma acc update device(b[0:n])

    axpy( a, b, 1.0, n );
    }
}


int main( int argc, char* argv[] ){
    int n = 1000;
    if( argc > 1 ) n = atoi(argv[1]);

    int* a, *b;

    a = new int[n];
    b = new int[n];

    for( int i = 0; i < n; ++i ) a[i] = i;
    for( int i = 0; i < n; ++i ) b[i] = n-i;

    test( a, b, n );

    int sum = 0;
    for( int i = 0; i < n; ++i ) sum += a[i];
    int exp = 0;
    for( int i = 0; i < n; ++i ) exp += (int)i + 2*(int)(n-i) + 2;
    std::cout << "Checksum is " << sum << std::endl;
    if( exp != sum )
    std::cout << "Difference is " << exp - sum << std::endl;
    else
    std::cout << "PASSED" << std::endl;
    return 0;
}

{% endhighlight %}

The most difficult bit is to get a compiler installed. The rest is very exciting!
