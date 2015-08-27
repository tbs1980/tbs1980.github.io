---
layout: post
title: "Open source numerical integration codes"
description: "Open source codes for numerical integration"
tags: [numerical integration, c++, fortran, matlab, python, mathematical methods, quadrature, QuantLin, AlgLib, QUADPACK]
modified: 2014-02-16
image:
  feature: abstract-6.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

Numerical integration is one of the widely used computational techniques employed in scientific computation. These method can be traced back to mathematicians of ancient Greece. Availability of stable integration routine depends on what programming language you use. For example in Python there is `scipy.integrate` and it provides a stable code for many differentness computational problems. In R there is the `integrate` function for one-diemnstional integral really well. Since I work mainly in c, c++ and fortran I was looking for a publicly available code that can be used with my code. I found that QUADPACK and Quadpack++ is the only stable codes publicly available (correct me if I am wrong). There are numerical integration methods available as a part of the QuantLib and AlgLib. Cuba library offers mutli-dimenstional integration routines.  

## QUADPACK

[QUADPACK](http://www.netlib.org/quadpack/) has been around for a while. Written in fortran it remains as one of most stable and widely used codes. It contains several routines for integrating functions of different characteristics. For example the routine `QAG` provides a 1D globally adaptive integrator using Gauss-Kronrod quadrature.

{% highlight fortran %}
subroutine qag(f,a,b,epsabs,epsrel,key,result,abserr,neval,ier,limit,lenw,last,iwork,work)
{% endhighlight %}

## GSL

[GSL](http://www.gnu.org/software/gsl/) has numerical integration module. It provides most of algorithms in QUADPACK. API is straightforward. The example below is from [GSL](https://www.gnu.org/software/gsl/manual/html_node/Numerical-integration-examples.html#Numerical-integration-examples)

{% highlight c %}
#include <stdio.h>
#include <math.h>
#include <gsl/gsl_integration.h>

double f (double x, void * params)
{
    double alpha = *(double *) params;
    double f = log(alpha*x) / sqrt(x);
    return f;
}

int main (void)
{
    gsl_integration_workspace * w
        = gsl_integration_workspace_alloc (1000);

    double result, error;
    double expected = -4.0;
    double alpha = 1.0;

    gsl_function F;
    F.function = &f;
    F.params = &alpha;

    gsl_integration_qags (&F, 0, 1, 0, 1e-7, 1000,
        w, &result, &error);

    printf ("result          = % .18f\n", result);
    printf ("exact result    = % .18f\n", expected);
    printf ("estimated error = % .18f\n", error);
    printf ("actual error    = % .18f\n", result - expected);
    printf ("intervals =  %d\n", w->size);

    gsl_integration_workspace_free (w);

    return 0;
}
{% endhighlight %}


## quadpack++

[quadpack++](http://quadpackpp.sourceforge.net/index.html) implements adaptive quadrature routines from the GNU Scientific Library for an arbitrary floating-point type using C++ templates. The only requirement is that standard arithmetic operators, including mixed arithmetic with standard types such as "int", be overloaded for the floating-point type. The core routines are implemented in header files, requiring nothing to be built or linked against. The library is intended for use with multiple-precision calculations. One package supporting this is MPFR, whose website has links to wrappers that define a multiple-precision floating-point type as a C++ class. This last point is *extremely* significant as both QUADPACK and GSL read the weights from a table and can only do certain predefined rules. quadpack++ implements codes to compute the weights for any arbitrary rule! Below is an example of what it can do

{% highlight c++ %}
#include <cmath>
#include <cstdlib>
#include <iostream>
#include "workspace.hpp"

typedef double Real;

#define ABS(x) (((x) < Real(0)) ? -(x) : (x))

Real integrand(Real x, Real* alpha)
{
    return pow(x, *alpha) * log(1/x);
}

Real exact(Real alpha)
{
    return pow(alpha + 1, -2);
}

int main(int argc, char** argv)
{
    size_t limit = 128;
    size_t m_deg = 10; // sets (2m+1)-point Gauss-Kronrod

    Workspace<Real> Work(limit, m_deg);

    // Set parameters and function class...
    Real alpha = (argc > 1) ? Real(atof(argv[1])) : Real(1);

    std::cout << "# alpha : " << alpha << std::endl;

    Function<Real, Real> F(integrand, &alpha);

    // Set default quadrature tolerance from machine epsilon...
    Real epsabs =  Work.get_eps() * 1e2;
    Real epsrel = Real(0);
    std::cout << "# epsabs : " << epsabs << std::endl;

    int status = 0;
    Real result, abserr;

    try {
        status = Work.qag(F, Real(0), Real(1), epsabs, epsrel, result, abserr);
    }
    catch (const char* reason) {
        std::cerr << reason << std::endl;
        return status;
    }

    std::cout << "result: " << result;
    std::cout << " error: " << ABS(result - exact(alpha));
    std::cout << std::endl;

    return 0;
}
{% endhighlight %}
