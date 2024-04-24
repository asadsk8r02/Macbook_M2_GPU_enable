# Steps to enable tensorflow GPU support on Macbook Pro M2 - Tensorflow-metal

These are step-by-step instructions for enabling TensorFlow GPU support on a MacBook Pro M2 using TensorFlow-metal. It outlines the necessary requirements, including Mac computers with Apple silicon M2, macOS version 12.0 or later, and Python version 3.8 or later. The guide covers setting up a virtual environment, installing TensorFlow and its dependencies, configuring the IPython kernel for Jupyter Notebook, and validating GPU usage. Optional configurations for TensorFlow GPU utilization are also included.

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

<br>

### 3. Upgrade pip to the latest version

```bash
python -m pip install --upgrade pip
```

This command ensures that you have the latest version of pip installed in your virtual environment.

<br>

### 4. Install TensorFlow 2.14

```bash
python -m pip install tensorflow==2.14.0
```

This command installs TensorFlow 2.14 in your virtual environment.

<br>

### 5. Install tensorflow-metal 1.1.0

```bash
python -m pip install tensorflow-metal==1.1.0
```

This command installs tensorflow-metal version 1.1.0, which is known to be compatible with TensorFlow 2.14.
I this doesn't work, you can downgrade to version 0.7.0.

<br>

### 6. Install ipykernel

```bash
pip install ipykernel


python -m ipykernel install --user --name=tf_metal_env
```

This will install the IPython kernel package. Once installed, you can then add the IPython kernel to Jupyter.

<br>

### 7. Open Jupyter Notebook.

```bash
jupyter notebook
```

<br>

### 8. Validation and usage.

Change or check the kernel to **tf_metal_env** in jupyter notebook: **Kernal> Change kernel> tf_metal_env**

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
Devices: [PhysicalDevice(name='/physical_device:CPU:0', device_type='CPU'), PhysicalDevice(name='/physical_device:GPU:0', device_type='GPU')]
GPU details: {'device_name': 'METAL'}
```

<br>

### Optional

You can set up TensorFlow to utilize the GPU, enabling memory growth and setting the visible device to the GPU if available.

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

```bash
GPU is available and set to use.
```

You can configure TensorFlow to utilize the GPU exclusively if available, allowing memory growth and setting the visible device accordingly. If no GPU is detected, TensorFlow will default to using the CPU.

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

```bash
TensorFlow is set to use only GPU.
```

<br>

### Verify tensorflow-metal version

You can retrieve the version of "tensorflow-metal" using pkg_resources and print it.

```python
import pkg_resources

tf_metal_version = pkg_resources.get_distribution("tensorflow-metal").version
print("tensorflow-metal version:", tf_metal_version)
```

tensorflow-metal version: 1.1.0

<br>

### 9. Demo

You can evaluate the performance enhancement using this script. Execute it once with GPU (metal) support enabled and once within a virtual environment lacking metal installation.

```python
import tensorflow as tf
cifar = tf.keras.datasets.cifar100
(x_train, y_train), (x_test, y_test) = cifar.load_data()
model = tf.keras.applications.ResNet50(
  include_top=True,
  weights=None,
  input_shape=(32, 32, 3),
  classes=100,)

loss_fn = tf.keras.losses.SparseCategoricalCrossentropy(from_logits=False)

model.compile(optimizer="adam", loss=loss_fn, metrics=["accuracy"])

model.fit(x_train, y_train, epochs=5, batch_size=64)
```

```bash
2024-04-24 13:07:05.516306: I metal_plugin/src/device/metal_device.cc:1154] Metal device set to: Apple M2 Pro
2024-04-24 13:07:05.516327: I metal_plugin/src/device/metal_device.cc:296] systemMemory: 16.00 GB
2024-04-24 13:07:05.516333: I metal_plugin/src/device/metal_device.cc:313] maxCacheSize: 5.33 GB
2024-04-24 13:07:05.516523: I tensorflow/core/common_runtime/pluggable_device/pluggable_device_factory.cc:306] Could not identify NUMA node of platform GPU ID 0, defaulting to 0. Your kernel may not have been built with NUMA support.
2024-04-24 13:07:05.516871: I tensorflow/core/common_runtime/pluggable_device/pluggable_device_factory.cc:272] Created TensorFlow device (/job:localhost/replica:0/task:0/device:GPU:0 with 0 MB memory) -> physical PluggableDevice (device: 0, name: METAL, pci bus id: <undefined>)
Epoch 1/5
2024-04-24 13:07:07.921342: I tensorflow/core/grappler/optimizers/custom_graph_optimizer_registry.cc:117] Plugin optimizer for device_type GPU is enabled.
782/782 [==============================] - 48s 57ms/step - loss: 4.7597 - accuracy: 0.0696
Epoch 2/5
782/782 [==============================] - 45s 57ms/step - loss: 4.1770 - accuracy: 0.1240
Epoch 3/5
782/782 [==============================] - 44s 57ms/step - loss: 4.0678 - accuracy: 0.1433
Epoch 4/5
782/782 [==============================] - 44s 57ms/step - loss: 4.0673 - accuracy: 0.1178
Epoch 5/5
782/782 [==============================] - 45s 57ms/step - loss: 3.8240 - accuracy: 0.1616
<keras.src.callbacks.History at 0x319f07c90>
```
