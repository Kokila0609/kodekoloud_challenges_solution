# JupyterLab Configuration and Startup Guide

Follow these steps to configure and start JupyterLab within your virtual environment using the required team settings.

### 1. Create the Notebook Directory
Ensure the required root directory for your notebooks exists on the disk:
```bash
mkdir -p /root/notebooks/
```

### 2. Configure JupyterLab Settings
Open the configuration file located at `/root/code/jupyter_lab_config.py` and verify or update the following settings to match the team requirements:

```python
# /root/code/jupyter_lab_config.py

# --- xFusionCorp team overrides ---
c.ServerApp.token = ''
c.ServerApp.password = ''
c.ServerApp.disable_check_xsrf = True

# Required Settings:
c.ServerApp.notebook_dir = '/root/notebooks/'  # Sets the root directory
c.ServerApp.port = 8888                         # Listens on port 8888
c.ServerApp.ip = '0.0.0.0'                      # Binds to all network interfaces
```

### 3. Activate the Environment and Start JupyterLab
Activate the Python virtual environment and launch the JupyterLab server in the background:
```bash
source /root/code/ml-env/bin/activate
jupyter lab --config=/root/code/jupyter_lab_config.py --allow-root --no-browser &
```
