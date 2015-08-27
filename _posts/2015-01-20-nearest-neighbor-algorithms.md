---
layout: post
title: "Finding nearest neighbors in large dimensions"
description: "Codes for computing nearest neighbors"
tags: [nearest neighbor search,c++,python,R,fortran,c,GPU]
modified: 2015-01-20
---

In my previous post I wrote about my experience with writing a KDE code. One of the biggest challenges was to optimally select the points in space which are "relevant" to KDE given a random point. Of course one can select all points to the "best" estimate given the set of samples. However, this may be computationally expensive when the dimensionality of the problem is high and the number of samples are very large. In such situations on may need to choose the best points for KDE given a kernel. This can be achieved by choosing set of nearest neighbors for computing the KDE. Again I was exploring the github landscape for nearest neighbor algorithms.

# Nearest Neighbors in github

There are plenty of codes in github that implements the nearest neighbors. Here is a small list

1. [https://github.com/mariusmuja/flann](https://github.com/mariusmuja/flann)
2. [https://github.com/spotify/annoy](https://github.com/spotify/annoy)
3. [https://github.com/aaalgo/kgraph](https://github.com/aaalgo/kgraph)

I found `flann` extremely light and easy to use. The only dependency in Eigen. An example from `flann` showing the API is here

{% highlight c %}
#include <flann/flann.hpp>
#include <flann/io/hdf5.h>

#include <stdio.h>

using namespace flann;

int main(int argc, char** argv)
{
    int nn = 3;

    Matrix<float> dataset;
    Matrix<float> query;
    load_from_file(dataset, "dataset.hdf5","dataset");
    load_from_file(query, "dataset.hdf5","query");

    Matrix<int> indices(new int[query.rows*nn], query.rows, nn);
    Matrix<float> dists(new float[query.rows*nn], query.rows, nn);

    // construct an randomized kd-tree index using 4 kd-trees
    Index<L2<float> > index(dataset, flann::KDTreeIndexParams(4));
    index.buildIndex();

    // do a knn search, using 128 checks
    index.knnSearch(query, indices, dists, nn, flann::SearchParams(128));

    flann::save_to_file(indices,"result.hdf5","result");

    delete[] dataset.ptr();
    delete[] query.ptr();
    delete[] indices.ptr();
    delete[] dists.ptr();

    return 0;
}
{% endhighlight %}
