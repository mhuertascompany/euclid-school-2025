# EUCLID School 2025 — Notebooks & Environment

This repo contains the notebooks and data used in the EUCLID School 2025 lectures and labs.


# 🔧 Setup: Euclid School 2025 (if you prefer to run on colab, skip)

These notebooks are tested with **conda** (Miniforge / Mambaforge recommended).
You have two ways to install:

* **Recommended**: use the exact, reproducible environment from `environment-full.yml`.
* **Alternative**: use a minimal `environment.yml` (lighter solve, but a bit less pinned).

> **OS support**: macOS (Apple Silicon), Linux, Windows.
> **GPU**: macOS uses Apple **MPS** automatically. Linux/Windows users with NVIDIA GPUs can install **CUDA** wheels for PyTorch (see below).

---

## 0) Prerequisites

* Install **Miniforge** (or Mambaforge): [https://conda-forge.org/miniforge/](https://conda-forge.org/miniforge/)
* (Optional) Faster solver:

  ```bash
  conda config --set channel_priority flexible
  conda config --set solver libmamba
  ```

  If you hit solver issues, switch temporarily to classic:

  ```bash
  CONDA_SOLVER=classic conda info  # just to confirm
  ```

---

## 1) Clone the repo

```bash
git clone https://github.com/mhuertascompany/euclid-school-2025.git
cd euclid-school-2025
```

---

## 2A) Create the environment (exact, reproducible)

This uses **`environment_full.yml`** (includes locked versions and a `pip:` block for PyTorch 2.5.1 / torchvision 0.20.1 / torchaudio 2.5.1).

```bash
# If libmamba solver gives you grief, prepend CONDA_SOLVER=classic
conda env create -f env/environment_full.yml
conda activate euclid-school
```

> Tip: If you edit the file later, update the env with:
>
> ```bash
> conda env update -f env/environment_full.yml --prune
> ```

### Verify the install

```bash
python - <<'PY'
import torch, sys
def yes(x): 
    return bool(x) if isinstance(x, (bool,int)) else bool(getattr(torch.backends, 'mps', None) and torch.backends.mps.is_available()) if x=='MPS' else False
print("Python:", sys.version.split()[0])
print("Torch:", torch.__version__)
print("CUDA available:", torch.cuda.is_available())
print("MPS available:", hasattr(torch.backends, 'mps') and torch.backends.mps.is_available())
if torch.cuda.is_available():
    print("CUDA device:", torch.cuda.get_device_name(0))
PY
```

---

## 2B) (Optional) Minimal environment

If you prefer a lighter spec, add a **`environment.yml`** to env/ with:

```yaml
name: euclid-school
channels:
  - conda-forge
dependencies:
  - python=3.11
  - numpy
  - scipy
  - pandas
  - scikit-learn=1.5.2
  - matplotlib
  - seaborn
  - h5py
  - pillow
  - tqdm
  - jupyterlab
  - ipykernel
  - pip
  - pip:
      - torch==2.5.1
      - torchvision==0.20.1
      - torchaudio==2.5.1
```

Then:

```bash
conda env create -f env/environment.yml
conda activate euclid-school
```

> **Why PyTorch via pip?** It’s the most portable across macOS/Linux/Windows for this course. macOS users get Apple **MPS** automatically; Linux/Windows users can swap to CUDA wheels (below).

---

## 3) Register the Jupyter kernel

```bash
python -m ipykernel install --user --name euclid-school --display-name "Python (euclid-school)"
```

Then open Jupyter Lab and pick **Python (euclid-school)** as the kernel:

```bash
jupyter lab
```

---

## 4) NVIDIA / CUDA (Linux & Windows)

PyTorch in the YAML installs CPU (and MPS on macOS). If you have an NVIDIA GPU and drivers, you can switch to CUDA wheels **inside the same env**:

1. **Check drivers first**

   * **Windows**: open *Device Manager → Display adapters* → you should see *NVIDIA ...*. Or run **NVIDIA Control Panel**.

   * **Linux**:

     ```bash
     nvidia-smi
     ```

     If it prints a table with driver & CUDA version, you’re good.

   * If not installed:

     * **Windows**: install the latest Game Ready/Studio Driver from NVIDIA.
     * **Ubuntu**:

       ```bash
       sudo apt update
       ubuntu-drivers devices
       sudo ubuntu-drivers autoinstall
       sudo reboot
       ```

2. **Install CUDA wheels for PyTorch** (example for CUDA 12.1):

   ```bash
   conda activate euclid-school
   pip uninstall -y torch torchvision torchaudio
   pip install --index-url https://download.pytorch.org/whl/cu121 \
       torch==2.5.1+cu121 torchvision==0.20.1+cu121 torchaudio==2.5.1+cu121
   ```

3. **Verify CUDA**

   ```bash
   python - <<'PY'
   ```

import torch
print("Torch:", torch.**version**)
print("CUDA available:", torch.cuda.is\_available())
if torch.cuda.is\_available():
print("CUDA device:", torch.cuda.get\_device\_name(0))
PY

````

> If CUDA isn’t detected, double-check drivers and that you used the **CUDA wheel** (`+cu121` and the `--index-url`).

---
````

## ☁️ Google Colab option

If you can’t (or don’t want to) set up locally:

1. Open [colab.research.google.com](https://colab.research.google.com) → **GitHub** tab → paste:

   ```
   https://github.com/mhuertascompany/euclid-school-2025
   ```
2. Pick a notebook.
3. Runtime → **Change runtime type** → Hardware accelerator: **GPU** → Save.
4. The first cell will cd into `Y1/notebooks/`, install minimal deps, download the ZIP, unzip to `./zoo-data/`, and show the device.

> Colab already ships with PyTorch + CUDA. Avoid reinstalling `torch/torchvision/torchaudio` unless you know why.




