Information
==========================

### Authors 

This program has been written by 
- Chan Ieong Kuan (Sun Yat-Sen University)
- Didier Lesesvre (Universit√© de Lille)
- Xuanxuan Xiao (Macau MUST)

### Last update

December 2021

### Python and Sage versions

This code has been developed and runs under Sage 8.9, using Python 2.7.15. 

It does not properly run under Sage 9.2 and later, since it is based on Python 3 (the main issues are changes in the print command).

### Alternative URL

The GitHub URL for this program is
https://github.com/DidierLesesvre/mean-value-theorems

A stable URL for this program is 
http://lesesvre.perso.math.cnrs.fr/Code.zip	

The data used in this program can be either generated from scratch using the program (but this will take a while), or be directly downloaded at
http://lesesvre.perso.math.cnrs.fr/Data.zip	

The program
==========================

Introduction
----

### Presentation

The program is part of the "Growing powers" project, used to obtain explicit values in the paper "Sums of even ascending powers" by the same authors (arXiv identifier: https://arxiv.org/abs/2001.02429). This paper contains all the mathematical statements justifying this algorithms and the relevant references. 

Each function in the code (in the file generateLambdas.spyx) is fully commented, contains examples, references and further details. The present manual is made to present the overall setting of the program, explain the main functions implemented and used in the paper, and make the code understandable and usable in a short and self-contained way.

### Summary of the program's output

This program implement different mean-value algorithms (best-known at the current date, in different ranges). The aim of mean-value algorithms is to provide explicit exponents in bounds of L^2 norms of exponential sums.

More precisely, the code provides exponents \phi such that we have the mean-value bound (these lines have to be read as LaTeX code)

\int_0^1 |g_{k_1} \cdots g_{k_r}|^2 \ll G(0)N^\phi

where the g_k are exponential sums defined by

g_k(x) = \sum*_{n<N^{1/i}} e(x k^i)

where \sum* means that the sum is restricted to smooth numbers in this range (i.e. with no large prime factor, in a quantified way). Moreover, we put

G(0) = g_{k_1} \cdots g_{k_r} (0) = N^{1/k_1 + \cdots + 1/k_r}

The algorithm also accommodates that some functions are instead full sums, not only smooth sums (this is called mixed mean-value algorithms), then denoted f_k.

Running the program
------

### Setup

The source code of the program is in the file generateLambdas.spyx. In Sage, the source code can be read (this can take a minute or two to compile) with the command

> load("generateLambdas.spyx")

The algorithms we use are proven in papers of Vaughan and Wooley, and are mostly computation of crossed recursive sequences up to the desired precision. Running the algorithm may slow, and appeal many times to the same computations. In order to make it more efficient, intermediate values of the required iterations have been computed and stored in separate files provided with the code: lambdaListings.csv and nuListings.csv.

If you have already downloaded the generated data, enter the following line in Sage to read the data (this ma take a few minutes):

> [M, NL] = initialize()

If you do so, you can directly use the different functions below to obtain values of the exponent \phi.
Otherwise, replace this "Setup of the program" by the "Generate lambda listings" section at the end of this file, and all the subsequent ones.

### Mean-value exponents


Here we obtain a bound \phi such that we have the mean-value bound

> \int_0^1 |g_{k_1} \cdots g_{k_r}|^2 \ll G(0) N^\phi

where the g_k are smooth exponential sums defined above. This is exactly the paper's equations (3.7).

The exponent \phi is obtained from \lambda(k, a_k) exponents (provided by Vaughan-Wooley algorithms, see the paper for precise references) by equation (3.8). Many values of these \lambda(k, a_k) are stored in the listing lambdaListing.csv (loaded in the list M, in the current program).

The function to use has this signature:

> getPhi(K, M)

Here, K is the list of k's that you want to use in the g_k, and M is the lambda values you have read in at Setup part 2.

To recover the example in the case of Ford's paper (The Representation of Numbers as Sums of Unlike Powers. Journal of the London Mathematical Society 51 (1995), 14‚Äď26.), call:

> sage: getPhi([10, 13, 14, 15, 16], M)
> -0.3562744234482682

(Here, this means K=[10, 13, 14, 15, 16] which is indeed close to the value of Ford's paper (1995), equation (4.10)).

This function is used in our paper each time we need mean value theorems for product of *smooth* exponential sums.

### Mixed mean-value exponents


Here we obtain a bound \phi such that we have the mean-value bound

\int_0^1 |f_h g_{k_1} \cdots g_{k_r}|^2 \ll N^\phi

where the g_k are smooth exponential sums defined above, f_h is a full (non-smooth) exponential sum defined analogously. This is exactly the paper's equations (3.9).

The exponent \phi is obtained from \nu(h, k, 1/x_k) exponents (provided by Ford's interpolation algorithm with convex coefficients (x_k)_k, see the paper for precise references) by equation (3.10). Many values of these \nu(h, k, 1/x_k) are stored in the listing nuListing.csv (loaded in the list NL, in the current program).

The function to use has this signature:

> getNuPhi(M,h,K, NL=[])

Here M is the lambda values, NL the nu values (both may have been already downloading and initialized in the "Setup" section), h is the non-smooth exponent, K is the usual list of smooth exponents k.

We allow running this function with or without a list of nu values, NL. However, it is HIGHLY recommended to supply the list of nu values to speed up the process.

Example:

> sage: getNuPhi(M,4,range(6,19),NL)
> -0.9140858262569682

(This means K goes from 6 to 18. This closely matches the value page 938 of Ford 1996, first equation of the page.)

Generating the data
=======

Below are just things that you may only need to know if you are using this file for other variations of the problem and need to generate other ranges for the listings of values for \lambda(k, a_k) and \nu(h,k, 1/x_k)


### Generate Lambda Listings

If you ever need to generate lambda(k,s) values, the function to use is:

writeListToFile(k_low = 5, k_high = 200, s_high = 3000, writeFile = 'lambdaListings.csv')

There are default values we set, which gives a listing of lambda(k,s) with conditions:

- k: 5 to 200
- s: 1 to 3000

We set the default place to write the file is the base working directory of sage that you set up when installing sage,
with file name 'lambdaListings.csv'.

If you can live with the default values, just call:

> writeListToFile()

If you need different numbers, you can input your own values. Say we want:

- k: 5 to 1000
- s: 1 to 5000

If we still allow the default output file name, then we call:

> writeListToFile(5,1000,5000)

Depending on what values you put into the arguments, this may or may not take some few minutes.
Progress is printed on screen, so you can easily verify if things are running.

### Reading Lambda Listings


This step is STRICTLY necessary. It assumes you already have a file that contains the lambda values.

If you haven't changed the filename, just call the following to read in the values:

> M = readFromFile()

If you changed the filename (say it is 'abc.csv'), then call:

> M = readFromFile(['abc.csv'])

The end result is that M will contain lambda values. M[k][s] gives you [s, lambda(k,s)].

### Generating Nu Values

This step needs to be done if we want to optimize the speed of getting phi corresponding to nu(h,k,s)'s.

Since the files are relatively large, it may be advisable to generate them yourself.

The function signature is:

> writeNuListParallel(M, h_low=3, h_high = 15, k_low = 5, k_high = 200, up_mult = 5)

M contains the lambda values, coming from readFromFile(). Default values are given and they correspond to generating nu(h,k,s) for:

- h: 3 to 15
- k: max(h,5) to 200
- s: 1 to 5*k

We have fixed the name of the output files in this one. If default values suffice, just call:

> writeNuListParallel(M)

If you want to test a small case, you can do something like this:

> writeNuListParallel(M,3,8,5,30,3)

This runs with:

- h: 3 to 8
- k: max(h,5) to 30
- s: 1 to 3*k

This process will take LONG, but it is only a one-time deal (unless lambda values are improved/changed).
Again, progress is displayed on screen.

Side-comment: currently parallelism is not seemingly working. It should be further investigated...

### Reading Nu Values

Once you have written the nu values to file, you can read them in via the following function with signature:

readNuListParallel(h_low=3, h_high=15, readPrefix = 'nuListings')

The readPrefix argument should not be changed. It is just there for debugging purpose while developing the program.

All you need to change perhaps are the values of h_low, h_high, relating to the part of nu(h,k,s) values you want to read in.

The default mode is corresponding to the default in the generating part, and the read-in is done by calling:

> NL = readNuListParallel()

If you just want some particular ranges of h, say h=3 to 5, then just call:

> NL = readNuListParallel(3,5)

The end result is that NL contains nu(h,k,s) values, with NL[h][k][s] being [s, nu(h,k,s)].
