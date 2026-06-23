# Code Quality Configuration for Fraud Detection Project

## 🎯 Requirement
The xFusionCorp Industries ML team enforces code quality using **Ruff** and **Black** on every pull request. The project located at `/root/code/fraud-detection/` contains a `pyproject.toml` configuration and sample source files under `src/`. Currently, the codebase fails both quality tools. You must update the configuration and source files to make them pass cleanly.

### 📋 Technical Specifications
The updated project setup must meet the following precise criteria:
* **Line Length**: Both `ruff` and `black` must be configured with a maximum line length of `120`.
* **Ruff Rules**: Lint rule selections must include `E`, `F`, `W`, and `I`.
* **Ruff Schema**: Rules must be declared under the `[tool.ruff.lint]` block (the mandatory schema for Ruff v0.1 and later).
* **Validation Status**: Both verification commands below must exit with a status code of `0`:
  * `ruff check src/`
  * `black --check src/`

*Note: `ruff`, `black`, and `mypy` are already pre-installed in the environment.*

---

## 🛠️ Tool Responsibilities
* **Ruff**: Analyzes your code syntax without running it to ensure it makes logical sense, catches bugs early, and clears out unused imports or dead code.
* **Black**: Ensures that the remaining clean code looks visually beautiful, structured, and consistent across your entire engineering team.

---

## 📝 Configuration Updates

Update your `/root/code/fraud-detection/pyproject.toml` file with the following layout to satisfy the line limits and the modern Ruff 0.1+ nested schema requirement:

```toml
[project]
name = "fraud-detection"
version = "0.1.0"

[tool.black]
line-length = 120

[tool.ruff]
line-length = 120

[tool.ruff.lint]
select = ["E", "F", "W", "I"]
```

---

## 🚀 Execution & Verification Steps

Run these commands sequentially from the `/root/code/fraud-detection/` directory to fix the source code and confirm compliance:

### 1. Automatically Sort Imports and Fix Lint Issues
```bash
ruff check src/ --fix
```

### 2. Format Files to Fit the 120 Character Limit
```bash
black src/
```

### 3. Final Validation Checks
Verify that both commands now exit cleanly with a status code of `0`:
```bash
ruff check src/
black --check src/
```
