## Comparison with DACE

The main difference between the DACE library, with respect to automated differentiation capabilities, and `pyaudi` is to be found in their 
polynomial multiplication algorithm. We thus focus on that for a preliminary comparison. Our benchmarks run on an AMD EPYC 7702 64-Core Processor with 512GB of RAM
and show the relative computational time of the exact same quantities. We compare the polynomial multiplication algorithm in `pyaudi` with the one implemented in DACE, on a single polynomial multiplication. 

We thus intoduce two polynomials of the form:

$$
\begin{array}{l}
\begin{aligned}
     p_1 &= (1 + x_1 + x_2 + ... + x_n)^{m} \\
     p_2 &= (1 - x_1 - x_2 - ... - x_n)^{m} \\
\end{aligned}
\end{array}
$$
where $x_i, i=1..n$ etc. are the variables and $m$ the order. The polynomials are then multiplied and the result of $p_1 p_2$ is timed. For this simple and basic operation, the speed up of pyaudi w.r.t. DACE is reported in the table below (2.0 would mean pyaudi is twice as fast as DACE). 

| nvars↓ Order→     | 6     | 7     | 8      | 9     | 10    | 11    | 12    | 13    | 14    | 15   |
| ----------------- | ----- | ----- | ------ | ----- | ----- | ----- | ----- | ----- | ----- | ---- |
| 6                 | 0.162 | 0.269 | 0.0669 | 0.181 | 0.157 | 0.359 | 0.593 | 0.63  | 1.11  | 1.16 |
| 8                 | 0.0538| 0.237 | 0.147  | 0.438 | 0.721 | 0.992 | 2.25  | 3.58  | 6.49  | 9.26 |
| 10                | 0.168 | 0.304 | 0.43   | 0.641 | 1.32  | 2.9   | 7.04  | 11    | 18.5  | 27.8 |

It can be seen that pyaudi is faster from $n + m >\approx 19$ where memory management becomes an issue. At lower orders and number of variables
DACE is significantly faster as it is able to exploit an easier memory structure and has no overhead.

### Vectorized coefficients

In order to mitigate the bookkeeping overheads of `pyaudi`, vectorized coefficients have been implemented as to allow
to perform the same computations over batches, also having in mind potential machine learning applications.

To showcase the resulting performances of such a vectorization, we perform the following computation batching the
value $c_v$ of the constant coefficient.

$$
\begin{array}{l}
    p_3 = \left(\frac{c_v + x_1 + x_2 + ... + x_n}{c_v - x_1 - x_2 - ... - x_n}\right)^{10} \\
\end{array}
$$

It is worth noting here that this operation is not representative of actual applications where one is mostly interested in
computing higher order derivatives of computer programs. Rather it is selected to isolate the feature we are proposing to benchmark which is
batching coefficients. In case of DACE we perform the same computation over the entire batch in a loop.

The results are displayed in the three following tables with differing number of variables.

#### 6 variables

| points↓ Order →      | 1     | 2     | 3     | 4     | 5     | 6     | 7     | 8     | 9     |
| -------------------- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| 16                   | 0.221 | 0.18  | 0.314 | 0.076 | 0.0731| 0.142 | 0.139 | 0.185 | 0.337 |
| 64                   | 0.967 | 0.717 | 0.162 | 0.183 | 0.142 | 0.325 | 0.413 | 0.614 | 0.985 |
| 256                  | 1.59  | 0.212 | 0.355 | 0.273 | 0.312 | 0.716 | 1.14  | 1.68  | 2.32  |
| 1024                 | 0.341 | 0.444 | 0.373 | 0.336 | 0.478 | 1.35  | 1.56  | 2.65  | 3.77  |
| 4096                 | 0.752 | 0.533 | 0.537 | 0.486 | 0.679 | 1.63  | 2.29  | 2.82  | 4.02  |

#### 8 variables

| points↓ Order →      | 1     | 2     | 3     | 4     | 5      | 6     | 7     | 8     | 9     |
| -------------------- | ----- | ----- | ----- | ----- | ------ | ----- | ----- | ----- | ----- |
| 16                   | 0.433 | 0.231 | 0.0446| 0.0746| 0.0774 | 0.153 | 0.222 | 0.367 | 0.472 |
| 64                   | 1.18  | 1     | 0.168 | 0.174 | 0.195  | 0.464 | 0.764 | 0.978 | 1.38  |
| 256                  | 3.93  | 0.405 | 0.261 | 0.345 | 0.575  | 1.28  | 1.57  | 2.08  | 2.95  |
| 1024                 | 1.23  | 0.834 | 0.468 | 0.836 | 1.04   | 2.05  | 2.45  | 3.66  | 4.3   |
| 4096                 | 2.14  | 0.878 | 1.03  | 1.43  | 1.28   | 2.24  | 2.95  | 3.87  | 5.11  |

