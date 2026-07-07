# OpenSpiel Fork

This directory contains my fork of OpenSpiel, extended with a custom implementation of the **patrol game** developed as part of my master's thesis. The game is implemented in **C++** and fully integrated with the [OpenSpiel](https://github.com/google-deepmind/open_spiel).

In addition to the files described below, several small modifications to the original OpenSpiel repository are required (e.g. CMake registration). These changes follow the standard OpenSpiel integration process described in the [Developer Guide](https://github.com/MarcinTraskowski/open_spiel/blob/master/docs/developer_guide.md).

---

## Patrol game implementation

The main implementation of the patrol game is located in:

- `open_spiel/games/patrol/`

The folder contains the complete game implementation together with unit tests and example graph definitions.

### Graph definitions

Example environments used throughout the thesis are stored in:

- `open_spiel/games/patrol/graphs/`

These JSON files define the graph topology, attack durations, target values, and coverage probabilities.

---

## Training defender strategies

Several training source files are provided for different CFR variants.

### Vanilla CFR / CFR+

- `open_spiel/examples/cfr_no_exploit.cc`
- `open_spiel/examples/cfr_with_exploit.cc`

### Monte Carlo CFR

- `open_spiel/examples/mccfr_with_exploit.cc`

These source files can be used to train defender strategies using Vanilla CFR, CFR+, or Monte Carlo CFR. The `*_with_exploit` variants additionally compute exploitability during training.

---

## Example strategy

Training results are stored in:

```text
results/full/
```

A small example strategy for the `clique_3` graph is included.

It can be generated using

```bash
~/open_spiel/build/examples/cfr_with_exploit \
  --game_name="patrol(graph_path=$HOME/open_spiel/open_spiel/games/patrol/graphs/clique_3.json)" \
  --num_iters=10000 \
  --report_every=1000 \
  --graph_name=clique_3 \
  --solver=cfr_plus
```

The exported JSON file contains both training statistics (iterations, exploitability, elapsed time) and the learned strategy.

For example,

```json
"p=0|def_hist=init": {
    "0": 0.333333,
    "1": 0.333333,
    "2": 0.333333
}
```

describes the defender's initial position distribution. Since `clique_3` is perfectly symmetric, the optimal strategy starts uniformly from each vertex.

---

## Reproducing playthroughs

Because the patrol game requires specifying the graph file, the standard OpenSpiel playthrough command must be slightly modified:

```bash
python3 open_spiel/python/examples/playthrough.py \
  --game="patrol(graph_path=$HOME/open_spiel/open_spiel/games/patrol/graphs/star_shield.json)" \
  --output_file=open_spiel/integration_tests/playthroughs/patrol.txt \
  --alsologtostdout
```

Unlike most OpenSpiel games, the patrol game requires the additional `graph_path` parameter when creating the game instance.

---

## Analysis scripts

The notebooks and scripts used to process experimental results and generate the figures included in the thesis are located in

```text
analysis/
```