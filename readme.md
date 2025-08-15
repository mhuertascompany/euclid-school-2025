# EUCLID School 2025 — Notebooks & Environment

This repo contains the notebooks and data used in the EUCLID School 2025 lectures and labs.

We support:

- **Local installs** with **fully pinned conda-lock lockfiles**
  - macOS (Apple Silicon / MPS)
  - Linux (CUDA **or** CPU-only)
  - Windows (CUDA **or** CPU-only)
- A **Google Colab** option with a ready-to-use **bootstrap cell**

---

## 🔢 What you need

- **Git**
- **micromamba** (recommended) or **conda**
- If you want **GPU** on Linux/Windows: an **NVIDIA GPU + drivers** (see below)

---

## 🧰 Choose an environment (lockfiles)

We ship platform-specific lockfiles in `env/`:

| OS | GPU? | Lockfile |
|---|---|---|
| macOS (Apple Silicon) | MPS (Apple GPU) | `env/conda-osx-arm64.lock.yml` |
| Linux (x86_64) | **CUDA** | `env/conda-linux-64-cuda.lock.yml` |
| Linux (x86_64) | **CPU-only** | `env/conda-linux-64-cpu.lock.yml` |
| Windows (x86_64) | **CUDA** | `env/conda-win-64-cuda.lock.yml` |
| Windows (x86_64) | **CPU-only** | `env/conda-win-64-cpu.lock.yml` |

> Maintainers: the YAMLs used to produce those lockfiles live in `env/environment-*.yml`.

---

## 🚀 Install from a lockfile

> **micromamba (recommended):**
```bash
micromamba create -n euclid -f env/<chosen-lockfile>
micromamba activate euclid
