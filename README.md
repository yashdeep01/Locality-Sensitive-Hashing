# Locality-Sensitive-Hashing

## Introduction
This is an implementation of LSH, a popular IR algorithm to bunch similar items together.

## Dataset
I used a corpus of news articles of the period 2016-17 from kaggle. It consists of 20,000+ news articles from esteemed international dailies like NYT, The Guardian, CNN, etc.

## Task
Our task was to identify similar pairs of articles. We use locality sensitive hashing to identify similar pair of news articles in this assignment. The corpus was retrieved from https://www.kaggle.com/snapcrack/all-the-news. The data is given in the form of three .csv files. The file contain docID, Title, Publication, Author Date, Year, month, and content. The relevant information for this assignment is found under “content” header. 

## Tokenising
The articles are present in a text format and naturally contain various linguistic aberrations of root words in English. Our first task is to tokenise the text into space-separated words. We do this by using word_tokenize() method of NLTK library in python. Now, our document is a list of many words, punctuations, numbers, and possibly non-ascii values. Our next task is to lemmatise these tokens.

## Lemmatisation
Now, we intend to convert these tokens into lemmas. We use NLTK library for this task, which was used for tokenisation as well. Before lemmatising the words, we remove all punctuations and lower case all the words. After this we remove stop words (of, at, the, an, a, etc). This would make it easier for shingling and would help resolve the problem of false positives. Then, we convert all numerals to words. This is important because in order to distinguish articles, writers tend to use this trick. Thus, our tokens are said to be normalised.

Next, we form lemmas out of the given words. We use lemmatizer.lemmatize() of NLTK. 

## Shingling
We use 3 words as a shingle. Since, the news sources are reputable and hence it would be hard to find exact duplication of articles, we use whole words in shingle, as opposed to n-letter shingle. In the process of shingling, we hash the shingles to 32-bit integers. We store the set of shingles for each document in a dictionary to preserve the document id to fetch the document later. This is the major time consuming step, since every document is lemmatised, hashed, and shingled. However, this is the pre-processing bit. 

We don’t create a characteristic matrix as the matrix would be very sparse and would increase space and time complexity. Instead we store the set of shingles as the postings list of each document, although the dictionary is name char_matrix to make the program intuitive.

## Minhashing
After shingling the documents and making a dictionary of shingles, we make the signature matrix. We have chosen the number of hash functions for minhashing to be 128. Our hash functions are of the form (a*x + b)%c, where a and b are random coefficients less than the largest hashed value of shingles and c is a prime, which is the first prime greater than the number of shingles. We have used Miller-Rabin Primality Test to determine whether the next prime greater than the number of shingles is prime or not. We find the smallest hash value for each function and insert into the signature matrix.
Again we have stored the signatures in a dictionary to preserve document information for our perusal.

## LSH
Here we use the AND-OR construction. We enter the size of bands from the user and perform a built-in hash function from python. We form pairs of similar documents and output both. We output the S-shaped curve also for detecting the amount of false positives and negatives. 

This approach was for Jaccard distance. We apply a similar approach for cosine distance too by making random vectors and taking dot products to form the signature matrix. We do a comparison later. We perform a query too. 
