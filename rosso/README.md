# RoSSO Evaluation

This directory contains the notebooks and scripts used to evaluate strategies produced by the [RoSSO](https://github.com/conhugh/RoSSO) framework.

Despite multiple attempts, we were not able to fully reproduce the results reported in the original paper. The best-performing strategy was obtained using the configuration provided in `sf_5_init.json`.

## Required Fixes

To train the `weighted_Stackelberg_co_opt_pi` strategy using the main branch of the original RoSSO repository (as of **2026-07-07**), two small fixes are required.

### 1. Gradient Sign (`strat_opt.py`)

In the `run_optimizer` function, replace

```python
updates, opt_state = optimizer.update(grad, opt_state)
```

with

```python
updates, opt_state = optimizer.update(-grad, opt_state)
```

The objective used by `weighted_Stackelberg_co_opt_pi` is a maximization problem, while the optimizer performs gradient descent. Negating the gradient converts it into gradient ascent.

After training, save the final transition matrix, as it is required by the evaluation scripts in this directory.

### 2. Matrix Parameterization (`patrol_problem.py`)

In the `apply_parametrization` function, replace

```python
P = strat_comp.comp_P_param(
    Q[0],
    self.problem_params["adjacency_matrix"],
    self.opt_params["use_abs_param"],
)
```

with

```python
P = strat_comp.comp_P_param(
    Q,
    self.problem_params["adjacency_matrix"],
    self.opt_params["use_abs_param"],
)
```

Using `Q[0]` results in an incorrect matrix shape. NumPy broadcasting then causes every row of the optimized transition matrix to become identical, producing invalid strategies.