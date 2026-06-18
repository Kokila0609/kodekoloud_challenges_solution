# Requirements:

A colleague has started a new ML project at /root/code/fraud-detection/, but the layout does not match the xFusionCorp Industries standard. Bring the project in line with the team's conventions.


Inspect the existing project at /root/code/fraud-detection/.

The final layout must match the tree below exactly:

```
fraud-detection/
├── data/
│   ├── raw/
│   └── processed/
├── models/
├── notebooks/
├── src/
│   ├── data/
│   ├── features/
│   ├── models/
│   └── utils/
├── tests/
├── configs/
├── requirements.txt
└── README.md
```

Every subdirectory under src/ must contain an __init__.py file so that Python recognises it as a package.

requirements.txt must list the following dependencies, one per line: scikit-learn, pandas, numpy, and mlflow. The canonical PyPI name for the scikit-learn package is scikit-learn.

README.md must begin with the heading # fraud-detection.

Review the existing project and correct everything that does not match the requirements above.


# Steps

# Fraud Detection Project Setup

Follow these steps to fix the project layout and match the company standard.

## 1. Create Missing Folders

Run these commands to build the required folder structure:

*   `mkdir -p /root/code/fraud-detection/{tests,configs}`
*   `mkdir -p /root/code/fraud-detection/data/{raw,processed}`
*   `mkdir -p /root/code/fraud-detection/{models,notebooks,src}`
*   `mkdir -p /root/code/fraud-detection/src/{data,features,models,utils}`

## 2. Rename Existing Folders

Fix misspelled folder names to match the standard layout:

*   `mv /root/code/fraud-detection/src/feature /root/code/fraud-detection/src/features`
*   `mv /root/code/fraud-detection/src/util /root/code/fraud-detection/src/utils`

## 3. Create Package Init Files

Add an empty `__init__.py` file to every folder under `src`:

*   `touch /root/code/fraud-detection/src/__init__.py`
*   `touch /root/code/fraud-detection/src/data/__init__.py`
*   `touch /root/code/fraud-detection/src/features/__init__.py`
*   `touch /root/code/fraud-detection/src/models/__init__.py`
*   `touch /root/code/fraud-detection/src/utils/__init__.py`

## 4. Setup Project Files

Create the required configuration and documentation files:

*   `echo -e "scikit-learn\npandas\nnumpy\nmlflow" > /root/code/fraud-detection/requirements.txt`
*   `echo "# fraud-detection" > /root/code/fraud-detection/README.md`
