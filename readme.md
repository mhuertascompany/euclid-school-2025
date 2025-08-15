# EUCLID School 2025 — Notebooks & Environment

This repo contains the notebooks and data used in the EUCLID School 2025 lectures and labs.

Supported options:

- **Local installs** with **fully pinned conda-lock lockfiles**
  - macOS (Apple Silicon / MPS)
  - Linux (CUDA **or** CPU-only)
  - Windows (CUDA **or** CPU-only)
- A **Google Colab** option with a ready-to-use **bootstrap cell** (see bottom of this readme)

---

## 🔢 Requirements

- **Git**
- **micromamba** (recommended) or **conda**
- If you want **GPU** on Linux/Windows: an **NVIDIA GPU + drivers** (see below)

---

## 🧰 Choose an environment (lockfiles)

Use the per-platform lockfiles in `env/`:

| OS | GPU? | Lockfile |
|---|---|---|
| macOS (Apple Silicon) | MPS (Apple GPU) | `env/conda-osx-arm64.lock.yml` |
| Linux (x86_64) | **CUDA** | `env/conda-linux-64-cuda.lock.yml` |
| Linux (x86_64) | **CPU-only** | `env/conda-linux-64-cpu.lock.yml` |
| Windows (x86_64) | **CUDA** | `env/conda-win-64-cuda.lock.yml` |
| Windows (x86_64) | **CPU-only** | `env/conda-win-64-cpu.lock.yml` |

> Maintainers: YAMLs that produce these are under `env/environment-*.yml`.

---

## 🚀 Install from a lockfile

**micromamba (recommended)**
```bash
micromamba create -n euclid -f env/<chosen-lockfile>
micromamba activate euclid
````

**conda**

```bash
pip install conda-lock
conda-lock install -n euclid env/<chosen-lockfile>
conda activate euclid
```

Verify:

```bash
python - << 'PY'
import torch, sys
print("python:", sys.version.split()[0])
print("torch:", torch.__version__)
print("CUDA available:", torch.cuda.is_available())
print("MPS available:", getattr(torch.backends, "mps", None) and torch.backends.mps.is_available())
PY
```

Launch:

```bash
jupyter lab    # or: jupyter notebook
```

> **macOS MPS note:** put this at the *top* of GPU notebooks to gracefully fall back to CPU for a few missing MPS ops:
>
> ```python
> import os; os.environ["PYTORCH_ENABLE_MPS_FALLBACK"] = "1"
> ```

---

## 💾 Getting the data (`zoo-data`) — **ZIP download**

All notebooks that use the dataset live under **`Y1/notebooks/`** and expect a local folder:

```
Y1/notebooks/zoo-data/
  train/<class>/*
  test/<class>/*
```

### Easiest (works on both local & Colab)

Rune the **first cell** of your notebook, whcih should download the data to the correct location for everything to work.


### Local manual download (alternative)

```bash
cd euclid-school-2025/Y1/notebooks
curl -L -o euclid-zoo-data.zip https://astdp.net/euclid-zoo-data
unzip -q euclid-zoo-data.zip
# You should now have: Y1/notebooks/zoo-data/train/... and /test/...
```

---

## 🖥️ NVIDIA GPU drivers (Linux/Windows, CUDA path only)

### Check

**Windows**

```powershell
nvidia-smi   # should print a table with Driver Version & CUDA Version
```

**Linux**

```bash
lspci | grep -i nvidia
nvidia-smi
```

### Install / Update

**Windows (GeForce / RTX):**

* Download the latest **Game Ready** or **Studio** driver from NVIDIA and install → **reboot** → run `nvidia-smi`.

**Ubuntu/Debian:**

```bash
ubuntu-drivers devices
sudo ubuntu-drivers autoinstall
sudo reboot
```

**Fedora:**

```bash
sudo dnf install \
  https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm \
  https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
sudo dnf install akmod-nvidia xorg-x11-drv-nvidia-cuda
sudo reboot
```

**Arch:**

```bash
sudo pacman -Syu nvidia nvidia-utils
sudo reboot
```

Verify:

```bash
nvidia-smi
python - << 'PY'
import torch
print("CUDA available:", torch.cuda.is_available())
print("Device:", torch.cuda.get_device_name(0) if torch.cuda.is_available() else "CPU")
PY
```

---

## ☁️ Google Colab option

If you can’t (or don’t want to) set up locally:

1. Open [colab.research.google.com](https://colab.research.google.com) → **GitHub** tab → paste:

   ```
   https://github.com/mhuertascompany/euclid-school-2025
   ```
2. Pick a notebook.
3. Runtime → **Change runtime type** → Hardware accelerator: **GPU** → Save.
4. Paste the **bootstrap cell** (first cell) and run it.
   It will cd into `Y1/notebooks/`, install minimal deps, download the ZIP, unzip to `./zoo-data/`, and show the device.

> Colab already ships with PyTorch + CUDA. Avoid reinstalling `torch/torchvision/torchaudio` unless you know why.




