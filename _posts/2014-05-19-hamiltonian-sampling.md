---
layout: post
title: "Hamiltonian sampling"
description: "Hamiltonian sampling for large scale interface"
tags: [Hamiltonian sampling, Markov Chain Monte Carlo, c++, fortran,CUDA]
modified: 2014-05-19
image:
  feature: abstract-6.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

One of the problems I am interested is the power spectrum estimation of cosmological data in the sky. Hamiltonian can be very effective here in sampling the large dimensional (~10^6) posterior probability distributions. If the posterior distribution is unimodal this technique can be very effective. I have written several codes (in C,fortran and CUDA) which can be found here. CUDA version actually outperforms CPU version as long as as one can keep all the data inside the GPU memory.

Here are some papers I found very useful for understanding the fundamental concepts of Hamiltonian Monte Carlo (HMC)

1. [Neal, 2011, *MCMC using Hamiltonian dynamics*](http://arxiv.org/abs/1206.1901v1)
2. [Beskos et al. 2010, *The Acceptance Probability of the Hybrid Monte Carlo Method in High-Dimensional Problems*](http://scitation.aip.org/content/aip/proceeding/aipcp/10.1063/1.3498436)
3. [Girolami & Calderhead, 2011, *Riemann manifold Langevin and Hamiltonian Monte Carlo methods*](http://doi.wiley.com/10.1111/j.1467-9868.2010.00765.x)
4. [Hanson, 2001, *Markov chain Monte Carlo posterior sampling with the Hamiltonian method*](http://link.aip.org/link/?PSI/4322/456/1&Agg=doi)
5. [Hoffman & Gelman, 2014, *The No-U-Turn Sampler: Adaptively Setting Path Lengths in Hamiltonian Monte Carlo*](http://arxiv.org/abs/1111.4246)

I started with a fortran code which can be found [here](), then moved to a c version called [Ellipsis](https://github.com/tbs1980/Ellipsis) and finally created a [CUDA version](). For the CUDA version I get really big speed up. You can see the plots below.