#### 10 variables

| points↓ Order →      | 1     | 2     | 3      | 4      | 5      | 6     | 7     | 8     | 9     |
| -------------------- | ----- | ----- | ------ | ------ | ------ | ----- | ----- | ----- | ----- |
| 16                   | 0.376 | 0.228 | 0.0348 | 0.0705 | 0.0806 | 0.177 | 0.246 | 0.376 | 0.586 |
| 64                   | 1.05  | 0.167 | 0.185  | 0.189  | 0.289  | 0.517 | 0.832 | 1.27  | 1.97  |
| 256                  | 3.37  | 0.489 | 0.337  | 0.435  | 0.796  | 1.4   | 2.21  | 3.34  | 4.87  |
| 1024                 | 1.07  | 0.816 | 0.624  | 1.03   | 1.35   | 2.82  | 4.54  | 6.7   | 10    |
| 4096                 | 2.36  | 0.983 | 1.2    | 1.35   | 2.04   | 3.94  | 6.36  | 9.72  | 15.2  |

It can be seen that, from batches of size 64 onwards, pyaudi becomes competitive with respect to DACE and significanlty faster at higher orders. 
Clearly these results are only indicative of the specific computation selected and different sparsities and computations will result in rather different 
speedups. The tables above are nonetheless useful to establish a trend which remains true in general: applications where very high derivation orders
or multiple expansion points need to be computed, will benefit from `pyaudi` algorithmic implementations.

## Comparison with TaylorModels.jl

We here test the performance of the implementation of Taylor models in `pyaudi` against the Julia package
TaylorModels.jl. To perform the comparison we use three functions $f,g,h$: one univariate, one bivariate and one trivariate defined
below. We then construct Taylor models of all the variables separately and time the evaluation of
the corresponding Taylor model. The comparison is made on a single CPU machine.

$$
\begin{array}{l}
\begin{aligned}
f(x) = & x(x-1.1)(x+2)(x+2.2)(x+2.5)(x+3)\sin(1.7x+0.5) \\[2 ex]
g(x, y) = & \sin(1.7x+0.5)(y+2)\sin(1.5y) \\[2 ex]
h(x, y, z) = {} &
\frac{4 \tan(3y)}{3x + x \sqrt{\tfrac{6x}{-7(x-8)}}}
- 120 - 2x - 7z(1+2y) \\
& - \sinh\!\left(0.5 + \frac{6y}{8y+7}\right)
+ \frac{(3y+13)^2}{3z}
- 20z(2z-5) \\
& + \frac{5x \tanh(0.9z)}{\sqrt{5y}}
- 20y \sin(3z)
\end{aligned} 
\end{array}
$$

| Dimension             | Package                 | Remainder Bound (Order 1) | Remainder Bound (Order 15) | Speed Comparison                                                                                  |
| --------------------- | ----------------------- | ------------------------- | -------------------------- | ------------------------------------------------------------------------------------------------- |
| f(x)                  | TaylorModels.jl         | 1e+2                      | 1e-5                       | ~1–1.5× faster than pyaudi                                                                        |
|                       | pyaudi                  | 1e+2                      | 1e-5                       | ~1–1.5× slower than TaylorModels.jl                                                               |
|                       |                         |                           |                            |                                                                                                   |
| g(x, y)               | TaylorModels.jl         | 1e+1                      | 1e-6                       | Slower: pyaudi is 5× faster (order 3), 15× faster (order 15), 7800× faster (order 1, edge case)   |
|                       | pyaudi                  | 1e+1                      | 1e-6                       | Faster (see above)                                                                                |
|                       |                         |                           |                            |                                                                                                   |
| h(x, y, z)            | TaylorModels.jl         | 1e+0                      | 1e-11                      | Slower: pyaudi is 8× faster (order 3), 155× faster (order 15), 13000× faster (order 1, edge case) |
|                       | pyaudi                  | 1e-1                      | 1e-17                      | Faster (see above)                                                                                |

In the table above, a clear trend can be seen both in terms of speed and accuracy. For univariate
Taylor models, TaylorModels.jl and `pyaudi` have similar performances. At two dimensions, while the
remainder bounds are comparable in size, `pyaudi` is significantly faster, with the speedup
increasing with the order of the polynomial. At three dimensions, `pyaudi` produces significantly
tighter bounds and is again significantly faster, with the speedup increasing with the order of
the polynomial.

