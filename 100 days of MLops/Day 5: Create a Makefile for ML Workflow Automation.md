# MLOps Makefile Correction Guide

This document outlines the requirements and the final corrected structure for the `fraud-detection` project Makefile. 

---

## 📋 Requirements

The xFusionCorp Industries ML team uses a Makefile to orchestrate common tasks: data processing, training, testing, and cleanup. The corrected Makefile must meet the following criteria:

* **Location**: `/root/code/fraud-detection/Makefile`
* **Tab Indentation**: Every command recipe must be indented with a **real tab character**, not spaces.
* **Phony Targets**: All 6 targets must be declared under `.PHONY` to avoid file name conflicts.

### Target Tasks and Behaviours:
1. **`setup`** – Creates a virtual environment at `mlops-venv/` and installs dependencies from `requirements.txt`.
2. **`data`** – Runs `python src/data/process_data.py`.
3. **`train`** – Runs `python src/models/train.py`.
4. **`test`** – Runs `pytest tests/`.
5. **`clean`** – Recursively removes every `__pycache__` directory, removes `.pytest_cache`, and clears the contents of `models/`.
6. **`all`** – Runs `setup`, `data`, `train`, and `test` in that exact order.

---

## 🛠️ Execution Steps

1. Change into the project directory:
   ```bash
   cd /root/code/fraud-detection/
   ```
2. Open the existing `Makefile` using a text editor (like `nano` or `vi`).
3. Replace the contents with the corrected template below. **Ensure your editor inserts a literal tab character for the indented lines.**
4. Run the full pipeline to verify success:
   ```bash
   make all
   ```

---

## 📝 Corrected Makefile Blueprint

```makefile
# fraud-detection Makefile
.PHONY: setup data train test clean all

setup:
	python3 -m venv mlops-venv && mlops-venv/bin/pip install -r requirements.txt

data:
	python src/data/process_data.py

train:
	python src/models/train.py

test:
	pytest tests/

clean:
	find . -type d -name "__pycache__" -exec rm -rf {} +
	rm -rf .pytest_cache
	rm -rf models/*

all: setup data train test
```

> ⚠️ **Troubleshooting Note:** If you encounter the error `Makefile: *** missing separator. Stop.`, it means spaces were used instead of tabs on the action lines. Delete the spaces at the start of the command lines and press the **Tab** key once. Avoid leaving random trailing blank spaces or extra lines inside the command blocks.
