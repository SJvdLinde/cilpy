# Python Computational Intelligence Library for Large-Scale Optimization Problems

This README describes the large-scale optimization additions made to `cilpy`.
The `cilpy/` folder has its own dedicated README with full library documentation, installation instructions and usage examples. Refer to that for details about `cilpy` itself. This README contains information about the Large-Scale Optimization aspect and
any other additions made to the library.

---

## Overview

The cooperative solvers added to `cilpy` split the variables into groups and give
each group its own sub-solver. A sub-solver evaluates its part of a solution by
inserting it into a context vector, which holds the best value found so far for
every variable. Any `cilpy` solver can be used as the sub-solver, so the same
approaches work with PSO, GA and DE.

Four cooperative approaches are available:

| Approach | Solver | Groups |
|---|---|---|
| CPSO | Static cooperative solver | stay the same for the whole run |
| DRG | Dynamic random grouping solver | are shuffled into new random groups during the run |
| DCPSO | Decomposition cooperative solver | start as one group and are split into smaller groups |
| MCPSO | Merging cooperative solver | start as one group per variable and are merged into bigger groups |

Each approach is built from interchangeable strategies:

| Strategy | Options | Used by |
|---|---|---|
| Grouping | `StaticEvenGrouping`, `RandomGrouping`, `RandomGroupSizeGrouping` | all four |
| Trigger | `FixedIntervalTrigger`, `EvaluationIntervalTrigger`, `StagnationTrigger`, `NeverTrigger` | DRG, DCPSO, MCPSO |
| Splitter | `EvenSplitter`, `RandomSplitter` | DCPSO |
| Merger | `EvenMerger`, `RandomMerger` | MCPSO |

---

## Repository Structure

Files marked `[new]` were added in this project, and files marked `[changed]` are
original [cilpy](https://github.com/cilpy-org/cilpy) files that were extended.
Next to each file is what I added or updated.

```
.
├── README.md
└── cilpy/
    ├── README.md                      # original cilpy README [changed]
    ├── Makefile                       # make test [new]
    ├── pyproject.toml                 # added optional "cec" extra (opfunu) [changed]
    ├── cilpy/
    │   ├── runner.py                  # added optional evaluation budget [changed]
    │   ├── problem/
    │   │   ├── unconstrained.py       # Rastrigin, Rosenbrock, Griewank, Elliptic, Schwefel 2.21,
    │   │   │                          # Schwefel 2.22, Schwefel 2.26, Weierstrass [changed]
    │   │   ├── constrained.py         # ConstrainedSphere [changed]
    │   │   ├── counting.py            # counts fitness evaluations for the budget [new]
    │   │   └── cec2010.py             # CEC 2010 large-scale functions [new]
    │   └── solver/
    │       ├── __init__.py            # refresh_evaluations and accept_best on Solver [changed]
    │       ├── pso.py                 # velocity limit and bound handling [changed]
    │       ├── ga.py                  # refresh_evaluations and accept_best [changed]
    │       ├── de.py                  # refresh_evaluations and accept_best [changed]
    │       └── decomposition/         # cooperative decomposition solvers [new]
    │           ├── base.py            # shared cooperative coordinator
    │           ├── context.py         # context vector
    │           ├── sub_problem.py     # sub-problem for one group of dimensions
    │           ├── grouping.py        # groupings, splitters and mergers
    │           ├── trigger.py         # regroup triggers
    │           ├── cpso.py            # Static cooperative solver
    │           ├── drg.py             # Dynamic random grouping solver
    │           ├── dcpso.py           # Decomposition cooperative solver
    │           └── mcpso.py           # Merging cooperative solver
    ├── examples/
    │   ├── cpso_examples.py           # every CPSO grouping [new]
    │   ├── drg_examples.py            # every DRG grouping and trigger [new]
    │   ├── dcpso_examples.py          # every DCPSO grouping, splitter and trigger [new]
    │   ├── mcpso_examples.py          # every MCPSO grouping, merger and trigger [new]
    │   ├── cooperative_exa.py         # CPSO, CGA, CDE against PSO, GA, DE [new]
    │   └── baselines_exa.py           # plain PSO, GA, DE baselines [new]
    └── test/
        └── solver/decomposition/      # unit tests for the decomposition package [new]
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

To use the CEC 2010 large-scale benchmark functions (`cilpy/problem/cec2010.py`),
install the optional `cec` extra, which adds the `opfunu` library (version 1.0.1):

```bash
pip install -e ".[cec]"
```

---

## Evaluation Budget

The original `ExperimentRunner` was only able to run experiments using
an iteration budget. I updated it to be able to run experiments using a 
fitness evaluations budget. An example of how to run it is given below:

```python
runner = ExperimentRunner(
    problems=problems_to_run,
    solver_configurations=solver_configs,
    num_runs=30,
    max_evaluations=100000,  # instead of max_iterations
)
```

If both `max_iterations` and `max_evaluations` are given, a run stops at whichever
is reached first.

---

## Examples

All examples are run from inside the `cilpy/` directory (with the virtual environment active).
Results are written to `out/`.

### One example per cooperative solver

I added example files to show how each cooperative solver can be combined with different groupings, triggers and base solvers.

| File | Solver | Problem | Shows |
|---|---|---|---|
| `cpso_examples.py` | CPSO | Rastrigin | the three groupings |
| `drg_examples.py` | DRG | Rosenbrock | the three groupings and three triggers |
| `dcpso_examples.py` | DCPSO | Rosenbrock | the three groupings, the random splitter and three triggers |
| `mcpso_examples.py` | MCPSO | Rastrigin | the three groupings, the random merger and three triggers |

```bash
python3 examples/cpso_examples.py
python3 examples/drg_examples.py
python3 examples/dcpso_examples.py
python3 examples/mcpso_examples.py
```


### Cooperative approaches vs. baselines (unconstrained)

Runs `CPSO`, `CGA`, and `CDE` (cooperative decomposition) against plain `PSO`, `GA`, and `DE` baselines on the four unconstrained benchmark functions (Sphere, Quadric, Ackley, Rastrigin).
```bash
python3 examples/cooperative_exa.py
```

---

## Running Tests

All make commands are run from inside the `cilpy/` directory (with the virtual environment active).

### Run the test suite

```bash
make test
```

Runs all tests using `pytest`.
