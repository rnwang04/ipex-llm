# IPEX-LLM Installation: GPU

## Windows

> [!NOTE]
> For installation with PyTorch 2.6, please refer to this [guide](../Quickstart/install_pytorch26_gpu.md) for more information.

### Prerequisites

IPEX-LLM on Windows supports Intel iGPU and dGPU.

To apply Intel GPU acceleration, please first verify your GPU driver version.

> [!NOTE]
> The GPU driver version of your device can be checked in the "Task Manager" -> GPU 0 (or GPU 1, etc.) -> Driver version.

If you have driver version lower than `31.0.101.5122`, it is required to [**update your GPU driver to the latest**](https://www.intel.com/content/www/us/en/download/785597/intel-arc-iris-xe-graphics-windows.html).

<!-- Intel® oneAPI Base Toolkit 2024.0 installation methods:

```eval_rst
.. tabs::
   .. tab:: Offline installer
      Download and install `Intel® oneAPI Base Toolkit <https://www.intel.com/content/www/us/en/developer/tools/oneapi/base-toolkit-download.html?operatingsystem=window&distributions=offline>`_ version 2024.0 through Offline Installer.
      
      During installation, you could just continue with "Recommended Installation". If you would like to continue with "Custom Installation", please note that oneAPI Deep Neural Network Library, oneAPI Math Kernel Library, and oneAPI DPC++/C++ Compiler are required, the other components are optional.

   .. tab:: PIP installer
      Pip install oneAPI in your working conda environment.

      .. code-block:: bash

         pip install dpcpp-cpp-rt==2024.0.2 mkl-dpcpp==2024.0.0 onednn==2024.0.0

      .. note::
         Activating your working conda environment will automatically configure oneAPI environment variables.
``` -->

### Install IPEX-LLM
#### Install IPEX-LLM From PyPI

We recommend using [Miniforge](https://conda-forge.org/download/) to create a python 3.11 enviroment.

> [!IMPORTANT]
> ``ipex-llm`` is tested with Python 3.9, 3.10 and 3.11. Python 3.11 is recommended for best practices.

The easiest ways to install `ipex-llm` is the following commands.

Choose either US or CN website for `extra-index-url`:

- For **US**:

   ```cmd
   conda create -n llm python=3.11 libuv
   conda activate llm

   pip install --pre --upgrade ipex-llm[xpu] --extra-index-url https://pytorch-extension.intel.com/release-whl/stable/xpu/us/
   ```

- For **CN**:

   ```cmd
   conda create -n llm python=3.11 libuv
   conda activate llm

   pip install --pre --upgrade ipex-llm[xpu] --extra-index-url https://pytorch-extension.intel.com/release-whl/stable/xpu/cn/
   ```

#### Install IPEX-LLM From Wheel

If you encounter network issues when installing IPEX, you can also install IPEX-LLM dependencies for Intel XPU from source archives. First you need to download and install torch/torchvision/ipex from wheels listed below before installing `ipex-llm`.

Download the wheels on Windows system:

```
wget https://intel-extension-for-pytorch.s3.amazonaws.com/ipex_stable/xpu/torch-2.1.0a0%2Bcxx11.abi-cp311-cp311-win_amd64.whl
wget https://intel-extension-for-pytorch.s3.amazonaws.com/ipex_stable/xpu/torchvision-0.16.0a0%2Bcxx11.abi-cp311-cp311-win_amd64.whl
wget https://intel-extension-for-pytorch.s3.amazonaws.com/ipex_stable/xpu/intel_extension_for_pytorch-2.1.10%2Bxpu-cp311-cp311-win_amd64.whl
```

You may install dependencies directly from the wheel archives and then install `ipex-llm` using following commands:

```
pip install torch-2.1.0a0+cxx11.abi-cp311-cp311-win_amd64.whl
pip install torchvision-0.16.0a0+cxx11.abi-cp311-cp311-win_amd64.whl
pip install intel_extension_for_pytorch-2.1.10+xpu-cp311-cp311-win_amd64.whl

pip install --pre --upgrade ipex-llm[xpu]
```

> [!NOTE]
> All the wheel packages mentioned here are for Python 3.11. If you would like to use Python 3.9 or 3.10, you should modify the wheel names for ``torch``, ``torchvision``, and ``intel_extension_for_pytorch`` by replacing ``cp11`` with ``cp39`` or ``cp310``, respectively.


### Runtime Configuration

To use GPU acceleration on Windows, several environment variables are required before running a GPU example:

<!-- Make sure you are using CMD (Miniforge Prompt if using conda) as PowerShell is not supported, and configure oneAPI environment variables with: 

```cmd
call "C:\Program Files (x86)\Intel\oneAPI\setvars.bat"
```

Please also set the following environment variable if you would like to run LLMs on: -->

- For **Intel iGPU** and **Intel Arc™ A-Series Graphics**:
   ```cmd
   set SYCL_CACHE_PERSISTENT=1
   ```

> [!NOTE]
> For **the first time** that **each model** runs on Intel iGPU/Intel Arc™ A300-Series or Pro A60, it may take several minutes to compile.


### Troubleshooting

#### 1. Error loading `intel_extension_for_pytorch`

If you met error when importing `intel_extension_for_pytorch`, please ensure that `libuv` is installed in your conda environment. This can be done during the creation of the environment with the command:
  ```cmd
  conda create -n llm python=3.11 libuv
  ```
  If you missed `libuv`, you can add it to your existing environment through
  ```cmd
  conda install libuv
  ```

## Linux

> [!NOTE]
> For installation with PyTorch 2.6, please refer to this [guide](../Quickstart/install_pytorch26_gpu.md) for more information.

### Prerequisites

IPEX-LLM GPU support on Linux has been verified on:

* Intel Arc™ A-Series Graphics
* Intel Data Center GPU Flex Series
* Intel Data Center GPU Max Series
* Intel iGPU

> [!IMPORTANT]
> For prerequisite installation on Intel Core™ Ultra Processors (Series 1) with processor number 1xxH/U/HL/UL (code name Meteor Lake), please refer to [this guide](../Quickstart/install_linux_gpu.md#install-prerequisites).

> [!Note]
> IPEX-LLM on Linux supports PyTorch 2.0 and PyTorch 2.1.
> 
> **Warning**
> 
> IPEX-LLM support for Pytorch 2.0 is deprecated as of ``ipex-llm >= 2.1.0b20240511``.

> [!IMPORTANT]
> We currently support the Ubuntu 20.04 operating system and later.

- For **PyTorch 2.1**:

   To enable IPEX-LLM for Intel GPUs with PyTorch 2.1, here are several prerequisite steps for tools installation and environment preparation:


   - Step 1: Install Intel GPU Driver version >= stable_775_20_20231219. We highly recommend installing the latest version of intel-i915-dkms using apt.

      > **Tip**:
      >
      > For client GPUs, such as the Intel® Arc™ A-series, please refer to [Client GPU Installation Guide](https://dgpu-docs.intel.com/driver/client/overview.html). For data center GPUs, including Intel® Data Center GPU Max Series and Intel® Data Center GPU Flex Series, please refer to our [Installation for Data Center GPU](https://dgpu-docs.intel.com/driver/installation.html) for general purpose GPU capabilities.
      >
      > See [release page](https://dgpu-docs.intel.com/releases/index.html) for latest version.

   - Step 2: Download and install [Intel® oneAPI Base Toolkit](https://www.intel.com/content/www/us/en/developer/tools/oneapi/base-toolkit-download.html) with version 2024.0. OneDNN, OneMKL and DPC++ compiler are needed, others are optional.

      Intel® oneAPI Base Toolkit 2024.0 installation methods:
      <details>
      <summary> For <b>APT installer</b> </summary>

      - Step 1: Set up repository

         ```bash
         wget -O- https://apt.repos.intel.com/intel-gpg-keys/GPG-PUB-KEY-INTEL-SW-PRODUCTS.PUB | gpg --dearmor | sudo tee /usr/share/keyrings/oneapi-archive-keyring.gpg > /dev/null
         echo "deb [signed-by=/usr/share/keyrings/oneapi-archive-keyring.gpg] https://apt.repos.intel.com/oneapi all main" | sudo tee /etc/apt/sources.list.d/oneAPI.list
         sudo apt update
         ```

      - Step 2: Install the package

         ```bash
         sudo apt install intel-oneapi-common-vars=2024.0.0-49406 \
            intel-oneapi-common-oneapi-vars=2024.0.0-49406 \
            intel-oneapi-diagnostics-utility=2024.0.0-49093 \
            intel-oneapi-compiler-dpcpp-cpp=2024.0.2-49895 \
            intel-oneapi-dpcpp-ct=2024.0.0-49381 \
            intel-oneapi-mkl=2024.0.0-49656 \
            intel-oneapi-mkl-devel=2024.0.0-49656 \
            intel-oneapi-mpi=2021.11.0-49493 \
            intel-oneapi-mpi-devel=2021.11.0-49493 \
            intel-oneapi-dal=2024.0.1-25 \
            intel-oneapi-dal-devel=2024.0.1-25 \
            intel-oneapi-ippcp=2021.9.1-5 \
            intel-oneapi-ippcp-devel=2021.9.1-5 \
            intel-oneapi-ipp=2021.10.1-13 \
            intel-oneapi-ipp-devel=2021.10.1-13 \
            intel-oneapi-tlt=2024.0.0-352 \
            intel-oneapi-ccl=2021.11.2-5 \
            intel-oneapi-ccl-devel=2021.11.2-5 \
            intel-oneapi-dnnl-devel=2024.0.0-49521 \
            intel-oneapi-dnnl=2024.0.0-49521 \
            intel-oneapi-tcm-1.0=1.0.0-435
         ```

         > **Note**:
         >
         > You can uninstall the package by running the following command:
         >
         > ```bash
         > sudo apt autoremove intel-oneapi-common-vars
         > ```
      </details>

      <details>
      <summary> For <b>PIP installer</b> </summary>

      - Step 1: Install oneAPI in a user-defined folder, e.g., ``~/intel/oneapi``.

         ```bash
         export PYTHONUSERBASE=~/intel/oneapi
         pip install dpcpp-cpp-rt==2024.0.2 mkl-dpcpp==2024.0.0 onednn==2024.0.0 --user
         ```

         > **Note**:
         >
         > The oneAPI packages are visible in ``pip list`` only if ``PYTHONUSERBASE`` is properly set.

      - Step 2: Configure your working conda environment (e.g. with name ``llm``) to append oneAPI path (e.g. ``~/intel/oneapi/lib``) to the environment variable ``LD_LIBRARY_PATH``.

         ```bash
         conda env config vars set LD_LIBRARY_PATH=$LD_LIBRARY_PATH:~/intel/oneapi/lib -n llm
         ```

         > **Note**:
         >
         > You can view the configured environment variables for your environment (e.g. with name ``llm``) by running ``conda env config vars list -n llm``.
         > You can continue with your working conda environment and install ``ipex-llm`` as guided in the next section.

         > **Note**:
         >
         > You are recommended not to install other pip packages in the user-defined folder for oneAPI (e.g. ``~/intel/oneapi``).
         > You can uninstall the oneAPI package by simply deleting the package folder, and unsetting the configuration of your working conda environment (e.g., with name ``llm``).
         >
         > ```bash
         > rm -r ~/intel/oneapi
         > conda env config vars unset LD_LIBRARY_PATH -n llm
         > ```
      </details>

      <details>
      <summary> For <b>Offline installer</b> </summary>
      
      Using the offline installer allows you to customize the installation path.

      ```bash      
      wget https://registrationcenter-download.intel.com/akdlm/IRC_NAS/20f4e6a1-6b0b-4752-b8c1-e5eacba10e01/l_BaseKit_p_2024.0.0.49564_offline.sh
      sudo sh ./l_BaseKit_p_2024.0.0.49564_offline.sh
      ```
      > **Note**:
      >
      > You can also modify the installation or uninstall the package by running the following commands:
      >
      > ```bash
      > cd /opt/intel/oneapi/installer
      > sudo ./installer
      > ```
      </details>

- For **PyTorch 2.0** (deprecated for versions ``ipex-llm >= 2.1.0b20240511``):

   To enable IPEX-LLM for Intel GPUs with PyTorch 2.0, here're several prerequisite steps for tools installation and environment preparation:


   - Step 1: Install Intel GPU Driver version >= stable_775_20_20231219. Highly recommend installing the latest version of intel-i915-dkms using apt.

      > **Tip**:
      >
      >   For client GPUs, such as the Intel® Arc™ A-series, please refer to [Client GPU Installation Guide](https://dgpu-docs.intel.com/driver/client/overview.html). For data center GPUs, including Intel® Data Center GPU Max Series and Intel® Data Center GPU Flex Series, please refer to our [Installation for Data Center GPU](https://dgpu-docs.intel.com/driver/installation.html) for general purpose GPU capabilities.
      >
      >   See [release page](https://dgpu-docs.intel.com/releases/index.html) for latest version.

   - Step 2: Download and install [Intel® oneAPI Base Toolkit](https://www.intel.com/content/www/us/en/developer/tools/oneapi/base-toolkit-download.html) with version 2023.2. OneDNN, OneMKL and DPC++ compiler are needed, others are optional.

      Intel® oneAPI Base Toolkit 2023.2 installation methods:

      <details>
      <summary> For <b>APT installer</b> </summary>

      - Step 1: Set up repository

         ```bash
         wget -O- https://apt.repos.intel.com/intel-gpg-keys/GPG-PUB-KEY-INTEL-SW-PRODUCTS.PUB | gpg --dearmor | sudo tee /usr/share/keyrings/oneapi-archive-keyring.gpg > /dev/null
         echo "deb [signed-by=/usr/share/keyrings/oneapi-archive-keyring.gpg] https://apt.repos.intel.com/oneapi all main" | sudo tee /etc/apt/sources.list.d/oneAPI.list
         sudo apt update
         ```

      - Step 2: Install the packages

         ```bash
         sudo apt install -y intel-oneapi-common-vars=2023.2.0-49462 \
            intel-oneapi-compiler-cpp-eclipse-cfg=2023.2.0-49495 intel-oneapi-compiler-dpcpp-eclipse-cfg=2023.2.0-49495 \
            intel-oneapi-diagnostics-utility=2022.4.0-49091 \
            intel-oneapi-compiler-dpcpp-cpp=2023.2.0-49495 \
            intel-oneapi-mkl=2023.2.0-49495 intel-oneapi-mkl-devel=2023.2.0-49495 \
            intel-oneapi-mpi=2021.10.0-49371 intel-oneapi-mpi-devel=2021.10.0-49371 \
            intel-oneapi-tbb=2021.10.0-49541 intel-oneapi-tbb-devel=2021.10.0-49541\
            intel-oneapi-ccl=2021.10.0-49084 intel-oneapi-ccl-devel=2021.10.0-49084\
            intel-oneapi-dnnl-devel=2023.2.0-49516 intel-oneapi-dnnl=2023.2.0-49516
         ```

         > **Note**:
         >
         > You can uninstall the package by running the following command:
         >
         > ```bash
         > sudo apt autoremove intel-oneapi-common-vars
         > ```
      </details>

      <details>
      <summary> For <b>PIP installer</b> </summary>

      - Step 1: Install oneAPI in a user-defined folder, e.g., ``~/intel/oneapi``

         ```bash
         export PYTHONUSERBASE=~/intel/oneapi
         pip install dpcpp-cpp-rt==2023.2.0 mkl-dpcpp==2023.2.0 onednn-cpu-dpcpp-gpu-dpcpp==2023.2.0 --user
         ```

         > **Note**:
         >
         > The oneAPI packages are visible in ``pip list`` only if ``PYTHONUSERBASE`` is properly set.

      - Step 2: Configure your working conda environment (e.g. with name ``llm``) to append oneAPI path (e.g. ``~/intel/oneapi/lib``) to the environment variable ``LD_LIBRARY_PATH``.

         ```bash
         conda env config vars set LD_LIBRARY_PATH=$LD_LIBRARY_PATH:~/intel/oneapi/lib -n llm
         ```

         > **Note**:
         >
         >   You can view the configured environment variables for your environment (e.g. with name ``llm``) by running ``conda env config vars list -n llm``.
         >   You can continue with your working conda environment and install ``ipex-llm`` as guided in the next section.

         > **Note**:
         >   
         >   You are recommended not to install other pip packages in the user-defined folder for oneAPI (e.g. ``~/intel/oneapi``).
         >   You can uninstall the oneAPI package by simply deleting the package folder, and unsetting the configuration of your working conda environment (e.g., with name ``llm``).  
         >
         > ```bash
         > rm -r ~/intel/oneapi
         > conda env config vars unset LD_LIBRARY_PATH -n llm
         > ```
      </details>

      <details>
      <summary> For <b>Offline installer</b> </summary>
      
      Using the offline installer allows you to customize the installation path.

      ```bash
      wget https://registrationcenter-download.intel.com/akdlm/IRC_NAS/992857b9-624c-45de-9701-f6445d845359/l_BaseKit_p_2023.2.0.49397_offline.sh
      sudo sh ./l_BaseKit_p_2023.2.0.49397_offline.sh
      ```
      > **Note**:
      >
      > You can also modify the installation or uninstall the package by running the following commands:
      >
      > ```bash
      > cd /opt/intel/oneapi/installer
      > sudo ./installer
      > ```
      </details>

### Install IPEX-LLM
#### Install IPEX-LLM From PyPI

We recommend using [Miniforge](https://conda-forge.org/download/) to create a python 3.11 enviroment:

> [!IMPORTANT]
> ``ipex-llm`` is tested with Python 3.9, 3.10 and 3.11. Python 3.11 is recommended for best practices.


> [!IMPORTANT]
>   Make sure you install matching versions of ipex-llm/pytorch/IPEX and oneAPI Base Toolkit. IPEX-LLM with Pytorch 2.1 should be used with oneAPI Base Toolkit version 2024.0. IPEX-LLM with Pytorch 2.0 should be used with oneAPI Base Toolkit version 2023.2.


- For **PyTorch 2.1**:

   Choose either US or CN website for ``extra-index-url``:
   
   - For **US**:

      ```bash
      conda create -n llm python=3.11
      conda activate llm

      pip install --pre --upgrade ipex-llm[xpu] --extra-index-url https://pytorch-extension.intel.com/release-whl/stable/xpu/us/
      ```

      > **Note**:
      >
      > The ``xpu`` option will install IPEX-LLM with PyTorch 2.1 by default, which is equivalent to
      >
      > ```bash
      > pip install --pre --upgrade ipex-llm[xpu_2.1] --extra-index-url https://pytorch-extension.intel.com/release-whl/stable/xpu/us/
      > ```

   - For **CN**:

      ```bash
      conda create -n llm python=3.11
      conda activate llm

      pip install --pre --upgrade ipex-llm[xpu] --extra-index-url https://pytorch-extension.intel.com/release-whl/stable/xpu/cn/
      ```

      > **Note**:
      >
      > The ``xpu`` option will install IPEX-LLM with PyTorch 2.1 by default, which is equivalent to
      >
      > ```bash
      > pip install --pre --upgrade ipex-llm[xpu_2.1] --extra-index-url https://pytorch-extension.intel.com/release-whl/stable/xpu/us/
      > ```

- For **PyTorch 2.0** (deprecated for versions ``ipex-llm >= 2.1.0b20240511``):

   Choose either US or CN website for ``extra-index-url``:
   
   - For **US**:

      ```bash
      conda create -n llm python=3.11
      conda activate llm

      pip install --pre --upgrade ipex-llm[xpu_2.0]==2.1.0b20240510 --extra-index-url https://pytorch-extension.intel.com/release-whl/stable/xpu/us/
      ```

   - For **CN**:

      ```bash
      conda create -n llm python=3.11
      conda activate llm

      pip install --pre --upgrade ipex-llm[xpu_2.0]==2.1.0b20240510 --extra-index-url https://pytorch-extension.intel.com/release-whl/stable/xpu/cn/
      ```


#### Install IPEX-LLM From Wheel

If you encounter network issues when installing IPEX, you can also install IPEX-LLM dependencies for Intel XPU from source archives. First you need to download and install torch/torchvision/ipex from wheels listed below before installing `ipex-llm`.


- For **PyTorch 2.1**:

   ```bash
   # get the wheels on Linux system for IPEX 2.1.10+xpu
   wget https://intel-extension-for-pytorch.s3.amazonaws.com/ipex_stable/xpu/torch-2.1.0a0%2Bcxx11.abi-cp311-cp311-linux_x86_64.whl
   wget https://intel-extension-for-pytorch.s3.amazonaws.com/ipex_stable/xpu/torchvision-0.16.0a0%2Bcxx11.abi-cp311-cp311-linux_x86_64.whl
   wget https://intel-extension-for-pytorch.s3.amazonaws.com/ipex_stable/xpu/intel_extension_for_pytorch-2.1.10%2Bxpu-cp311-cp311-linux_x86_64.whl
   ```

   Then you may install directly from the wheel archives using following commands:

   ```bash
   # install the packages from the wheels
   pip install torch-2.1.0a0+cxx11.abi-cp311-cp311-linux_x86_64.whl
   pip install torchvision-0.16.0a0+cxx11.abi-cp311-cp311-linux_x86_64.whl
   pip install intel_extension_for_pytorch-2.1.10+xpu-cp311-cp311-linux_x86_64.whl

   # install ipex-llm for Intel GPU
   pip install --pre --upgrade ipex-llm[xpu]
   ```

- For **PyTorch 2.0** (deprecated for versions ``ipex-llm >= 2.1.0b20240511``):

   ```bash
   # get the wheels on Linux system for IPEX 2.0.110+xpu
   wget https://intel-extension-for-pytorch.s3.amazonaws.com/ipex_stable/xpu/torch-2.0.1a0%2Bcxx11.abi-cp311-cp311-linux_x86_64.whl
   wget https://intel-extension-for-pytorch.s3.amazonaws.com/ipex_stable/xpu/torchvision-0.15.2a0%2Bcxx11.abi-cp311-cp311-linux_x86_64.whl
   wget https://intel-extension-for-pytorch.s3.amazonaws.com/ipex_stable/xpu/intel_extension_for_pytorch-2.0.110%2Bxpu-cp311-cp311-linux_x86_64.whl
   ```

   Then you may install directly from the wheel archives using following commands:

   ```bash
   # install the packages from the wheels
   pip install torch-2.0.1a0+cxx11.abi-cp311-cp311-linux_x86_64.whl
   pip install torchvision-0.15.2a0+cxx11.abi-cp311-cp311-linux_x86_64.whl
   pip install intel_extension_for_pytorch-2.0.110+xpu-cp311-cp311-linux_x86_64.whl

   # install ipex-llm for Intel GPU
   pip install --pre --upgrade ipex-llm[xpu_2.0]==2.1.0b20240510
   ```

> [!NOTE]
> All the wheel packages mentioned here are for Python 3.11. If you would like to use Python 3.9 or 3.10, you should modify the wheel names for ``torch``, ``torchvision``, and ``intel_extension_for_pytorch`` by replacing ``cp11`` with ``cp39`` or ``cp310``, respectively.


### Runtime Configuration

To use GPU acceleration on Linux, several environment variables are required or recommended before running a GPU example.


   - For **Intel Arc™ A-Series and Intel Data Center GPU Flex**:

      For Intel Arc™ A-Series Graphics and Intel Data Center GPU Flex Series, we recommend:

      ```bash
      # Configure oneAPI environment variables. Required step for APT or offline installed oneAPI.
      # Skip this step for PIP-installed oneAPI since the environment has already been configured in LD_LIBRARY_PATH.
      source /opt/intel/oneapi/setvars.sh

      # Recommended Environment Variables for optimal performance
      export USE_XETLA=OFF
      export SYCL_PI_LEVEL_ZERO_USE_IMMEDIATE_COMMANDLISTS=1
      export SYCL_CACHE_PERSISTENT=1
      ```

   - For **Intel Data Center GPU Max**:

      For Intel Data Center GPU Max Series, we recommend:

      ```bash
      # Configure oneAPI environment variables. Required step for APT or offline installed oneAPI.
      # Skip this step for PIP-installed oneAPI since the environment has already been configured in LD_LIBRARY_PATH.
      source /opt/intel/oneapi/setvars.sh

      # Recommended Environment Variables for optimal performance
      export LD_PRELOAD=${LD_PRELOAD}:${CONDA_PREFIX}/lib/libtcmalloc.so
      export SYCL_PI_LEVEL_ZERO_USE_IMMEDIATE_COMMANDLISTS=1
      export SYCL_CACHE_PERSISTENT=1
      export ENABLE_SDP_FUSION=1
      ```

      Please note that ``libtcmalloc.so`` can be installed by ``conda install -c conda-forge -y gperftools=2.10``

   - For **Intel iGPU**:

      ```bash
      # Configure oneAPI environment variables. Required step for APT or offline installed oneAPI.
      # Skip this step for PIP-installed oneAPI since the environment has already been configured in LD_LIBRARY_PATH.
      source /opt/intel/oneapi/setvars.sh

      export SYCL_CACHE_PERSISTENT=1
      ```

> [!NOTE]
> For **the first time** that **each model** runs on Intel iGPU/Intel Arc™ A300-Series or Pro A60, it may take several minutes to compile.

### Known issues

#### 1. Potential suboptimal performance with Linux kernel 6.2.0

For Ubuntu 22.04 and driver version < stable_775_20_20231219, the performance on Linux kernel 6.2.0 is worse than Linux kernel 5.19.0. You can use `sudo apt update && sudo apt install -y intel-i915-dkms intel-fw-gpu` to install the latest driver to solve this issue (need to reboot OS).

Tips: You can use `sudo apt list --installed | grep intel-i915-dkms` to check your intel-i915-dkms's version, the version should be latest and >= `1.23.9.11.231003.15+i19-1`.

#### 2. Driver installation unmet dependencies error: intel-i915-dkms

The last apt install command of the driver installation may produce the following error:

```
The following packages have unmet dependencies:
 intel-i915-dkms : Conflicts: intel-platform-cse-dkms
                   Conflicts: intel-platform-vsec-dkms
```

You can use `sudo apt install -y intel-i915-dkms intel-fw-gpu` to install instead. As the intel-platform-cse-dkms and intel-platform-vsec-dkms are already provided by intel-i915-dkms.

### Troubleshooting

#### 1. Cannot open shared object file: No such file or directory

Error where libmkl file is not found, for example,

```
OSError: libmkl_intel_lp64.so.2: cannot open shared object file: No such file or directory
```
```
Error: libmkl_sycl_blas.so.4: cannot open shared object file: No such file or directory
```

The reason for such errors is that oneAPI has not been initialized properly before running IPEX-LLM code.

* For oneAPI installed using APT or Offline Installer, make sure you execute `setvars.sh` of oneAPI Base Toolkit before running IPEX-LLM.
* For PIP-installed oneAPI, activate your working environment and run ``echo $LD_LIBRARY_PATH`` to check if the installation path is properly configured for the environment. If the output does not contain oneAPI path (e.g. ``~/intel/oneapi/lib``), check [Prerequisites](#prerequisites-1) to re-install oneAPI with PIP installer.
* Make sure you install matching versions of ipex-llm/pytorch/IPEX and oneAPI Base Toolkit. IPEX-LLM with PyTorch 2.1 should be used with oneAPI Base Toolkit version 2024.0. IPEX-LLM with PyTorch 2.0 should be used with oneAPI Base Toolkit version 2023.2.

#### 2. `core dump` when running with GPU
If encountered random  `core dump` when running with GPU, please remove out of tree driver.
```
sudo apt purge -y  intel-i915-dkms  intel-fw-gpu
```