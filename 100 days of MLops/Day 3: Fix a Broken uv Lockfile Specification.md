# Python Dependency Management Guide with uv

Follow these steps to update your high-level package specifications and compile them into a pinned lockfile.

### 1. Update the Requirements Specification
Open the `/root/code/fraud-detection/requirements.in` file and update it to list exactly the four required top-level packages with their version constraints:

```text
# /root/code/fraud-detection/requirements.in

# Fraud detection project dependencies
scikit-learn>=1.5
mlflow>=2.12
pandas>=2.2
numpy>=1.26
```

### 2. Compile the Specification into a Lockfile
Navigate to your project directory and run the compilation command. This resolves the versions against PyPI and includes all necessary background (transitive) dependencies:
```bash
uv pip compile requirements.in -o requirements.txt
```

### 3. Verify the Outputs
Confirm that the compilation was successful by checking the newly generated file. The resulting `requirements.txt` file will pin all packages to exact versions using `==`:
```bash
cat requirements.txt
```
