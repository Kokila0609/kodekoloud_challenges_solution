# Python Package Distribution Guide: Fraud Detection Model

## Overview & Requirements
The xFusionCorp Industries deployment team requires the `fraud-detection` model code to be bundled into a standard, installable Python wheel distribution. 

### Infrastructure Baseline
*   **Project Path:** `/root/code/fraud-detection/`
*   **Source Code Directory:** Located under `src/fraud_detection/` (Complete—do not modify).
*   **Target Output:** A compliant distribution wheel named `fraud_detection-0.1.0-*.whl` located inside the `dist/` directory.

---

## Step 1: Correct the `pyproject.toml` Configuration

The existing `pyproject.toml` must be updated because the project `name` field erroneously uses a hyphen (`-`) instead of an underscore (`_`), which prevents the distribution name from matching the module path under `src/`.

Open the file `/root/code/fraud-detection/pyproject.toml` and apply the following verified configuration:

```toml
[project]
name = "fraud_detection"
version = "0.1.0"
description = "Fraud detection model for xFusionCorp Industries"
requires-python = ">=3.10"
dependencies = [
    "scikit-learn",
    "pandas",
    "numpy"
]

[build-system]
requires = ["setuptools>=61.0", "wheel"]
build-backend = "setuptools.build_meta"

[tool.setuptools.packages.find]
where = ["src"]
```

### Key Corrections Made:
*   **`name` parameter:** Changed from `"fraud-detection"` to `"fraud_detection"` to match the module path directory exactly.
*   **`[tool.setuptools.packages.find]` block:** Retained because the source code layout relies on the `src/` subfolder structure.

---

## Step 2: Build the Python Distribution Package

Navigate to the project root directory and invoke the standard Python build module to compile the source code into binary and source distributions:

```bash
# Navigate to the project directory
cd /root/code/fraud-detection

# Execute the package build process
python3 -m build
```

---

## Step 3: Verify the Build Output

Once the build engine completes execution, check the newly created `dist/` folder to ensure the compiled artifacts conform to formatting regulations:

```bash
ls -l dist/
```

### Expected Output Checklist
Verify that the following two artifacts exist within the directory tree:
1.  **The Wheel Asset:** `fraud_detection-0.1.0-py3-none-any.whl` (or similar standard target architecture string).
2.  **The Source Archive:** `fraud_detection-0.1.0.tar.gz`.
