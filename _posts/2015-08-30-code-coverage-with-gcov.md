---
layout: post
title: "Analysing code coverage using gcov"
description: "Basics of analysing code coverage using gcov"
tags: [C++,C,code-coverage, unit-testing]
modified: 2015-08-30
---

Code coverage gives us an idea about what parts of the code is covered by the unit tests.

## `gcov`

There are several open source tools available for analysing coverage. In this post, however, I will be using `gcov`.

Let us consider a simple example of a calculator program. My calculator class is shown below

{% highlight c++ %}

template<class scalarType>
class calculator
{
public:
    static scalarType add(scalarType const a , scalarType const b)
    {
        return a+b;
    }

    static scalarType subtract(scalarType const a , scalarType const b)
    {
        return a-b;
    }

    static scalarType multiply(scalarType const a , scalarType const b)
    {
        return a*b;
    }

    static scalarType divide(scalarType const a , scalarType const b)
    {
        return a/b;
    }

    static scalarType abs(scalarType const a)
    {
        if(a<scalarType(0))
        {
            return -a;
        }

        return a;
    }
};

{% endhighlight %}

There are several public functions that emulates the functionality of a calculator. Ideally, one would like to write a unit test corresponding to each of these functions so that the code is robust. The code coverage tools help you understand how much of your code is covered by the test programs. Now let us write a test program for the `add` function as shown below

{% highlight c++ %}

#include <calculator.hpp>

int main(void)
{
    if( calculator<double>::add(double(1.),double(2.)) !=  double(1.)+double(2.) )
    {
        return 1;
    }
    return 0;
}

{% endhighlight %}

If this is the only test program you have, then the code coverage tools will tell you that the your code is not completely covered. Now let us consider unit tests for another function `abs`.

{% highlight c++ %}

if( calculator<double>::abs(double(-1.)) != double(1.) )
{
    return 1;
}

{% endhighlight %}

Imagine that this is the only test function for the `abs` function. We then know that the test will not go beyond the `if` clause.  This is where code coverage tools can be helpful. Let us see what the [codecov.io](https://codecov.io/github/tbs1980/CodCov/Calculator/calculator.hpp?ref=7dd5011fa287addc11ca6f3f302372bdcc136b6a) tells us about the testing. Below is a screenshot of the coverage report.

![calculator code coverage](/images/sreen_shot_code_cov_eg.png)

As we can see the tests missed the `return a` statement at the bottom `abs` function. Therefore, ideally, we require another test function like

{% highlight c++ %}

if( calculator<double>::abs(double(1.)) != double(1.) )
{
    return 1;
}

{% endhighlight %}

If both test functions are present we can be sure that the `abs` function is reliable.

## Setting up a `codecov` report using TravisCI

If your code is under a continuous integration platform, we can easily integrate the code to the a coverage platform like [codecov](https://codecov.io/). All you have to do is add a few lines in the `.travis.yml` file. Below is an example

{% highlight yaml %}
script:
    - make testCalculator
    - ./Calculator/testCalculator
    - gcov Calculator/testCalculator --object-directory=Calculator/CMakeFiles/testCalculator.dir/testCalculator.cpp.gcno
    - ls -la

after_success:
    - bash <(curl -s https://codecov.io/bash)

{% endhighlight %}

The next steps are

1. make an account in [codecov](https://codecov.io/) and
2. add your repository to the dashboard. 

If you are linking your [github](https://github.com/), then adding a public repository is straightforward. My example repository can be found [here](https://github.com/tbs1980/CodCov).
