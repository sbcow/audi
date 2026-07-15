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

`pyaudi` is an open-source Python toolbox developed at the [European Space Agency](https://www.esa.int) that provides high-order forward-mode automatic differentiation in
a multivariate setting. Built on C++ class templates and exposed to Python via pybind11, it implements the algebra of truncated Taylor polynomials. This design allows its
generalized dual number type to serve as a drop-in replacement for scalar types like floats, with operator overloading ensuring automatic derivative propagation. The
underlying C++ codebase `audi` can also be used directly, offering greater flexibility for instantiating the algebra over different fields.

All standard mathematical operations are supported, leveraging the nilpotency of exponentiation in truncated Taylor polynomial algebra. Within a truncated algebra $\mathcal A^n$,
any polynomial $p \in \mathcal A^n$ with vanishing constant term evaluates to zero when raised to a power greater than the truncation order ($m > n$). This property enables efficient and exact
computation of derivatives to arbitrary order while preserving the simplicity of standard numerical code.

Beyond truncated Taylor polynomials, `pyaudi` also implements Taylor models [@makino1998rigorous], which combine these polynomials with an interval bounding their truncation error,
along with miscellaneous algorithms for applications in differential intelligence, high-order automatic differentiation, verified integration, and related fields.


# Statement of need

<!-- A Statement of need section that clearly illustrates the research purpose of the software and places it in the context of related work. -->

`pyaudi` enables users to compute and manipulate order-$n$ Taylor expansions of generic computational graphs, while
providing rigorous bounds on truncation errors through associated Taylor models. These Taylor representations of program
outputs can be used for fast Monte Carlo simulations, rigorous uncertainty analysis, local inversion of output–input
relations, and high-order sensitivity studies. The package implements the high-order automatic differentiation methodology
of Berz and Makino [@berz2014introduction; @makino1998rigorous], introducing novel implementation details in polynomial
multiplication routines and Taylor model bounding.

# Existing libraries with similar capabilities

At the time of writing, two main open-source libraries perform computations similar to those in `pyaudi`. The first is the C/C++ library DACE
[@massari2018differential], which implements the full differential algebra of truncated Taylor polynomials with float coefficients. Unlike `pyaudi`,
DACE uses a memory-intensive polynomial multiplication routine for storing monomial coefficients. As shown in the separate paper_results.md file 
(in the paper directory of the paper branch of the repository), this gives DACE an advantage for single evaluations at low orders, though the benefit
decreases for batched computations and higher orders.

The second project comprises the Julia libraries TaylorSeries.jl and TaylorModels.jl [@benet2019taylormodels], which implement Taylor models to compute
rigorous bounds on Taylor series. Their approach, however, differs substantially from `pyaudi`, and preliminary results — also presented in the separate
paper_results.md file — indicate that `pyaudi` significantly outperforms them in the tested cases.

In recent years, numerous automatic differentiation toolboxes have emerged for machine learning and optimization, notably JAX [@jax2018github], TensorFlow
[@tensorflow2015-whitepaper], and PyTorch [@paszke2019pytorch] (autograd). These excel at low-order derivatives (first and second), where reverse-mode
differentiation is highly efficient. For higher-order derivatives, however, most implementations perform poorly; while experimental features such as JAX’s
jet module [@bettencourt2019taylor] show promise, they remain immature.

## Key aspects

The main features of `pyaudi` are:

- **Truncated polynomial algebra**, powered by Obake [@obake2020biscani], 
  a C++ library for symbolic manipulation of sparse multivariate polynomials, truncated power series, and Poisson series. 
  Unlike other packages, which often suffer from severe memory bottlenecks as the polynomial order or the number of variables increases, 
  `pyaudi` avoids large static memory allocations by adopting a sparse, dynamic approach. This remains memory-efficient at the cost of additional bookkeeping, 
  where sparse polynomials are the area of greatest benefit. The use of templates allows to instantiate the algebra over different fields such as floats, quadruple
  precision floats, vectorized floats, etc..

- **Vectorized coefficients**, enabling the simultaneous evaluation of identical computational graphs 
  at multiple expansion points. This feature makes it possible to compute high-order derivatives on multiple points, 
  while alleviating the overhead introduced by the sparse bookkeeping of Obake [@obake2020biscani].

- **Taylor models with Bernstein polynomial bounding**, used to enclose the range of multivariate polynomials. 
  
- **Map inversion algorithm**, implementing the method described in @berz2014introduction. 
  This feature enables the local inversion of input–output relations arising in generic computational graphs.

# Ongoing research

<!-- Mention (if applicable) a representative set of past or ongoing research projects using the software and recent scholarly publications enabled by it. -->

@acciarini2024stochastic extended the principle of numerical continuation to find solutions in non-linear
dynamical systems in the space domain to the moments of a probability density function, using `pyaudi`
to calculate the high-order derivatives in the Circular Restricted Three-Body Problem (CR3BP). Similarly, `pyaudi` was used in @acciarini2025nonlinear
for various test cases involving non-Gaussian uncertainty distributions to calculate the high-order derivatives.
@caleb2020uncertainty did work on exploring Differential Algebra (DA) for accelerating Monte Carlo simulations,
which is enabled by libraries such as `pyaudi`. For on-board applications, `pyaudi` was used in @burnett2025rapid
to implement convex guidance algorithms by describing the solutions using DA. @izzo2017differentiable
explored Cartesian Genetic Programming and made it differentiable with high-order automatic differentiation,
enabled by `pyaudi`.

# Acknowledgement of financial support

No financial support was provided for the development of this software.

# Bibliography
