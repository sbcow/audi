# Contributing to audi

First of all, thank you for considering a contribution to `audi`.

`audi` is an open-source, header-only C++ library, also exposed to Python through `pyaudi`, implementing the algebra of truncated Taylor polynomials and related algorithms for automatic differentiation, Taylor Models, and Differential Intelligence. Because the project serves both research and practical numerical applications, contributions should aim for correctness, clarity, and minimal disruption to existing users.

## What can be contributed

Contributions are welcome in areas such as:
- Bug fixes
- New tests
- Documentation improvements
- Examples and tutorials
- Performance improvements
- Build and packaging improvements
- Python binding (`pyaudi`) updates

If your change is large, affects the public API, or changes core numerical behavior, please open an issue first to discuss the idea before investing substantial effort.

## General principles

Please try to follow these principles when contributing:
- Keep pull requests focused and limited in scope.
- Avoid unrelated refactoring in the same PR.
- Preserve backward compatibility unless the change is explicitly discussed as breaking.
- Follow the existing coding style, naming, and file organization.
- Prefer simple, maintainable solutions over clever but hard-to-review ones.
- Do not add new dependencies unless there is a strong reason and the change has been discussed first.

Early draft pull requests are welcome. If you are unsure about the direction of an implementation, opening a WIP PR early is often the best approach.

## Reporting bugs

If you find a bug, please open an issue and include:
- A short description of the problem
- Expected behavior
- Actual behavior
- A minimal reproducible example
- Platform details, when relevant:
  - operating system
  - compiler and version
  - Python version, if `pyaudi` is involved
  - build/install method (`pip`, `conda`, source build, etc.)

If the issue concerns numerical behavior, please include the smallest example that reproduces the discrepancy.

## Suggesting features

Feature requests are welcome, especially when they are motivated by a concrete use case.

Please include:
- The problem you are trying to solve
- Why the current functionality is insufficient
- A proposed interface or behavior, if you have one
- Whether the change affects C++, Python, or both

For major features, it is strongly recommended to discuss the design in an issue before opening a full implementation PR.

## Development setup

### C++ library

`audi` is a header-only library, but local builds are still useful for tests and validation.

A typical setup is:
```bash
git clone https://github.com/darioizzo/audi.git
cd audi
mkdir build
cd build
cmake ..
```

When configuring CMake:
- enable the C++ library build
- disable the Python bindings if you are working only on the C++ side
- enable tests when validating code changes

Please refer to the project documentation for the current dependency and build details.

### Python bindings

If your change affects `pyaudi`, please verify the corresponding Python behavior locally as well. A contribution touching the bindings should not be considered complete unless both the binding layer and the exposed behavior are validated.

## Tests

Every bug fix should include a test whenever practical.

Every new feature should include:
- tests for the expected behavior
- edge cases when relevant
- updates to existing tests if the intended behavior changes

In particular, please add tests for changes affecting:
- algebraic correctness
- derivative computation
- Taylor model behavior
- sparse/dense handling
- multi-point or vectorized behavior
- Python bindings

If your contribution changes performance-critical code, benchmarks or timing evidence are appreciated when performance is part of the claim.

## Documentation

Please update the documentation for any user-visible change.

Examples of documentation-relevant changes include:
- new public APIs
- modified behavior
- installation or build changes
- new examples
- changed Python exposure
- important caveats or limitations

If possible, keep examples small and executable. Documentation should help both C++ users and `pyaudi` users understand the intended usage.

## Pull requests

When submitting a pull request, please include:
- A concise title
- A short explanation of what the PR changes
- Why the change is needed
- Any related issue number
- Notes on testing performed
- Notes on performance impact, if applicable

Good pull requests are usually:
- small enough to review comfortably
- justified by a clear use case
- accompanied by tests
- accompanied by documentation updates if needed

Please avoid mixing formatting-only edits with behavioral changes unless there is a clear reason.

## Numerical and scientific changes

For changes to core algorithms or numerical behavior, please be especially careful.

In the PR description, explain:
- the mathematical or computational rationale
- whether the change affects correctness, stability, or performance
- whether the behavior changes for existing users
- any assumptions, limitations, or trade-offs

For scientifically meaningful modifications, a short explanation is far more helpful than code alone.

## Style expectations

There is no need for rigid bureaucracy, but contributions should respect the existing style of the codebase.

As a rule of thumb:
- write code that is easy to read and review
- keep interfaces coherent with the current library
- prefer explicitness in critical numerical code
- add comments only where they help explain non-obvious decisions

## Licensing

By contributing to this repository, you agree that your contribution may be distributed under the same license as the project.

## Questions

If you are unsure whether a contribution is appropriate, opening an issue or an early draft PR is perfectly fine.

