# Fixing and Deploying the Cookiecutter ML Template

## Requirements
The xFusionCorp Industries ML platform team maintains a Cookiecutter template for generating new ML projects. The draft template located at `/root/code/mlops-template/` is currently failing to render. 

The corrected template must satisfy the following constraints:
1. **`cookiecutter.json`** variables:
   * `project_name` (default: `my-ml-project`)
   * `author` (default: `xFusionCorp`)
   * `python_version` (default: `3.11`)
   * `ml_framework` (choices: `sklearn`, `pytorch`, `tensorflow`)
2. **`requirements.txt`** conditional logic:
   * Contains `scikit-learn` if `ml_framework` is `sklearn`
   * Contains `torch` if `ml_framework` is `pytorch`
   * Contains `tensorflow` if `ml_framework` is `tensorflow`
3. **`README.md`** content: Must reference both `project_name` and `author` variables.
4. **Directory Structure** inside `{{cookiecutter.project_name}}/`:
   * Files: `README.md`, `requirements.txt`
   * Directories: `data/`, `models/`, `src/`, `tests/`

---

## Template Corrections

### 1. Fix Syntax Error in `cookiecutter.json`
**Issue:** Missing a comma separation between `"python_version"` and `"ml_framework"`, which causes invalid JSON parsing.

* **Before:**
  ```json
  {
      "project_name": "my-ml-project",
      "author": "xFusionCorp",
      "python_version": "3.11"
  
  }
  ```
* **After (Corrected):**
  ```json
  {
      "project_name": "my-ml-project",
      "author": "xFusionCorp",
      "python_version": "3.11",
      "ml_framework": ["sklearn", "pytorch", "tensorflow"]
  }
  ```

### 2. Fix Jinja2 Syntax in `requirements.txt`
**Issue:** Single equals signs (`=`) were used for assignment instead of comparison operators (`==`), and the block statement was missing its closing `{% endif %}` tag.

* **Before:**
  ```jinja
  {% if cookiecutter.ml_framework = 'sklearn' %}
  scikit-learn
  {% elif cookiecutter.ml_framework = 'pytorch' %}
  torch
  {% elif cookiecutter.ml_framework = 'tensorflow' %}
  tensorflow
  ```
* **After (Corrected):**
  ```jinja
  {% if cookiecutter.ml_framework == 'sklearn' %}
  scikit-learn
  {% elif cookiecutter.ml_framework == 'pytorch' %}
  torch
  {% elif cookiecutter.ml_framework == 'tensorflow' %}
  tensorflow
  {% endif %}
  ```

### 3. Fix Variable Casing in `README.md`
**Issue:** The template referenced `Author` instead of the exact variable casing `author` defined in `cookiecutter.json`. 

* **Correction:** Updated variable tokens to lowercase to match the configurations exactly (e.g., `{{ cookiecutter.author }}`).

---

## Project Generation Execution

Once the template files are corrected, generate the project inside the `/root/code/churn-model/` directory with the required parameters:

```bash
cookiecutter /root/code/mlops-template/ -o /root/code/ --no-input project_name=churn-model ml_framework=sklearn
```

### Verification
* The directory `/root/code/churn-model/requirements.txt` now successfully lists `scikit-learn`.
* The `README.md` file correctly renders and mentions `xFusionCorp` as the author.
