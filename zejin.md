# Using RngStreams for parallel random number generation in C++ and R

Simulation studies are in many respects the ideal application for parallel processing.
They are “naturally parallel” in the sense that computations may generally be conducted
without the need for inter-processor communication or modification of the base algorithm. 
Thus, near linear speedup can be realized in many cases that occur in practice.

However, in order for a division of labor across multiple processors to be productive, 
the random number streams being used by each processor must behave as if they are independent in a probabilistic sense.

Although the relationship between streams will likely be much complex in situations that arise in practice, 
the message remains the same: inter-stream dependence can sabotage a parallel random number generation scheme.

## Methods

Hill (2010) reviews available methods for parallel random number generation, including

1. Random spacing
2. Sequence splitting 
3. Cycle division
4. Parametrization

#### Random spacing
Workers are initialized to randomly spaced positions on the period of the same generator by assigning different, randomly generated seeds.

#### Sequence splitting 
Dividing a sequence into non overlapping contiguous blocks.

#### Cycle division 
A more involved technique for sequence splitting where the period of a generator is deterministically divided into segments.

#### Parametrization
Parameters associated with a generator are varied to produce different streams.

## Generators

1. R’s default random uniform generator: Mersenne Twister (MT) generator with random spacing
2. RngStreams package: multiple recursive generator (MRG) with cycle division 
3. SPRNG package: multiplicative lagged Fibonacci generator with parametrization

#### Mersenne Twister (MT) generator with random spacing - 
The random spacing method carries a risk of overlap between random streams on different processors with
an unlucky sampling of seeds, although the risk is small when using generators with large periods.

MT provides one example of a long-period generator with the period 2^19937 − 1 (Matsumoto and Nishimura 1998). 
This feature along with its widespread availability has made MT a popular choice for parallel generation via random spacing.

The work of Matsumoto et al. (2007) shows that many modern random number generators exhibit correlations if their initial states are chosen using another linear generator with a similar modulus. In addition, Random spacing and sequence splitting
may result in random number streams that exhibit undesirable dependence properties.

#### Multiple recursive generator (MRG) with cycle division
A method such as cycle division comes with a guarantee that the streams will not collide, 
whereas random spacing only renders such events unlikely.

Many of the problems with congruential generators can be avoided by using combined
multiple recursive generators (CMRG) with cycle division.

A MRG is roughly equivalent to a multiple recursive generator with a period that can be as large as the
product of the periods of the generators used in the combination (L’Ecuyer and Tezuka 1991).

With a good choice for the parameters (L’Ecuyer 1999), these combined generators have also
fared well when subjected to statistical tests as in L’Ecuyer and Simard (2007). 

As a case in point, the MRG32k3a generator that provides the backbone generator for the
RngStreams package developed by L’Ecuyer et al. (2001) is known to have good
theoretical and statistical properties. It is cited by Lemieux (2009), along with MT, as
a generator that can be “safely used”.

Of course, cycle division becomes an option only if it can be done efficiently. For
multiple recursive generators L’Ecuyer (1990) has shown that there are computable
matrices that can be used to advance the state of the generator to any specified point
in its associated random number stream. This feature is what makes the combination
of generators easy to use in a cycle division paradigm. The RngStreams package
gives an implementation of this approach in the context of its MRG32k3a generator.

The RngStreams package is quite compact with all its source code available
from [RngStreams](http://www.iro.umontreal.ca/~lecuyer/myftp/streams00/).

#### 

Another noteworthy package that provides functionality for parallel random number
generation is the SPRNG package of Mascagni and Srinivasan (2000). In contrast to
RngStreams, it produces different random number sequences for the processes
via parametrization of a single type of generator.

For example, one of the package’s
featured generators is a multiplicative lagged Fibonacci generator whose states fall into 2^1007 different equivalence classes of generators (each of period 281) that provide
the different streams.

L’Ecuyer et al. (2001) in reference to the SPRNG package
state that it is “not supported by the same theoretical analysis for the quality and
independence of the different streams” as RngStreams. There are also practical
difficulties that arise in using SPRNG due to its ties to MPI and a rather complex
implementation that necessitates the creation and linking of a compiled library.

## Goal

The goal of this article is to illustrate how easily RngStreams can be employed in
parallel settings in C++ with OpenMP and MPI and in R (Core Team 2013) through the
rstream and parallel packages.

1. Use RngStreams with C++ programs that are parallelized via OpenMP or MPI
2. Use RngStreams in R through the parallel and rstream packages
3. Run a simple example and a Monte Carlo integration application
