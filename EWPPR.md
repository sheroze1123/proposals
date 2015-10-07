#Edge-Weighted Personalized PageRank

Google has been famous of their PageRank algorithm. The general idea is to rank all the webpages based on their "influence", and the "influence" is signified by how many pages directs to it and how many it directs to.

PageRank problems can be abstracted as a linear system problem. Provided a graph of vertices (webpages) and edges (links), a PageRank matrix is a column stochastic matrix where the elements represent the probability of a web-surfer transporting from one webpage to another. The largest eigenvalue of this matrix is always 1, and its corresponding eigenvector is the "rank" value of each vertex. Thus by solving an linear equation one can obtain the rank values of all webpages in the graph.

However, solving the PageRank problem is computationally expensive, since we are talking about billions of webpages, not to mention that webpages are dynamic and constantly updating. Due to the heavy cost, Google only update their PageRank results only a few times per year.

Another issue is, this algorithm does not support personalized searches. That means, if you are particularly interest in the links from a news website to a sports website, google will not be able to do that for you. All it does is to treat all links evenly, which statistically should satisfy most people's demands.

Our project focuses on factoring in personalized parameters and aims at quickly solving PageRank problems.

##Background
There are a couple of parameters involved in a PageRank problem, including the PageRank matrix itself, a teleportation factor that indicates the probability of a web-surfer jumping to a random page, and a transportation vector that indicates when going into a sink (a webpage that has no links out) where the web-surfer would go. Modifying the teleportation factor and the transportation vector is relatively cheaper to do, and was the common solution for personalized PageRank problems in the past few years. However those results are only approximations to what people exactly want. Modifying the PageRank matrix is more computationally expensive, and remains an open problem these years.

Recently, W. Xie and D. Bindle et al. published their [fast algorithm](http://wenleix.github.io/paper/edgeppr.pdf) for computing PageRank on general graphs in which edge weights are personalized. The basic idea of the method basses off a novel model reduction strategy, where a reduced model is constructed offline, whereas it allows fast evaluation online. So far the authors bring up two approaches for generating the reduced space -- `Bubnov-Galerkin approach` and `Discrete Empirical Interpolation Method` (DEIM). And our job is to implement those two methods.

The sample database we are currently working on is a [Citation Network Dataset](https://aminer.org/billboard/citation) extracted from DBLP, ACM and other sources. We just built the skeleton of the demo, and the smallest dataset contains more than 2 million vertices and 4.5 million edges. Parsing the dataset is a task that takes almost 2 hours, and computing the PageRank problem out of this will only take much longer, and herein we need to make use of what we learned in CS 5220 and accelerate the computation by parallelism.

##Tasks
We are working with million-level matrices, but actually we will not spent much time handling basic matrix-matrix multiplications etc. We fully trust the well-developed packages to maximize the efficiency in those parts. Instead, we will intensively optimize the data storage and the logic of operations. Currently we found the following parts could be optimized:

1. Sparse matrix storage. 

    Right now our code is in Python (with the probability of switching to C++ later), and the well-known `scipy.sparse` library provides multiple sparse matrix formats, each of which has advantages and disadvantages. When dealing with a million-level matrix, we must carefully arrange data for different purposes. For instance, `coo_matrix` is best for initiating the matrix, but `csc_matrix` is most suitable for matrix-vector multiplication. We will take a look at this and come up with good plans.
    
2. Parallelize algorithms for linear systems

    We are going to solve linear problems with Gaussian Elimination, LU factorization, QR factorization and SVD. It would be a great idea to divide the 2 million matrix into blocks and run them in parallel. 
    
3. Benchmark the performance.

    We hope to see the performance of our implementation outperform other existing methods by *nearly five-order of magnitude*!

4. Move on to other public datasets

    We wish to use our code to answer queries towards other public datasets (such as tweets data). We would love to see our project making some difference.
    
Note that we do not anticipate to accomplish all the listed tasks, any subset of these tasks should suffice the requirement of CS 5220 final project, and we are happy to talk about it.

##Members
Our team currently consist of two members, Xiangyu Zhang (xz388) and Markus Salasoo (ms933). This project is also an important precursor of our CS M.Eng. project. Prof. Bindle serves as our advisor on this project.

We will be happy to welcome another one or two group members, preferably someone with good understanding of PageRank model and relatively good numeric background.

