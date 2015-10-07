#Recommender Systems

The notion of having a product suggested to a user by a computer has become pervasive in everyday life. From Netflix to Amazon, "smart" suggestions on what to buy have appeared, with the underlying models and machines gaining wisdom from various techniques such as collaborative filtering.

##The Goal
A key component of collaborative filtering is the similarity metric used to determine whose tastes are most like a given person's. This is where parallelism comes into play. While the suggestion mechanism remains constant, a dataset for product recommendations can become large. More specifically, various operations would be performed on a matrix of users and items they "favorite". Seeing as iterating through the matrix can be costly, optimizing the similarity metrics should speed up the process. The aim of this is to test various similarity metrics to create a speedy implementation that does not sacrifice the quality of recommendations.

## Similarity Metrics (Parallel Versions)
1. Euclidean distance
2. Cosine similarity (?)
3. Locality-sensitive hashing

##References
http://istc-bigdata.org/plsh/docs/plsh_paper.pdf