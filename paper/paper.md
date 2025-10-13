---
title: "pyaudi: A truncated Taylor polynomial algebra toolbox for differentiable intelligence, automatic differentiation, and verified integration applications."
tags:
  - Python
  - differentiable intelligence
  - automatic differentiation
  - verified integration
authors:
  - name: Dario Izzo
    affiliation: "1"
    orcid: 0000-0002-9846-8423
  - name: Francesco Biscani
    affiliation: "2"
  - name: Sean Cowan
    affiliation: "1"
    orcid: 0009-0000-9912-9354
affiliations:
  - index: 1
    name: Advanced Concepts Team, European Space Research and Technology Center
  - index: 2
    name: Skylon Dynamics
date: 13 October 2025
bibliography: paper.bib
---

# Summary

<!-- A summary describing the high-level functionality and purpose of the software for a diverse, non-specialist audience. -->

`pyaudi` is an open-source Python toolbox developed at the [European Space Agency](https://www.esa.int) that provides high-order, forward-mode automatic differentiation in a multivariate setting. The toolbox is built on C++ class templates, exposed to Python via pybind11, and, at its core, implements the algebra of truncated Taylor polynomials. This design allows the underlying generalized dual number type to act as a seamless drop-in replacement for scalar types such as floats, while operator overloading ensures that derivatives are propagated automatically. The C++ code base `audi` can also be used directly and allows for greater flexibility in the instantiation of the algebra over different fields.

All standard mathematical operations are supported, leveraging the nilpotency of exponentiation in the algebra of truncated Taylor polynomials. In essence, within a truncated algebra $\mathcal A^n$, any polynomial $p \in \mathcal A^n$ with vanishing constant term evaluates to zero when raised to a power greater than the truncation order ($m > n$). This property enables efficient and exact computation of derivatives to arbitrary order while maintaining the simplicity of standard numerical code.

On top of the algebra of truncated Taylor polynomials, `pyaudi` also offers an implementation of Taylor models [@makino1998rigorous], which combines truncated Taylor
polynomials with an interval bounding their truncation error as well as a number of miscellaneous algorithms useful for applications in differential intelligence,
high-order automatic differentiation, verified integration and more.


# Statement of need

<!-- A Statement of need section that clearly illustrates the research purpose of the software and places it in the context of related work. -->
`pyaudi` enables users to compute and manipulate order-$n$ Taylor expansions of generic computational
graphs, while also providing rigorous bounds on the truncation error through their associated Taylor models. 
These Taylor representations of program outputs can be exploited in a variety of ways, including fast Monte Carlo 
simulations, rigorous uncertainty analysis, local inversion of output–input relations, and high-order 
sensitivity studies. The package implements the high-order automatic differentiation methodology originally 
developed by Berz and Makino ([@berz2014introduction], [@makino1998rigorous]), while introducing 
novel implementation details in polynomial multiplication routines and in the bounding 
of Taylor models.

# Existing libraries with similar capabilities

As of the time of writing, there are two main open source libraries that allow to perform similar computations
to those allowed by `pyaudi`. The first one is the C/C++ library DACE [@massari2018differential] implementing the
full differential algebra of truncated Taylor polynomials with float coefficients. Unlike `pyaudi`, DACE relies on
a polynomial multiplication routine that makes extensive use of memory for the storage 
of monomial coefficients. As discussed in the comparison reported below, this approach gives DACE an advantage 
for single evaluations at lower orders, with the benefit diminishing as computations are performed in batches and at high orders.

A second relevant project are the Julia libraries TaylorSeries.jl and TaylorModels.jl [@benet2019taylormodels] providing implementations 
of Taylor models to compute rigorous bounds on generic Taylor series. However, their underlying approach differs 
substantially from that of `pyaudi`, and preliminary comparisons presented here indicate that `pyaudi` can 
significantly outperform these libraries in the practical cases tested.

In the field of machine learning, in recent years a plethora of automatic differentiation toolboxes have risen tailored
at the efficient execution of machine learning tasks and optimization in general. JAX [@jax2018github], TensorFlow [@tensorflow2015-whitepaper], PyTorch [@paszke2019pytorch] (autograd)
are perhaps the most widely adopted. For low order derivatives (first and second order only) these are very efficient and should be used
as they implement efficiently reverse mode automatic differentiation which for these tasks is often superior. For higher order derivatives, 
instead, most implementation suffer greatly and, although some experimental features start to be available (see JAX's jet feature [@bettencourt2019taylor]),
they are still not mature enough.

## Key aspects

The main features of `pyaudi` are:

- **Truncated polynomial algebra**, powered by [Obake](https://github.com/bluescarni/obake), 
  a C++ library for symbolic manipulation of sparse multivariate polynomials, truncated power series, and Poisson series. 
  Unlike other packages, which often suffer from severe memory bottlenecks as the polynomial order or the number of variables increases, 
  `pyaudi` avoids large static memory allocations by adopting a sparse, dynamic approach. This remains memory-efficient at the cost of additional bookkeeping, 
  where sparse polynomials are the area of greatest benefit. The use of templates allows to instantiate the algebra over different fields such as floats, quadruple
  precision floats, vectorized floats, etc..

- **Vectorized coefficients**, enabling the simultaneous evaluation of identical computational graphs 
  at multiple expansion points. This feature makes it possible to compute high-order derivatives on multiple points, 
  while alleviating the overhead introduced by the sparse bookkeeping of [Obake](https://github.com/bluescarni/obake).

- **Taylor models with Bernstein polynomial bounding**, used to enclose the range of multivariate polynomials. 
  
- **Map inversion algorithm**, implementing the method described in [@berz2014introduction]. 
  This feature enables the local inversion of input–output relations arising in generic computational graphs.

A performance comparison was conducted against DACE for gduals, and against TaylorModels.jl for
Taylor models, of which the results can be found in paper_results.md on the paper branch.


# Ongoing research

<!-- Mention (if applicable) a representative set of past or ongoing research projects using the software and recent scholarly publications enabled by it. -->

[@acciarini2024stochastic] extended the principle of numerical continuation to find solutions in non-linear
dynamical systems in the space domain to the moments of a probability density function, using `pyaudi`
to calculate the high-order derivatives in the Circular Restricted Three-Body Problem (CR3BP). Similarly, `pyaudi` was used in [@acciarini2025nonlinear]
for various test cases involving non-Gaussian uncertainty distributions to calculate the high-order derivatives.
[@caleb2020uncertainty] did work on exploring Differential Algebra (DA) for accelerating Monte Carlo simulations,
which is enabled by libraries such as `pyaudi`. For on-board applications, `pyaudi` was used in [@burnett2025rapid]
to implement convex guidance algorithms by describing the solutions using DA. [@izzo2017differentiable]
explored Cartesian Genetic Programming and made it differentiable with high-order automatic differentiation,
enabled by `pyaudi`.

# Acknowledgement of financial support

No financial support was provided for the development of this software.

# Bibliography
