# Steps to enable tensorflow GPU on Macbook Pro M2 - Tensorflow-metal

## Requirements

Mac computers with Apple silicon M2

macOS 12.0 or later

Python 3.8 or later

Xcode command-line tools: xcode-select --install

## Get started

### 1. Create a new virtual environment with Python 3.11

```bash
python3.11 -m venv ~/tf_metal_env
```

This command creates a new virtual environment named "tf_metal_env" in your home directory using Python 3.11.

### 2. Activate the virtual environment

```bash
source ~/tf_metal_env/bin/activate
```

This command activates the virtual environment, so any subsequent Python commands will run within this isolated environment.

### 3. Upgrade pip to the latest version

```bash
python -m pip install --upgrade pip
```

This command ensures that you have the latest version of pip installed in your virtual environment.
