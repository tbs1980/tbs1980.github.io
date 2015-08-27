---
layout: post
title: "Numerical optimization codes"
description: "A survey of numerical optimization codes that are publicly available"
tags: [mathematical methods, numerical optimization, c, c++, python, Eigen, GSL,MINPACK,LFBGS,HLFBGS,SciPy,M1QN3, OptSolve, fortran ]
modified: 2014-04-12
image:
  feature: abstract-7.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

I was looking for a code that can handle an optimization problem with a million dimension. What I know is that my function has a single peak. I can also get a fairly good starting point. There are correlations between parameters. Given these conditions, my question was what software is publicly available for solving the equations numerically. A huge list of open source minimization software can be found in [Wikipedia](https://en.wikipedia.org/wiki/List_of_optimization_software#Free_and_Open_Source_software). Eigen library provides a set of non-linear optimization methods. It is ported from MINPACK. Its starting point was the c version of MINPACK [cminpack](http://devernay.free.fr/hacks/cminpack/index.html). Then there is lfbgs++ and HLFBGS. There are routines in Python and R to do this but my code was in C++. I came across a M1QN3 algorithm and was very impressed by its performance.


## MINPACK

[MINPACK](http://www.netlib.org/minpack/) is a library of FORTRAN subroutines for the solving of systems of nonlinear equations, or the least squares minimization of the residual of a set of linear or nonlinear equations. One of the famous subroutines in this code is `lmder`.

{% highlight fortran %}
      subroutine lmder(fcn,m,n,x,fvec,fjac,ldfjac,ftol,xtol,gtol,
     *                 maxfev,diag,mode,factor,nprint,info,nfev,njev,
     *                 ipvt,qtf,wa1,wa2,wa3,wa4)
{% endhighlight %}

A C/C++ rewrite of the MINPACK is available in [GitHub](https://github.com/devernay/cminpack). This portable and easy to use. Here is an example from [cminpack](https://raw.githubusercontent.com/devernay/cminpack/master/examples/tlmder_.c). Note the definition of `__minpack_real__` and the use macro `__minpack_func__()`

{% highlight c %}
/*      driver for lmder example. */


#include <stdio.h>
#include <math.h>
#include <assert.h>
#include <minpack.h>
#define real __minpack_real__

void fcn(const int *m, const int *n, const real *x, real *fvec, real *fjac,
     const int *ldfjac, int *iflag);

int main()
{
  int i, j, m, n, ldfjac, maxfev, mode, nprint, info, nfev, njev;
  int ipvt[3];
  real ftol, xtol, gtol, factor, fnorm;
  real x[3], fvec[15], fjac[15*3], diag[3], qtf[3],
    wa1[3], wa2[3], wa3[3], wa4[15];
  int one=1;
  real covfac;

  m = 15;
  n = 3;

/*      the following starting values provide a rough fit. */

  x[1-1] = 1.;
  x[2-1] = 1.;
  x[3-1] = 1.;

  ldfjac = 15;

  /*      set ftol and xtol to the square root of the machine */
  /*      and gtol to zero. unless high solutions are */
  /*      required, these are the recommended settings. */

  ftol = sqrt(__minpack_func__(dpmpar)(&one));
  xtol = sqrt(__minpack_func__(dpmpar)(&one));
  gtol = 0.;

  maxfev = 400;
  mode = 1;
  factor = 1.e2;
  nprint = 0;

  __minpack_func__(lmder)(&fcn, &m, &n, x, fvec, fjac, &ldfjac, &ftol, &xtol, &gtol,
    &maxfev, diag, &mode, &factor, &nprint, &info, &nfev, &njev,
    ipvt, qtf, wa1, wa2, wa3, wa4);
  fnorm = __minpack_func__(enorm)(&m, fvec);
  printf("      final l2 norm of the residuals%15.7g\n\n", (double)fnorm);
  printf("      number of function evaluations%10i\n\n", nfev);
  printf("      number of Jacobian evaluations%10i\n\n", njev);
  printf("      exit parameter                %10i\n\n", info);
  printf("      final approximate solution\n");
  for (j=1; j<=n; j++) {
    printf("%s%15.7g", j%3==1?"\n     ":"", (double)x[j-1]);
  }
  printf("\n");
  ftol = __minpack_func__(dpmpar)(&one);
  covfac = fnorm*fnorm/(m-n);
  __minpack_func__(covar)(&n, fjac, &ldfjac, ipvt, &ftol, wa1);
  printf("      covariance\n");
  for (i=1; i<=n; i++) {
    for (j=1; j<=n; j++) {
      printf("%s%15.7g", j%3==1?"\n     ":"", (double)fjac[(i-1)*ldfjac+j-1]*covfac);
    }
  }
  printf("\n");
  return 0;
}

void fcn(const int *m, const int *n, const real *x, real *fvec, real *fjac,
     const int *ldfjac, int *iflag)
{

  /*      subroutine fcn for lmder example. */

  int i;
  real tmp1, tmp2, tmp3, tmp4;
  real y[15]={1.4e-1, 1.8e-1, 2.2e-1, 2.5e-1, 2.9e-1, 3.2e-1, 3.5e-1,
        3.9e-1, 3.7e-1, 5.8e-1, 7.3e-1, 9.6e-1, 1.34, 2.1, 4.39};
  assert(*m == 15 && *n == 3);

  if (*iflag == 0) {
    /*      insert print statements here when nprint is positive. */
    /* if the nprint parameter to lmder is positive, the function is
       called every nprint iterations with iflag=0, so that the
       function may perform special operations, such as printing
       residuals. */
    return;
  }

  if (*iflag != 2) {
    /* compute residuals */
    for (i=1; i <= 15; i++)
    {
      tmp1 = i;
      tmp2 = 16 - i;
      tmp3 = tmp1;
      if (i > 8) {
        tmp3 = tmp2;
      }
      fvec[i-1] = y[i-1] - (x[1-1] + tmp1/(x[2-1]*tmp2 + x[3-1]*tmp3));
    }
  } else {
    /* compute Jacobian */
    for (i=1; i<=15; i++) {
      tmp1 = i;
      tmp2 = 16 - i;
      tmp3 = tmp1;
      if (i > 8) {
        tmp3 = tmp2;
      }
      tmp4 = (x[2-1]*tmp2 + x[3-1]*tmp3); tmp4 = tmp4*tmp4;
      fjac[i-1 + *ldfjac*(1-1)] = -1.;
      fjac[i-1 + *ldfjac*(2-1)] = tmp1*tmp2/tmp4;
      fjac[i-1 + *ldfjac*(3-1)] = tmp1*tmp3/tmp4;
    };
  }
  return;
}
{% endhighlight %}

## Eigen library

[Eigen](http://eigen.tuxfamily.org/index.php?title=Main_Page) has its own port of MINPACK. However, it is in the unsupported modules and the documentation can be found [here](http://eigen.tuxfamily.org/dox-devel/unsupported/group__NonLinearOptimization__Module.html).  Here is a snippet from of the examples provided by Eigen.

{% highlight c++ %}
struct lmder_functor : Functor<double>
{
    lmder_functor(void): Functor<double>(3,15) {}
    int operator()(const VectorXd &x, VectorXd &fvec) const
    {
        double tmp1, tmp2, tmp3;
        static const double y[15] = {1.4e-1, 1.8e-1, 2.2e-1, 2.5e-1, 2.9e-1, 3.2e-1, 3.5e-1,
            3.9e-1, 3.7e-1, 5.8e-1, 7.3e-1, 9.6e-1, 1.34, 2.1, 4.39};

        for (int i = 0; i < values(); i++)
        {
            tmp1 = i+1;
            tmp2 = 16 - i - 1;
            tmp3 = (i>=8)? tmp2 : tmp1;
            fvec[i] = y[i] - (x[0] + tmp1/(x[1]*tmp2 + x[2]*tmp3));
        }
        return 0;
    }

    int df(const VectorXd &x, MatrixXd &fjac) const
    {
        double tmp1, tmp2, tmp3, tmp4;
        for (int i = 0; i < values(); i++)
        {
            tmp1 = i+1;
            tmp2 = 16 - i - 1;
            tmp3 = (i>=8)? tmp2 : tmp1;
            tmp4 = (x[1]*tmp2 + x[2]*tmp3); tmp4 = tmp4*tmp4;
            fjac(i,0) = -1;
            fjac(i,1) = tmp1*tmp2/tmp4;
            fjac(i,2) = tmp1*tmp3/tmp4;
        }
        return 0;
    }
};

void testLmder1()
{
  int n=3, info;

  VectorXd x;

  /* the following starting values provide a rough fit. */
  x.setConstant(n, 1.);

  // do the computation
  lmder_functor functor;
  LevenbergMarquardt<lmder_functor> lm(functor);
  info = lm.lmder1(x);

  // check return value
  VERIFY_IS_EQUAL(info, 1);
  VERIFY_IS_EQUAL(lm.nfev, 6);
  VERIFY_IS_EQUAL(lm.njev, 5);

  // check norm
  VERIFY_IS_APPROX(lm.fvec.blueNorm(), 0.09063596);

  // check x
  VectorXd x_ref(n);
  x_ref << 0.08241058, 1.133037, 2.343695;
  VERIFY_IS_APPROX(x, x_ref);
}

{% endhighlight %}


## HLBFGS

[HLBFGS](http://research.microsoft.com/en-us/um/people/yangliu/software/hlbfgs/) is a hybrid L-BFGS(Limited Memory Broyden–Fletcher–Goldfarb–Shanno Method) optimization framework which unifies L-BFGS method, Preconditioned L-BFGS method and Preconditioned Conjugate Gradient method. With different options, HLBFGS also works like gradient-decent method, Newton method and Conjugate-gradient method. The original purpose I wrote this routine is for fast-computing large-scaled Centroidal Voronoi diagram. This is one of my favorite libraries and offers a big set of algorithms, for example, Gradient Decent, Conjugate Gradient, L-BFGS and M1QN3. I am making use of this library in my code and find it very stable. Here is an example that shows the easiness of the API

{% highlight c++ %}
#ifdef _DEBUG
#define _CRTDBG_MAP_ALLOC
#include <stdlib.h>
#include <crtdbg.h>
#endif

#include "HLBFGS.h"

#include "Lite_Sparse_Matrix.h"

#include <iostream>

Lite_Sparse_Matrix* m_sparse_matrix = 0;

//////////////////////////////////////////////////////////////////////////
void evalfunc(int N, double* x, double *prev_x, double* f, double* g)
{
    *f = 0;
    for (int i = 0; i < N; i+=2)
    {
        double T1 = 1 - x[i];
        double T2 = 10*(x[i+1]-x[i]*x[i]);
        *f += T1*T1+T2*T2;
        g[i+1]   = 20*T2;
        g[i] = -2*(x[i]*g[i+1]+T1);
    }
}
//////////////////////////////////////////////////////////////////////////
void newiteration(int iter, int call_iter, double *x, double* f, double *g,  double* gnorm)
{
    std::cout << iter <<": " << call_iter <<" " << *f <<" " << *gnorm  << std::endl;
}
//////////////////////////////////////////////////////////////////////////
void evalfunc_h(int N, double *x, double *prev_x, double *f, double *g, HESSIAN_MATRIX& hessian)
{
    //the following code is not optimal if the pattern of hessian matrix is fixed.
    if (m_sparse_matrix)
    {
        delete m_sparse_matrix;
    }

    m_sparse_matrix = new Lite_Sparse_Matrix(N, N, SYM_LOWER, CCS, FORTRAN_TYPE, true);

    m_sparse_matrix->begin_fill_entry();

    static bool first = true;
    double *diag = m_sparse_matrix->get_diag();

    if (first)
    {
        // you need to update f and g
        *f = 0;
        double tmp;
        for (int i = 0; i < N; i+=2)
        {
            tmp = x[i]*x[i];
            double T1 = 1 - x[i];
            double T2 = 10*(x[i+1]-tmp);
            *f += T1*T1+T2*T2;
            g[i+1]   = 20*T2;
            g[i] = -2*(x[i]*g[i+1]+T1);
            diag[i] = 2+1200*tmp-400*x[i+1];
            diag[i+1] = 200;
            m_sparse_matrix->fill_entry(i, i+1, -400*x[i]);
        }
    }
    else
    {
        for (int i = 0; i < N; i+=2)
        {
            diag[i] = 2+1200*x[i]*x[i]-400*x[i+1];
            diag[i+1] = 200;
            m_sparse_matrix->fill_entry(i, i+1, -400*x[i]);
        }
    }

    m_sparse_matrix->end_fill_entry();

    hessian.set_diag(m_sparse_matrix->get_diag());
    hessian.set_values(m_sparse_matrix->get_values());
    hessian.set_rowind(m_sparse_matrix->get_rowind());
    hessian.set_colptr(m_sparse_matrix->get_colptr());
    hessian.set_nonzeros(m_sparse_matrix->get_nonzero());
    first = false;
}
//////////////////////////////////////////////////////////////////////////
void Optimize_by_HLBFGS(int N, double *init_x, int num_iter, int M, int T, bool with_hessian)
{
    double parameter[20];
    int info[20];
    //initialize
    INIT_HLBFGS(parameter, info);
    info[4] = num_iter;
    info[6] = T;
    info[7] = with_hessian?1:0;
    info[10] = 0;
    info[11] = 1;

    if (with_hessian)
    {
        HLBFGS(N, M, init_x, evalfunc, evalfunc_h, HLBFGS_UPDATE_Hessian, newiteration, parameter, info);
    }
    else
    {
        HLBFGS(N, M, init_x, evalfunc, 0, HLBFGS_UPDATE_Hessian, newiteration, parameter, info);
    }

}
//////////////////////////////////////////////////////////////////////////
void main()
{
#ifdef _DEBUG
    _CrtSetDbgFlag(_CRTDBG_ALLOC_MEM_DF | _CRTDBG_LEAK_CHECK_DF);
#endif
    std::cout.precision(16);
    std::cout << std::scientific;

    int N = 1000;
    std::vector<double> x(N);

    for (int i = 0; i < N/2; i++)
    {
        x[2*i]   = -1.2;
        x[2*i+1] =  1.0;
    }

    int M = 7;
    int T = 0;

    //use Hessian
    // if M = 0, T = 0, it is Newton
    //Optimize_by_HLBFGS(N, &x[0], 1000, M, T, true);

    //without Hessian
    Optimize_by_HLBFGS(N, &x[0], 1000, M, T, false);  // it is LBFGS(M) actually, T is not used

    if (m_sparse_matrix)
        delete m_sparse_matrix;
}
{% endhighlight %}


## M1QN3

While using HLFBGS I noticed the excellent performance of MIQN3 algorithm. So started looking further and came across the [original fortran version](https://who.rocq.inria.fr/Jean-Charles.Gilbert/modulopt/optimization-routines/m1qn3/m1qn3.html). It is a part of [MODULOPT](https://who.rocq.inria.fr/Jean-Charles.Gilbert/modulopt/modulopt.html), a library for solving optimization problems and testing optimization software. The API in fortran for M1QN3 is shown below

{% highlight fortran %}
subroutine m1qn3 (simul, prosca, ctonb, ctcab, n, x, f, g, &
    dxmin, df1, epsg, normtype, impres, io, &
    imode, omode, niter, nsim, iz, dz, ndz, &
    reverse, indic, izs, rzs, dzs)
{% endhighlight %}

Here is what they say about this software in the website

> The routine M1QN3 has been designed to minimize functions depending on a very large number of variables (several hundred million is sometimes possible) no subject to constraints. It implements a limited memory quasi-Newton technique (the L-BFGS method of J. Nocedal) with a dynamically updated scalar or diagonal preconditioner. It uses line-search to enforce global convergence; more precisely, the step-size is determined by the Fletcher-Lemaréchal algorithm and realizes the Wolfe conditions.

I find it true. I have tried it with a problem of 1e6 dimensions and it worked. My problem was unimodal and with minor correlations between parameters. Analytical gradients were available as well.
