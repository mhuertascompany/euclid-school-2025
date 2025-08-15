ressources for the 2025 edition of the Euclid France School

# Environment setup (conda-lock, fully pinned)

We provide **platform-specific lockfiles** so you can reproduce the exact environment we used for the notebooks.

## 0) Prereqs

* **Git**
* One of:

  * **micromamba** (recommended; fastest), or
  * **conda/mamba** (works too)

### Install micromamba (recommended)

* **macOS / Linux (bash/zsh):**

  ```bash
  /bin/bash -c "$(curl -L micro.mamba.pm/install.sh)"
  micromamba shell init -s bash -y   # or zsh/fish, etc.
  exec $SHELL
  ```
* **Windows (PowerShell):**

  ```powershell
  irm https://micro.mamba.pm/install.ps1 | iex
  micromamba shell init -s powershell -y
  # Close & reopen the terminal after this step
  ```

## 1) Clone the repo

```bash
git clone https://github.com/mhuertascompany/euclid-school-2025.git
cd euclid-school-2025
```

## 2) Create the environment from the lockfile (choose your platform)

> If you installed **micromamba**, use the first command in each block.
> If you only have **conda**, use the “conda-lock install” variant.

### macOS (Apple Silicon, arm64)

```bash
# micromamba
micromamba create -n euclid -f env/conda-osx-arm64.lock.yml

# OR conda
pip install conda-lock
conda-lock install -n euclid env/conda-osx-arm64.lock.yml
```

### Linux (x86\_64)

```bash
# micromamba
micromamba create -n euclid -f env/conda-linux-64.lock.yml

# OR conda
pip install conda-lock
conda-lock install -n euclid env/conda-linux-64.lock.yml
```

### Windows (x86\_64; run in “Anaconda Prompt” or PowerShell)

```powershell
# micromamba
micromamba create -n euclid -f env/conda-win-64.lock.yml

# OR conda
pip install conda-lock
conda-lock install -n euclid env/conda-win-64.lock.yml
```

## 3) Activate the environment

```bash
# micromamba
micromamba activate euclid

# OR conda
conda activate euclid
```

## 4) Verify PyTorch device & launch notebooks

```bash
python - << 'PY'
import torch, sys
print("python:", sys.version.split()[0])
print("torch:", torch.__version__)
print("CUDA available:", torch.cuda.is_available())
print("MPS available:", getattr(torch.backends, "mps", None) and torch.backends.mps.is_available())
PY
```

Launch Jupyter:

```bash
jupyter lab
# or: jupyter notebook
```

> Tip (Jupyter kernel): launching Jupyter **from inside** the environment is enough.
> If you prefer selecting kernels, you can also run:
>
> ```bash
> python -m ipykernel install --user --name euclid --display-name "euclid (conda)"
> ```

---

## Notes for Mac (Apple Silicon, MPS)

* Some models (e.g., certain ViT variants) call **bicubic** interpolation, which is not implemented on MPS in some PyTorch builds. Two workarounds:

  1. Add this at the **top of a notebook (first cell, before `import torch`)**:

     ```python
     import os; os.environ["PYTORCH_ENABLE_MPS_FALLBACK"] = "1"
     ```

     This keeps most ops on GPU and falls back to CPU only for the missing ones.
  2. If a specific model still errors, the notebook may include a small **monkey-patch** to switch bicubic → bilinear for positional embedding interpolation.

---

## Common pitfalls / troubleshooting

* **Wrong lockfile / architecture**
  Apple Silicon users: use `conda-osx-arm64.lock.yml`.
  Intel Macs: use an **osx-64** lockfile (if provided), otherwise install via conda from `environment.yml`.

* **Windows Antivirus / long paths**
  If installs fail, try running as Admin, shorten the path (e.g., `C:\envs\euclid`), or exclude the env folder in AV.

* **Proxy / corporate networks**
  You may need to set `HTTP_PROXY`/`HTTPS_PROXY` env vars for package downloads.

* **Outdated conda/mamba**
  Update your solver if environment creation is slow or fails to resolve:

  ```bash
  conda update -n base -c conda-forge conda
  # or install micromamba (faster)
  ```


