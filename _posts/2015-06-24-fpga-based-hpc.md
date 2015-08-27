---
layout: post
title: "FPGA for scientific computing"
description: "Scope of FPGAs in scientific computing"
tags: [scientific computing, OpenCL, compilers, c, c++, FPGA, VHDL, Markov Chain Monte Carlo, BLAS]
modified: 2015-06-24
---

FPGA or Field-Programmable Gate Array is the latest addition to crazy world of HPC. It is an  an integrated circuit designed to be configured by a customer or a designer after manufacturing. The FPGA configuration is generally specified using a hardware description language (HDL). However, several vendors are now offering OpenCL based API. The idea that you have custom hardware for a specific algorithm, for example, Fast Fourier Transform or Markov Chain Monte Carlo is breathtaking.

## MCMC
I am seeing more and more papers on this approach recently. For example, take [this](http://cas.ee.ic.ac.uk/people/ccb98/papers/MingasARC11.pdf) paper by Mingas & Bouganis. They report 1000X speedup for a Parallel Tempering Algorithm. They have implemented the code in [VHDL](https://en.wikipedia.org/wiki/VHDL) on a Virtex-6 FPGA. Here is a plot from the paper

![speedup](/images/fpga_speedup_mcmc.png)

The code however is not public yet.

## BLAS
Another interesting [paper](http://research.microsoft.com/pubs/130834/isvlsi_final.pdf) was on a BLAS implementation on FPGA by Kestur, Davis and Williams. Their conclusion is extremely interesting: "Results show that FPGAs offer comparable performance as well as 2.7 to 293 times better energy efficiency for the test cases that we implemented on all three platforms.". Here is a plot from their paper

![time-energy](/images/fpga_avg_time_energy.png)
