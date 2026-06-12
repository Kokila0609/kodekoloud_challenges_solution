# Python Virtual Environment Setup Guide

This guide walks you through creating a Python virtual environment, installing machine learning libraries, and saving the environment configuration.

## Steps

### 1. Create the Virtual Environment
Create an isolated environment named `ml-env` under the `/root/code/` directory.

```bash
root@controlplane ~/code ➜  python3 -m venv /root/code/ml-env
```

### 2. Activate the Python Environment
Activate the environment to start using it.

```bash
root@controlplane ~/code ➜  source ml-env/bin/activate
```

### 3. Install Packages
Install `numpy`, `pandas`, `scikit-learn`, and `matplotlib` inside the active environment.

```bash
root@controlplane ~/code via 🐍 v3.12.3 (ml-env) ➜  pip install numpy pandas scikit-learn matplotlib
```

### 4. Generate Requirements File
Use `pip freeze` to save your package list to a `requirements.txt` file.

```bash
root@controlplane ~/code via 🐍 v3.12.3 (ml-env) ➜  pip freeze > /root/code/requirements.txt
```

---

## References
* [Python `venv` Documentation](https://docs.python.org/3/library/venv.html)
* [Pip `pip freeze` Documentation](https://pip.pypa.io/en/stable/cli/pip_freeze/)
