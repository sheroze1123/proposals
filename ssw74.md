# Position Based Fluids

Scott Wu (ssw74)

In Physically Based Animation, we worked on a [fluid simulation](http://www.cs.cornell.edu/courses/cs5643/2015sp/a1PositionBasedFluids/index.html) using Smoothed Particle Hydrodynamics. Specifically, we followed two papers, [Particle-Based Fluid Simulation for Interactive Applications [Muller et. 2003]](http://matthias-mueller-fischer.ch/publications/sca03.pdf) and [Position Based Fluids [Macklin, Muller 2013]](http://mmacklin.com/pbf_sig_preprint.pdf), on implementing SPH with a framework called Position Based Dynamics. The project starter code was based in Java and was not focused on performance, although the advantage of the algorithm described in the paper is that many portions of the framework are parallelizable.

## Algorithm Outline

#### Setup and Initialization

We begin with an array of particles, each with a mass, radius, position, velocity and acceleration. Mass and radius are typically constants. Velocity and acceleration are initialized to 0, and position is defined by an input file.

We also define a few kernel functions. These functions describe the strength of relationships between neighboring particles.

Finally, we define some constraints that dictate particle behavior. The main constraint is incompressibility - maintaining density throughout the fluid volume. The second constraint is boundary conditions, which includes walls and rigid bodies within the simulation volume.

#### Simulation Loop

1. First take an Eulerian step and predict a position x'
2. Use a grid to find neighboring particles within a certain radius
3. Iteratively correct our predicted position x' using constraints
    1. Per iteration of the solver loop, attempt to solve incompressibility constraints
    2. Maintain boundary conditions
    3. Update the predicted position with those constraints
4. Using the new predicted position, update the particle velocity ((x' - x) / dt)
5. Apply vorticity confinement to maintain energy
6. Apply viscosity for a sort of 'blurred' motion (coherence)
6. Finally, update the particle position with the predicted position

Each step of the simulation loop (including the inner solver loop) must be done serial. Within each step however, we deal with hundreds of thousands of particles which may be computed in parallel.

## Tasks

1. Familiarize with the Position Based Fluids paper
2. Port a Java codebase to C/C++ and verify
3. Perform serial optimizations to the simulation
4. Perform parallel optimizations to the simulation
5. Time, analyze and render test cases
