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

Every folder and file in the repository is listed below. Files marked `[new]` were created in
this project and files marked `[changed]` are original
[cilpy] files that were extended. Files marked
`[Andrew]` come from Andrew's version of `cilpy`. Andrew is another honours student that is working on `cilpy` and we shared some files so that we could work off of the same version of the repo.
Unmarked files are original `cilpy` files that were not changed.

```
.
├── .gitignore                             # [new] Created git ignore file.
├── README.md                              # [new] The outer README describing the large-scale additions of this project.
└── cilpy/
    ├── .github/
    │   └── workflows/
    │       └── docs.yml
    ├── .gitignore                         # [changed] Ignored the virtual environment (.venv/).
    ├── LICENSE
    ├── Makefile                           # [new] Added a make test command that runs the test suite.
    ├── README.md                          # [changed] Added large-scale optimization aspect.
    ├── mkdocs.yml
    ├── pyproject.toml                     # [changed] Added opfunu as an optional "cec" dependency.
    ├── cilpy/
    │   ├── runner.py                      # [changed] Added an optional fitness evaluation budget (max_evaluations).
    │   ├── compare/
    │   │   ├── __init__.py
    │   │   └── metrics.py                 # [Andrew]
    │   ├── problem/
    │   │   ├── __init__.py
    │   │   ├── cec2010.py                 # [new] Added a wrapper for the CEC 2010 large-scale benchmark functions.
    │   │   ├── cmpb.py
    │   │   ├── constrained.py             # [changed] Added the ConstrainedSphere problem.
    │   │   ├── counting.py                # [new] Added a problem wrapper that counts fitness evaluations.
    │   │   ├── dynamic_multi_objective.py # [Andrew]
    │   │   ├── mpb.py
    │   │   ├── multi_objective.py         # [Andrew] Changed by Andrew.
    │   │   └── unconstrained.py           # [changed] Added the Rastrigin, Rosenbrock, Griewank, Elliptic, Schwefel 2.21,
    │   │                                  #           Schwefel 2.22, Schwefel 2.26 and Weierstrass functions.
    │   └── solver/
    │       ├── __init__.py                # [changed] Added refresh_evaluations and accept_best to the Solver interface.
    │       ├── ccls.py                    # [Andrew] Changed by Andrew.
    │       ├── de.py                      # [changed] Added refresh_evaluations and accept_best to DE.
    │       ├── ga.py                      # [changed] Added refresh_evaluations and accept_best to GA.
    │       ├── mgpso.py                   # [Andrew]
    │       ├── pso.py                     # [changed] Added refresh_evaluations, accept_best an optional velocity
    │       │                              #           limit and bound handling to PSO.
    │       ├── chm/
    │       │   ├── __init__.py
    │       │   └── alpha_constraint.py
    │       └── decomposition/             # [new] Created the cooperative decomposition package.
    │           ├── __init__.py            # [new] Exported all public decomposition classes.
    │           ├── base.py                # [new] Created the shared cooperative coordinator that all four solvers build on.
    │           ├── context.py             # [new] Created the context vector that holds the best value for every variable.
    │           ├── sub_problem.py         # [new] Created the sub-problem that evaluates one group through the context vector.
    │           ├── grouping.py            # [new] Created the groupings, group splitters and group mergers.
    │           ├── trigger.py             # [new] Created the triggers that decide when the groups change.
    │           ├── cpso.py                # [new] Created the static cooperative solver.
    │           ├── drg.py                 # [new] Created the dynamic random grouping solver.
    │           ├── dcpso.py               # [new] Created the decomposition cooperative solver.
    │           └── mcpso.py               # [new] Created the merging cooperative solver.
    ├── docs/
    │   ├── favicon.svg
    │   ├── index.md
    │   ├── logo_cilpy_dark.svg
    │   ├── logo_cilpy_light.svg
    │   ├── quickstart.md
    │   ├── api/
    │   │   ├── compare.md
    │   │   ├── constraint_handler.md
    │   │   ├── index.md
    │   │   ├── problem.md
    │   │   ├── runner.md
    │   │   └── solver.md
    │   ├── dev/
    │   │   ├── index.md
    │   │   ├── testing.md
    │   │   └── todo.md
    │   └── lib/
    │       ├── compare.md
    │       ├── index.md
    │       ├── problem.md
    │       └── solver.md
    ├── examples/
    │   ├── baseline_comparison_exa.py     # [new] Added an example that runs CCLS with PSO, DE and GA as objective solvers on G01.
    │   ├── baselines_exa.py               # [new] Added an example that runs plain PSO, GA and DE as baselines.
    │   ├── ccls_cpso_exa.py               # [new] Added an example that runs CCLS with cooperative solvers on ConstrainedSphere.
    │   ├── ccls_exa.py                    # [new] Added an example that runs CCLS with cooperative solvers on constrained problems.
    │   ├── cooperative_exa.py             # [new] Added an example comparing CPSO, CGA and CDE with plain PSO, GA and DE.
    │   ├── cpso_exa.py                    # [new] Added an example comparing CPSO with plain PSO.
    │   ├── cpso_examples.py               # [new] Added an example that shows every grouping CPSO can use.
    │   ├── dcpso_exa.py                   # [new] Added an example comparing DCPSO with CPSO and plain PSO.
    │   ├── dcpso_examples.py              # [new] Added an example that shows every grouping, splitter and trigger DCPSO can use.
    │   ├── de_exa.py
    │   ├── drg_examples.py                # [new] Added an example that shows every grouping and trigger DRG can use.
    │   ├── drg_vs_cpso_exa.py             # [new] Added an example comparing DRG with CPSO for PSO, GA and DE.
    │   ├── ga_exa.py
    │   ├── ga_hm_cc_exa.py
    │   ├── ga_ri_cc_exa.py
    │   ├── mcpso_exa.py                   # [new] Added an example comparing MCPSO with CPSO and plain PSO.
    │   ├── mcpso_examples.py              # [new] Added an example that shows every grouping, merger and trigger MCPSO can use.
    │   ├── pso_constrained_alpha_exa.py   # [changed] Fixed the example so that all of its versions run.
    │   ├── pso_exa.py
    │   ├── pso_multi_objective_exa.py
    │   └── pso_qi_exa.py
    └── test/
        ├── README.md
        ├── problem/
        │   ├── test_cmpb.py
        │   ├── test_constrained.py
        │   ├── test_mpb.py
        │   └── test_unconstrained.py      # [changed] Added unit tests for Rastrigin and Rosenbrock.
        └── solver/
            ├── test_de.py
            ├── test_ga.py
            ├── test_pso.py
            ├── chm/
            │   ├── test_alpha_constraint.py
            │   └── test_default.py
            └── decomposition/             # [new] Created unit tests for the decomposition package.
                ├── test_context.py        # [new] Added unit tests for the context vector.
                ├── test_cpso.py           # [new] Added unit tests for the static cooperative solver.
                ├── test_grouping.py       # [new] Added unit tests for the groupings.
                └── test_sub_problem.py    # [new] Added unit tests for the sub-problem.
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
