# March Machine Learning Mania 2026

------



## Table of Contents

-   [Intro](#intro)
    -   [Disclaimer](#disclaimer)
    -   [About](#about)

-   [Methodology explanation](#methodology-explanation)
-   [Quickstart](#quickstart)
    -   [Environment Setup](#environment-setup)
    -   [Usage](#usage)
    -   [Troubleshooting](#troubleshooting)

# Intro

## Disclaimer

Competition rules do not permit sharing competition data outside of Kaggle. Thus, if you wish to run the notebook yourself, a Kaggle account is required for the purposes of [downloading the data](https://www.kaggle.com/competitions/march-machine-learning-mania-2026/data) (navigate to the bottom of the page).

## About

This repository is a showcase of my submissions for the [Machine Learning Mania 2026 Kaggle competition](https://www.kaggle.com/competitions/march-machine-learning-mania-2026) I participated in as part of my student research group.

Showcased here are two notebooks, which can be viewed directly from within this repository - their contents have already been executed on Kaggle's GPUs.

-   **baseline.ipynb** - The raw competition notebook sourced from Kaggle. MLP trained on regular season games and tournaments (up to 2020) on team seeds and boxcol averages/season extracted from the regular season data. Early stopping during validation on tournaments 2021-2024. **This is the notebook our team ended up submitting.**
-   **improved.ipynb** - The baseline notebook refactored and made more accessible. Improved upon by introducing rolling averages to eliminate indirect target leakage and adding elo calculations. **This is the *proper* notebook intended for viewing.**

Alternatively, you can run the notebooks yourself locally. See [Quickstart](#Quickstart) below.

# Methodology explanation

The so-called meta for this kind of tabular data competition are tree-based models, but I wanted to see how a neural net would stack against them. Thus, requiring a lot of data, I decided to train on both the regular season and tournaments (A popular approach is training exclusively on tournament data enriched with features derived from the regular season). 

This introduced target leakage in the form of training on a game whitch has been taken into account indirectly through season averages. The improved notebook "fixes" this with rolling stats, but suffers much data loss because of that. Moreover, the leaky model was able to train on data, which simulated tournament conditions (so, conditions present in the leaderboard-relevant data) through stats based on whole seasons. 

A technique which helped our team with model selection (for submission and potential ensemble) was leaving a holdout season (2025) which would not be touched during the iterative process. Instead, it would be tested on while generating submission, after the model had already been developed (after validation and before being re-added to the training data for the final model).

Unfortunately, due to time constraints, I was not able to explore further ideas such as using proprietary models for feature engineering, extracting domain-specific features, and experimenting with the model architecture.

# Quickstart

## Environment setup

### Prerequisites - `git` and `uv`

```powershell
#terminal
#example check
> git --version
git version 2.51.0.windows.2
> uv --version
uv 0.11.2 (02036a8ba 2026-03-26 x86_64-pc-windows-msvc)
```

[Installing `git`](https://git-scm.com/install/)

[Installing `uv`](https://docs.astral.sh/uv/getting-started/installation/)

*Note: This project requires Python 3.11. `uv` will automatically fetch the required Python version if it is not present on the system.*

### Clone the Repository

```bash
git clone https://github.com/qIdol/March-Machine-Learning-Mania-2026.git
cd March-Machine-Learning-Mania-2026
```

### Sync Hardware Environment

Use `uv sync` with the `--extra` flag corresponding to the target hardware to create the virtual environment (`.venv`) and install locked dependencies.

**Warning** Executing `uv sync` without an extra flag installs base dependencies only and will exclude PyTorch.

Run **one** of the following commands:

-   **NVIDIA GPUs (CUDA 12.4):**

    ```bash
    uv sync --extra cu124
    ```

-   **Intel GPUs (XPU):**

    ```bash
    uv sync --extra xpu
    ```

-   **CPU:**

    ```bash
    uv sync --extra cpu
    ```

### Data

Make sure to unzip the [previously downloaded data](#Disclaimer) into the same directory as the notebook

## Usage

### VS Code IDE

**Note**  Make sure to have the `jupyter` and `python` extensions installed and enabled. 

1.  Open the `.ipynb` file in VS Code.
2.  Click **Select Kernel** (top right).
3.  Select **Python Environments**.
4.  Choose the environment located in the project's `.venv` directory.

### Browser-Based Jupyter

Launch the server:

```bash
uv run jupyter notebook
```

**Note** Execution Context - The `uv run` command isolates execution. Prefixing commands with `uv run` (e.g., `uv run jupyter notebook` or `uv run python script.py`) temporarily injects the necessary environment variables (`VIRTUAL_ENV`, `PATH`) to execute the command strictly within the `.venv`. Manual activation (e.g., `source .venv/bin/activate`) is not required.

## Troubleshooting

### ModuleNotFoundError for `torch`

-   **Cause:** `uv sync` was executed without a hardware extra.
-   **Resolution:** Execute `uv sync --extra <hardware_flag>` (e.g., `cpu`, `cu124`) to install PyTorch.

### Local kernel missing in VS Code

-   **Resolution:** Click the current kernel -> **Select Another Kernel...** -> **Python Environments**. If the environment is absent, select the folder icon to manually browse and define the Python executable at `\.venv\Scripts\python.exe` (Windows) or `/.venv/bin/python` (Mac/Linux).

### `.venv` kernel missing in Browser Jupyter

-   **Note:** Jupyter defaults to the `.venv` kernel upon launch via `uv run`, but labels it **"Python 3 (ipykernel)"**.

-   **Verification:** Execute the following in a notebook cell to verify the environment path:

    ```python
    import sys
    print (sys.executable)
    ```

------

