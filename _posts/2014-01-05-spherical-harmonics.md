---
layout: post
title: Open source spherical harmonics codes
description: "Representing data on the sphere and the codes to convert the sky into harmonic coefficients."
modified: 2014-01-05
tags: [spherical Harmonics, cosmology, mathematical methods,code, c, c++, fortran, CUDA, MPI, OpenMP, vectorization, FFTW, python]
image:
  feature: abstract-3.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

I deal with data on the (shperical) sky every day and one of the easiest way of computing statistics of the data is to reduce them into a set of power spectra. This technique relies on the availability of fast accurate Spherical Harmonic Transforms (SHT). Their applications range from the analysis of geographical and atmospherical data to cosmological surveys. Here is a small list of SHT codes that I tested as a part of my data analysis pipeline.

## LibSHARP

[LibSHARP](https://github.com/dagss/libsharp) is written in c99 with OpenMP and SSE2/AVX. There is also MPI support. It is used as a backend for the widely used [HEALPix](http://healpix.sourceforge.net/) library. More information can be found in [ArXiv](http://arxiv.org/abs/1303.4945). It supports many different grid-geometries such as HEALPix, Gauss-Legendre, Fejer and Clenshaw-Curtis. Both scalar and vector transforms are supported. Note that this library is the successor to the [libpsht](http://sourceforge.net/projects/libpsht/) library.

Most of the functionality can be loaded by three header files as shown below.

{% highlight c %}
#include <sharp_lowlevel.h>
#include <sharp_almhelpers.h>
#include <sharp_geomhelpers.h>
{% endhighlight %}

The helpers are useful in creating grids on the sky. For example a Gauss-Legendre grid can be created using the helper function.

{% highlight c %}
void sharp_make_gauss_geom_info (int nrings, int nphi, double phi0,
  int stride_lon, int stride_lat, sharp_geom_info **geom_info);
{% endhighlight %}

## SHTns

SHTns is a high performance library for Spherical Harmonic Transform written in c. The spatial data can be stored in latitude-major or longitude-major order arrays. More details and benchmarks can be found in [ArXiv](http://arxiv.org/abs/1202.6522). The code is parallelized by OpenMP. The main dependency is [FFTW](http://www.fftw.org/). API can be load by

{% highlight c %}
#include <shtns.h>
{% endhighlight %}

The library interface is extremely light, for example, the synthesis can be called by

{% highlight c %}
void SH_to_spat (shtns_cfg shtns,cplx * Qlm,double * Vr );
{% endhighlight %}

## SHTOOLS

[SHTOOLS](https://github.com/SHTOOLS/SHTOOLS/) is an archive of Fortran 95 and Python software that can be used to perform spherical harmonic transforms and reconstructions, rotations of data expressed in spherical harmonics, and multitaper spectral analyses on the sphere. There is a good amount of documentation [here](http://shtools.ipgp.fr/www/documentation.html). API is in fortran is straightforward, for example a synthesis  can be performed by

{% highlight fortran %}
call MakeGridGLQ (gridglq, cilm, lmax, plx, zero, norm, csphase, lmax_calc)
{% endhighlight %}

The above code will create a 2D map from a set of spherical harmonic coefficients sampled on the Gauss-Legendre quadrature nodes.

## S2HAT

S2HAT is derived from HEALPIx package but since v2.5 it is an independent library. The main features include memory scalability, linear speed-up, memory and load-balance optimization and applicability to any iso-latitude pixelization. Both scalar and vector transforms are supported. What is fascinating about this code is that it has a CUDA version. An example CUDA synthesis API is shown below

{% highlight c %}
void s2hat_alm2map_cuda (
    int plms, // no use
    s2hat_pixeltype pixelization,
    s2hat_scandef scan,
    int nlmax,
    int nmmax,
    int nmvals,
    int* mvals,
    int nmaps, // no use
    int nstokes, // no use
    int first_ring,
    int last_ring,
    int map_size,
    double *local_map,
    int lda,
    s2hat_dcomplex *local_alm,
    int nplm, // no use
    double* local_plm, // no use
    int numprocs,
    int myid,
    MPI_Comm comm);
{% endhighlight %}
