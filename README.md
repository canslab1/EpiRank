# EpiRank — Epidemic Risk Analysis via Asymmetric Commuting Networks

![Python 3.10+](https://img.shields.io/badge/Python-3.10%2B-blue)
![License: MIT](https://img.shields.io/badge/License-MIT-green)
[![CANS Lab](https://img.shields.io/badge/CANS_Lab-Homepage-orange)](https://canslab1.github.io/)

A desktop application implementing the **EpiRank** algorithm for epidemic risk analysis across Taiwan's 353 townships based on asymmetric commuting networks.

## Overview

Epidemic spread is not random — it flows along the daily commuting paths of millions of people. Every morning, commuters move from home (origin) to work (destination); every evening, they return. This bidirectional human flow is the highway of disease transmission.

**EpiRank** borrows from Google's PageRank philosophy: a webpage's importance depends on "who links to it"; analogously, a township's epidemic risk depends on "who commutes here and who returns home from here." But EpiRank goes further — it simultaneously models the *forward trip* (morning: population spreads from home to work) and the *return trip* (evening: population flows back from work to home), with the `daytime` parameter controlling the balance between the two.

Using only a single commuting OD matrix, EpiRank can predict the spatial distribution of three different diseases (Influenza, Enterovirus, SARS) — demonstrating that the commuting structure itself is the fundamental driver of epidemic risk.

## Features

The GUI provides 14 interactive tabs reproducing all key figures and tables from the paper:

| Tab | Content | Paper Reference |
|-----|---------|-----------------|
| 0 | Results Table | Ranked EpiRank scores for all 353 townships |
| 1 | Network Map | Commuting network visualisation |
| 2 | Core Classification | Table 1 — head/tail break counts by method |
| 3 | Correlations | Table 2 — Pearson / Spearman / Recall / Precision |
| 4 | Commuter Flow | Figure 2 — 7 sub-plots: map, scatter, histogram |
| 5 | Frequency Distributions | Figure 3 — disease frequency + log ratio |
| 6 | Frequency Distribution | Figure 6 — EpiRank frequency by daytime |
| 7 | EpiRank vs Disease | Figure 9 — stacked percentage bars |
| 8 | Index Comparison | Figure 10 — EpiRank vs PageRank vs HITS |
| 9 | Disease Map | Figure 4 — spatial disease severity |
| 10 | EpiRank Map | Figure 7 — spatial EpiRank levels |
| 11 | EpiRank vs Disease Map | Figure 8 — overlay: prediction vs actual |
| 12 | Log | Computation log |
| 13 | Sensitivity Analysis | Figure 11 — daytime × d heatmaps |

### Output Files

The program automatically saves three output files after each computation:

| File | Description |
|------|-------------|
| `ERA_result_d_{d}_daytime_{dt}_loops_{n}.xlsx` | Results table with ranked EpiRank scores |
| `ERA_result_d_{d}_daytime_{dt}_loops_{n}.png` | Network map visualisation (450 dpi) |
| `ERA_result.net` | Pajek format network file |

Individual charts can also be exported as PNG, PDF, or SVG via the menu.

## Installation

**Requirements:** Python 3.10+

```bash
git clone https://github.com/canslab1/EpiRank.git
cd EpiRank
pip install -r requirements.txt
```

### Dependencies

| Package | Purpose |
|---------|---------|
| PySide6 | Qt-based GUI |
| NumPy / SciPy | Numerical computing |
| NetworkX | Network analysis |
| Matplotlib | Visualization |
| openpyxl | Excel output |

## Usage

```bash
python EpiRank_GUI.py
```

The program loads the five bundled data files automatically from the same directory.

### Parameters

| Parameter | Range | Default | Description |
|-----------|-------|---------|-------------|
| Damping factor (d) | 0.0–1.0 | 0.95 | Higher → network structure dominates; lower → result approaches uniform distribution |
| Daytime weight | 0.0–1.0 | 0.5 | 0.0 = backward only (evening return); 0.5 = bidirectional; 1.0 = forward only (morning commute) |
| Max iterations | 1–5000 | 1000 | Convergence limit (typically converges in 50–200 iterations) |

### Data Files

#### `bs.xlsx` — Township Metadata

Sheet: `town_data` · 409 sub-township rows aggregated into 353 unique townships.

| Column | Field | Description |
|--------|-------|-------------|
| A | `db_ID` | Unique township ID (0–352) |
| B | `county` | County name (縣市) |
| C | `town` | Township name (鄉鎮市區) |
| D | `area` | Area label |
| E–F | `pos.x`, `pos.y` | Map coordinates (TWD97) |
| G–H | `pos2.x`, `pos2.y` | Adjusted map coordinates |
| I | `population` | Total population |
| J | `sub_percentage` | Sub-township ratio (for aggregation) |
| K | `sub_area km2` | Sub-township area (km²) |
| L | `area_km2` | Township area (km²) |
| M | `pop_den` | Population density (people/km²) |
| N | `pop_den (normal)` | Normalised population density (0–1) |
| O | `age 0-14` | Age group 0–14 (%) |
| P | `age 15-64` | Age group 15–64 (%) |
| Q | `age 65+` | Age group 65+ (%) |

#### `cn.xlsx` — Commuting Network

Sheet: `353C` · 353×353 origin–destination matrix (from the 2000 Taiwan population census).

| Row | Content |
|-----|---------|
| 1 | Sequence number (0–352) for each township |
| 2 | Post code (郵遞區號) |
| 3 | `db_ID` matching `bs.xlsx` |
| 4–… | Header rows; data starts at row 6 |

Cell (i, j) = number of commuters living in township i who work in township j.

Key properties:
- **Directed**: commuting A→B does not imply equal flow B→A
- **Weighted**: edge weight = commuter count
- **Self-loops**: OD[i][i] = local commuters living and working in the same township
- **Asymmetric**: a bedroom suburb may send 50,000 commuters to the city but receive only 2,000

#### `Flu.xlsx` — Influenza Cases

Sheet: `2009` · 353 rows. Source: Taiwan CDC (疾管署) surveillance data.

| Column | Field | Description |
|--------|-------|-------------|
| A | `county` | County name |
| B | `town` | Township name |
| C | `SUM` | Total reported influenza cases (2009) |

#### `ev.xlsx` — Enterovirus Cases

Sheet: `2000_2008` · 353 rows.

| Column | Field | Description |
|--------|-------|-------------|
| A | `county` | County name |
| B | `town` | Township name |
| C | `AVERAGE` | Average yearly enterovirus cases (2000–2008) |

#### `SARS.xlsx` — SARS Cases

Sheet: `2003` · 353 rows. Used for Greater Taipei (大台北都會區, 48 townships) correlation analysis.

| Column | Field | Description |
|--------|-------|-------------|
| A | `county` | County name |
| B | `town` | Township name |
| C | `SUM` | Total reported SARS cases (2003) |

## Algorithm

### Three-Stage Pipeline

**Stage 1 — Network Construction**
Build a 353-node directed graph from the census commuting OD (origin–destination) matrix. The network is directed, weighted, and asymmetric — commuting from A→B does not imply equal flow B→A. Self-loops represent local commuters (~84% of all commuters).

**Stage 2 — Matrix Normalisation**
Column-normalise the raw OD matrix into two stochastic matrices, each capturing a different direction of disease transmission:

- **W** = col-normalise(OD) → backward (evening) direction: models risk flowing from workplaces back to residences
- **Wᵀ** = col-normalise(ODᵀ) → forward (morning) direction: models risk flowing from residences to workplaces

**Stage 3 — Iterative Convergence**
Starting from a uniform distribution, repeatedly apply the EpiRank formula until the risk vector stabilises:

```
ER(t+1) = (1 − d) · (1/N) + d · [daytime · Wᵀ · ER(t) + (1 − daytime) · W · ER(t)]
```

| Term | Interpretation |
|------|----------------|
| `(1 − d) · (1/N)` | **Teleportation**: with probability (1−d), a pathogen arrives from an external source (e.g. international travel) regardless of the commuting network. Prevents isolated areas from having zero risk. |
| `d · daytime · Wᵀ · ER` | **Forward (morning) contribution**: commuters arrive at workplaces carrying risk from their home townships. High-risk townships that send many workers raise the risk of the destination (pull effect). |
| `d · (1−daytime) · W · ER` | **Backward (evening) contribution**: commuters return to residences carrying risk from their workplaces. High-risk workplaces push disease back to the bedroom suburbs (push effect). |

### Convergence Guarantee

Convergence is guaranteed by the Perron–Frobenius theorem. The iteration matrix M = (1−d)·E + d·P is a strictly positive column-stochastic matrix (since (1−d)/N > 0 fills all zero entries), which is irreducible and aperiodic. Therefore M has a unique dominant eigenvalue λ₁ = 1 and all other |λᵢ| < 1, ensuring power iteration converges to the unique stationary distribution from any initial vector. The convergence rate is geometric: ‖ER(t) − ER*‖ ≤ dᵗ · ‖ER(0) − ER*‖. Typically converges within 50–200 iterations for d = 0.95.

### Classification: Head/Tail Breaks

After computing continuous EpiRank scores, the program classifies townships into four discrete risk levels using the **head/tail breaks** method (Jiang, 2013) — specifically designed for heavy-tailed distributions where most values are low and a few are extremely high:

```
Round 1: all 353 townships
 ├─ tail (≤ mean₁): ~239 townships → NC  (non-core)
 └─ head (> mean₁): ~114 townships
     Round 2:
     ├─ tail (≤ mean₂): ~67 townships → C-III
     └─ head (> mean₂): ~47 townships
         Round 3:
         ├─ tail (≤ mean₃): ~31 townships → C-II
         └─ head (> mean₃): ~16 townships → C-I  (highest risk)
```

### Comparison with Other Indices

The program also computes **PageRank** and **HITS** (Hub/Authority) for comparison against EpiRank, evaluating them using Pearson/Spearman correlation, recall, and precision against actual disease data.

## Project Structure

```
EpiRank/
├── EpiRank_GUI.py     # Main application (GUI + algorithm)
├── requirements.txt   # Python dependencies
├── bs.xlsx            # Township metadata (353 townships)
├── cn.xlsx            # Commuting OD matrix (353×353)
├── Flu.xlsx           # Influenza case data (2009)
├── ev.xlsx            # Enterovirus case data (2000–2008)
├── SARS.xlsx          # SARS case data (2003)
└── LICENSE            # MIT License
```

## Authors

- **Chung-Yuan Huang** (黃崇源) — Department of Computer Science and Information Engineering, Chang Gung University, Taiwan (gscott@mail.cgu.edu.tw)

## Citation

If you use this software in your research, please cite:

> Huang, C.-Y., Chin, W. C. B., Wen, T.-H., Fu, Y.-H., & Tsai, Y.-S. (2019). EpiRank: Modeling Bidirectional Disease Spread in Asymmetric Commuting Networks. *Scientific Reports*, 9, 5415. https://doi.org/10.1038/s41598-019-41719-8

See `CITATION.cff` for machine-readable citation metadata.

## References

1. Huang, C.-Y., Chin, W. C. B., Wen, T.-H., Fu, Y.-H., & Tsai, Y.-S. (2019). EpiRank: Modeling Bidirectional Disease Spread in Asymmetric Commuting Networks. *Scientific Reports*, 9, 5415. https://doi.org/10.1038/s41598-019-41719-8

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.
