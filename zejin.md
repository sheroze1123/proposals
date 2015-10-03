# Using RngStreams for parallel random number generation in C++ and R

In order for a division of labor across multiple processors to be
productive, the random number streams being used by each processor must behave
as if they are independent in a probabilistic sense.

inter-stream dependence can sabotage a parallel random
number generation scheme.



## Methods

Available methods:

1. Random spacing 
2. Sequence splitting 
3. Cycle division 
4. Parametrization 

### Random spacing

Workers are initialized to randomly spaced positions on
the period of the same generator by assigning different, randomly generated seeds

The random spacing method carries a risk of overlap between random streams on different processors with
an unlucky sampling of seeds, although the risk is small when using generators with large periods.

### Sequence splitting

dividing a sequence into non overlapping contiguous blocks

### Cycle division

a more involved technique for sequence splitting where the period of a generator
is deterministically divided into segments

### Parametrization

parameters associated with a generator are varied to produce different streams


## Generators

1. MT
2. 

Issues that arisewhen using linear congruential generators for parallel random number
generation have been well documented. Random spacing and sequence splitting
may result in random number streams that exhibit undesirable dependence properties

### R’s default Mersenne Twister (MT) random uniform generator

a long-period generator with the period 2^19937 − 1

This feature along with its widespread availability 
has made MT a popular choice for parallel generation via random spacing.

many modern random number generators exhibit correlations if their initial states are chosen using
another linear generator with a similar modulus.

### MRG

Many of the problems with congruential generators can be avoided by using combined
multiple recursive generators (CMRG) with cycle division. A MRG is roughly
equivalent to a multiple recursive generator with a period that can be as large as the
product of the periods of the generators used in the combination

MRG32k3a generator that provides the backbone generator for the RngStreams package developed by L’Ecuyer et al. (2001) is known to have good
theoretical and statistical properties.

It is cited by Lemieux (2009), along with MT, as
a generator that can be “safely used”.

RngStreams package is quite compact with all its source code available
from http://www.iro.umontreal.ca/~lecuyer/myftp/streams00/.

### SPRNG package

it produces different random number sequences for the processes
via parametrization of a single type of generator. For example, one of the package’s
featured generators is a multiplicative lagged Fibonacci generator whose states fall
into 2^1007 different equivalence classes of generators (each of period 2^81) that provide
the different streams.

For example, L’Ecuyer et al. (2001) in reference to the SPRNG package
state that it is “not supported by the same theoretical analysis for the quality and
independence of the different streams” as RngStreams.

There are also practical
difficulties that arise in using SPRNG due to its ties to MPI and a rather complex
implementation that necessitates the creation and linking of a compiled library.

## Our

RngStreams is easy
to incorporate into C/C++ programs using OpenMP or MPI through simple include
directives. This property extends its applications from cluster down to desktop environments.

the RngStreams generators become available in
R through the rstream package (Leydold 2012). They are also the default generator
for the parallel package that provides multithreading capabilities and now comes
as part of the R base software.

The goal of this article is to illustrate how easily RngStreams can be employed in
parallel settings in C++ with OpenMP and MPI and in R (Core Team 2013) through the
rstream and parallel packages.

