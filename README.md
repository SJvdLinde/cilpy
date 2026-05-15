# Python Computational Intelligence Library for Large-Scale Optimization Problems

This is the overarching repository README. The `cilpy/` subdirectory contains its own dedicated README with full library documentation, installation instructions, and usage examples -- refer to that for details about `cilpy` itself. This README contains information about the Large-Scale Optimization aspect and
any other additions made to the library.

---

## Repository Structure

```
.
├── cilpy/      # The cilpy library
└── README.md   # This file
```

---

## Setup

After cloning, set up the virtual environment from inside the `cilpy/` directory:

```bash
cd cilpy
python3 -m venv .venv
source .venv/bin/activate
pip install -e .
```

This installs `cilpy` and all its dependencies (`matplotlib`, `numpy`, `pandas`, `pytest`).

---

## Examples

All examples are run from inside the `cilpy/` directory (with the virtual environment active).

### Cooperative approaches vs. baselines (unconstrained)

Runs `CPSO`, `CGA`, and `CDE` (cooperative decomposition) against plain `PSO`, `GA`, and `DE` baselines on the four unconstrained benchmark functions (Sphere, Quadric, Ackley, Rastrigin).
```bash
python3 examples/cooperative_exa.py
```

### Coevolutionary Lagrangian solver vs. baselines (constrained)

Runs `CCLS+CPSO`, `CCLS+CGA`, and `CCLS+CDE` against their `CCLS+PSO`, `CCLS+GA`, and `CCLS+DE` baselines on the constrained benchmark problems (ConstrainedSphere and G01).

```bash
python3 examples/ccls_exa.py
```

---

## Running Tests

All make commands are run from inside the `cilpy/` directory (with the virtual environment active).

### Run the test suite

```bash
make test
```

Runs all tests using `pytest`.
