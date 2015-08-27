---
layout: post
title: In love with CMake
description: "Exploring the capabilities of CMake"
tags: [CMake, build automation, linux, osx, windows, make, compilers, GNU, Intel, clang, MPI, OpenMP, CUDA, OpenCL, python, numpy, scipy, LaTeX, Doxygen]
modified: 2014-10-04
image:
  feature: abstract-7.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

I am absolutely in love with CMake. For years I have been looking at automating the cross platform build-process. There are may tools made for this process. I think CMake is one of the best in the lot. Here are a few stunning capabilities of CMake that I find extremely useful.

## Features of CMake

1.  can handle in-place and out-of-place builds
2. ability to build a directory tree outside the source tree
3. ability to generate a cache to be used with a graphical editor
4. can locate executables, files and libraries
5. able to accommodate a project that has multiple toolkits, or libraries that each have multiple directories
6. can work with projects that require executables to be created before generating code to be compiled
7. *open-source*
8. *CMake can generate makefiles for many platforms and IDEs*

From a scientific computing perspective, the following features are excellent

1. support for MPI, OpenMP, CUDA and OpenCL
2. support for multiple compilers, e.g., GNU, Intel, clang
3. able to work with python, numpy, scipy etc
4. able to work with LaTeX
5. support for Doxygen
