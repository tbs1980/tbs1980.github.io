---
layout: post
title: "Integrating github projects with Travis-CI"
description: "How to integrate github project with Travis-CI?"
tags: [continuous integration, Travis-CI, github, c, c++]
modified: 2015-03-28
---

Continuous integration is extremely useful when developing a project with multiple collaborators. Travis-CI platform offers an excellent service for github based projects. I wanted to create and example project and see what are the steps required for integrating github project with Travis-CI.

## Overview

Here is an excellent summary of Continuous Integration from [Marko Bonaci](https://github.com/mbonaci/mbo-storm/wiki/Integrate-Travis-CI-with-your-GitHub-repo)

>Continuous Integration is the umbrella for various techniques and practices that every self-respecting software project should employ.The main goal is to eliminate the long and tedious integration process, the work that you normally have to do between version's final development stage and its deployment in production. So you routinely perform (or rather, CI performs for you) the same sequence of steps, thus making the whole process polished and reliable.

The main features of Travis-CI agree

1. Automatically detects when a commit has been made and pushed to a GitHub repository that is using Travis CI, and each time this happens,
2. It will automatically try to build the project and run tests.
3. Project management integration
4. Project Dashboard -> everything is out in the open (generally without any read access restrictions, making it available to all interested parties)
5. Supports a wide range of languages,

## Integrating a GitHub project

If the project is GitHub the integration is very easy. Here are the basic steps I followed

1. Sign in to [Travis-CI](https://travis-ci.org/) using a GitHub id
2. In the upper right corner click on your name (or choose `Accounts`) to open your Travis-ci profile. Then you will be able to see the GitHub repositories. The sync toggle-slider will enable
3. Now in the GitHub local repo, create a `.travis.yml` file. You can see an example [here](https://github.com/tbs1980/TravisCI/blob/master/.travis.yml). More documentation can be found [here](http://docs.travis-ci.com/user/getting-started/). If you have more then one language in the code you may have to be a bit creative and do something like [this](https://github.com/tbs1980/Ellipsis/blob/master/.travis.yml)
4. As soon as you push this to GitHub, you will see that the build is happening in your Travis-CI Dashboard.

{% highlight yaml %}
language: cpp

compiler:
    - gcc

before_install:
    - echo $LANG

before_script: cmake CMakeLists.txt

script:
    - $CXX --version
    - make
{% endhighlight %}


You can even embed the build status in the README.md of your project. See the documentation [here](http://docs.travis-ci.com/user/status-images/)
