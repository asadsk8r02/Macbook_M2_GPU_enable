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

### 4. Install TensorFlow 2.14

```bash
ython -m pip install tensorflow==2.14.0
```

This command installs TensorFlow 2.14 in your virtual environment.

### 5. Install tensorflow-metal 1.1.0

```bash
python -m pip install tensorflow-metal==1.1.0
```

This command installs tensorflow-metal version 1.1.0, which is known to be compatible with TensorFlow 2.14.
I this doesn't work, you can downgrade to version 0.7.0.

### 6. Install ipykernel

```bash
pip install ipykernel

#

python -m ipykernel install --user --name=tf_metal_env
```

This will install the IPython kernel package. Once installed, you can then add the IPython kernel to Jupyter.

### 7. Open Jupyter Notebook.

```bash
jupyter notebook
```

### 8. Validation and Usage.

Change or check the kernel to tf_metal_env in jupyter notebook: Kernal> Change kernel> tf_metal_env

Following this you can run this script to validate and check the GPU.

```python
import tensorflow as tf
devices = tf.config.list_physical_devices()
print("\nDevices: ", devices)

gpus = tf.config.list_physical_devices('GPU')
if gpus:
  details = tf.config.experimental.get_device_details(gpus[0])
  print("GPU details: ", details)
```

```bash
Devices:  [PhysicalDevice(name='/physical_device:CPU:0', device_type='CPU'), PhysicalDevice(name='/physical_device:GPU:0', device_type='GPU')]
GPU details:  {'device_name': 'METAL'}
```

```python
import tensorflow as tf

# Specify GPU device
gpus = tf.config.experimental.list_physical_devices('GPU')
if gpus:
    try:
        # Currently, memory growth needs to be the same across GPUs
        for gpu in gpus:
            tf.config.experimental.set_memory_growth(gpu, True)
        # Set visible devices to use only GPU
        tf.config.experimental.set_visible_devices(gpus[0], 'GPU')
        print("GPU is available and set to use.")
    except RuntimeError as e:
        # Memory growth must be set before GPUs have been initialized
        print(e)
else:
    print("No GPU available, using CPU instead.")
```

GPU is available and set to use.

```python
import tensorflow as tf

# Specify GPU device
gpus = tf.config.experimental.list_physical_devices('GPU')
if gpus:
    try:
        # Currently, memory growth needs to be the same across GPUs
        for gpu in gpus:
            tf.config.experimental.set_memory_growth(gpu, True)
        # Set visible devices to use only GPU
        tf.config.experimental.set_visible_devices(gpus[0], 'GPU')
        print("TensorFlow is set to use only GPU.")
    except RuntimeError as e:
        # Memory growth must be set before GPUs have been initialized
        print(e)
else:
    print("No GPU available, TensorFlow will use CPU by default.")
```

TensorFlow is set to use only GPU.
