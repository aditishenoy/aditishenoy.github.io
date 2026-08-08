---
title: "Installing Nvidia Driver and Cuda in a Singularity Container"
date: 2020-05-21T16:08:13+02:00
draft: false
tags: ["Tech Tricks"]
---

Installing Nvidia Drivers and Cuda on a Linux machine can be a tricky affair. Though it is possible to install both the nvidia-driver and the nvidia-cuda-toolkit using a package manager, it could result in incompatibile versions and could potentially break the graphics or operating system. In this post I have described how I have kept the driver and toolkit separate using singularity containers to check for conflicting versions and prevent potential mishaps.

Brief notes on terminology:

  * [_CUDA_](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#introduction) \- parallel computing platform and programming model to communicate with NVIDIA GPUs
  * [_Nvidia Driver_](https://www.geforce.com/drivers) \- software that allows the NVIDIA GPU to communicate with the operating system
  * [_Singularity Container_](https://sylabs.io/guides/3.0/user-guide/build_a_container.html) \- a file/image running an operating system on top of the host system’s operating system. The software environment of the container is determined by the contents of the singularity image and what is run within the container will not affect the host operating system.



These were my system specifications:
    
    
    ashenoy@hostname~$ uname -m && cat /etc/*release
    x86_64
    DISTRIB_ID=Ubuntu
    DISTRIB_RELEASE=20.04
    DISTRIB_CODENAME=focal
    DISTRIB_DESCRIPTION="Ubuntu 20.04 LTS"
    NAME="Ubuntu"
    VERSION="20.04 LTS (Focal Fossa)"
    

I first started with searching for and installing Nvidia-driver outside container since root priviledges are needed for this.
    
    
    #Search for versions of drivers applicable for current operating system
    ashenoy@hostname:~$ apt search nvidia-driver
    Sorting... Done
    Full Text Search... Done
    nvidia-driver-390/focal,focal 390.132-0ubuntu2 amd64
      NVIDIA driver metapackage
    
    nvidia-driver-435/focal,focal 435.21-0ubuntu7 amd64
      NVIDIA driver metapackage
    
    nvidia-driver-440/focal 440.82+really.440.64-0ubuntu6 amd64
      NVIDIA driver metapackage
      
    # Or try the following command
    ashenoy@hostname:~$ ubuntu-drivers list
    nvidia-driver-390, (kernel modules provided by linux-modules-nvidia-390-generic-hwe-20.04)
    nvidia-driver-435, (kernel modules provided by linux-modules-nvidia-435-generic-hwe-20.04)
    nvidia-driver-440, (kernel modules provided by linux-modules-nvidia-440-generic-hwe-20.04)
    

Since I already had a driver installed and I needed to change the version, I did the following:
    
    
    #Remove old version of driver
    ashenoy@hostname:~$ sudo apt remove <NVIDIA-DRIVER-VERSION-TO-REMOVE>
    #Install nvidia-modprobe and graphics-drivers/ppa
    ashenoy@hostname:~$ sudo apt install nvidia-modprobe
    ashenoy@hostname:~$ sudo add-apt-repository ppa:graphics-drivers/ppa
    # Update and install the driver version you need
    ashenoy@hostname:~$ sudo apt-get update && sudo apt-get upgrade
    ashenoy@hostname:~$ sudo apt-get install <NVIDIA-DRIVER-VERSION-NEEDED>
    #To check if driver version has been updated
    ashenoy@hostname:~$ nvidia-smi
    Failed to initialize NVML: Driver/library version mismatch
    ashenoy@hostname:~$ sudo reboot
    

After the reboot, the error “Failed to initialize NVML: Driver/library version mismatch” did not persist and the output of nvidia-smi was as below.
    
    
    ashenoy@hostname:~$ nvidia-smi
    Thu May 21 15:43:45 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 435.21       Driver Version: 435.21       CUDA Version: 10.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  GeForce GT 1030     Off  | 00000000:04:00.0 Off |                  N/A |
    | 35%   32C    P8    N/A /  30W |      1MiB /  2001MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    |   1  GeForce GTX 1070    Off  | 00000000:09:00.0  On |                  N/A |
    | 28%   32C    P8     7W / 151W |     68MiB /  8097MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    

Following this I created singularity container as described [here](https://sylabs.io/guides/3.5/user-guide/gpu.html)
    
    
    ashenoy@hostname~$ singularity pull docker://tensorflow/tensorflow:latest-gpu
    ashenoy@hostname~$ singularity run --nv tensorflow_latest-gpu.sif
    Singularity> cat /etc/issue
    Ubuntu 18.04.3 LTS \n \l
    #(Note how the operating system inside this container was 18.04 while the host operating system shown at the beginning of this article was 20.4)
    

and followed the instructions for installing nvidia-cuda-toolkit from [here](https://developer.nvidia.com/cuda-downloads). I selected Linux -> x86_64 -> Ubuntu -> deb(local)
    
    
    Singularity> wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
    Singularity> mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
    Singularity> wget http://developer.download.nvidia.com/compute/cuda/10.2/Prod/local_installers/cuda-repo-ubuntu1804-10-2-local-10.2.89-440.33.01_1.0-1_amd64.deb
    Singularity> dpkg -i cuda-repo-ubuntu1804-10-2-local-10.2.89-440.33.01_1.0-1_amd64.deb
    Singularity> apt-key add /var/cuda-repo-10-2-local-10.2.89-440.33.01/7fa2af80.pub
    Singularity> apt-get update
    Singularity>apt-get -y install cuda
    
    #Check the nvidia-cuda-toolkit version 
    Singularity> nvcc -V
    nvcc: NVIDIA (R) Cuda compiler driver
    Copyright (c) 2005-2019 NVIDIA Corporation
    Built on Sun_Jul_28_19:07:16_PDT_2019
    Cuda compilation tools, release 10.1, V10.1.243
    Singularity> exit
    ashenoy@hostname~$ sudo reboot
    

At this point, I was getting an output for nvidia-smi and nvcc -V (with compatible versions) inside the singularity container. Using the following commands, tensorflow was able to find and identify the number of GPUs available in the system.
    
    
    Singularity> python
    Python 3.6.9 (default, Apr 18 2020, 01:56:04)
    [GCC 8.4.0] on linux
    Type "help", "copyright", "credits" or "license" for more information.
    >>> import tensorflow as tf
    >>> print("Num GPUs Available: ",len(tf.config.experimental.list_physical_devices('GPU')))
    2020-05-21 15:44:35.622505: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcuda.so.1
    2020-05-21 15:44:35.658062: I tensorflow/stream_executor/cuda/cuda_gpu_executor.cc:981] successful NUMA node read from SysFS had negative value (-1), but there must be at least one NUMA node, so returning NUMA node zero
    2020-05-21 15:44:35.658542: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1561] Found device 0 with properties:
    pciBusID: 0000:09:00.0 name: GeForce GTX 1070 computeCapability: 6.1
    coreClock: 1.7715GHz coreCount: 15 deviceMemorySize: 7.91GiB deviceMemoryBandwidth: 238.66GiB/s
    2020-05-21 15:44:35.658580: I tensorflow/stream_executor/cuda/cuda_gpu_executor.cc:981] successful NUMA node read from SysFS had negative value (-1), but there must be at least one NUMA node, so returning NUMA node zero
    2020-05-21 15:44:35.659125: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1561] Found device 1 with properties:
    pciBusID: 0000:04:00.0 name: GeForce GT 1030 computeCapability: 6.1
    coreClock: 1.5185GHz coreCount: 3 deviceMemorySize: 1.95GiB deviceMemoryBandwidth: 44.76GiB/s
    2020-05-21 15:44:35.701686: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcudart.so.10.1
    2020-05-21 15:44:35.816640: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcublas.so.10
    2020-05-21 15:44:35.872584: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcufft.so.10
    2020-05-21 15:44:35.947596: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcurand.so.10
    2020-05-21 15:44:36.150146: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcusolver.so.10
    2020-05-21 15:44:36.200153: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcusparse.so.10
    2020-05-21 15:44:36.447265: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcudnn.so.7
    2020-05-21 15:44:36.447451: I tensorflow/stream_executor/cuda/cuda_gpu_executor.cc:981] successful NUMA node read from SysFS had negative value (-1), but there must be at least one NUMA node, so returning NUMA node zero
    2020-05-21 15:44:36.448344: I tensorflow/stream_executor/cuda/cuda_gpu_executor.cc:981] successful NUMA node read from SysFS had negative value (-1), but there must be at least one NUMA node, so returning NUMA node zero
    2020-05-21 15:44:36.448992: I tensorflow/stream_executor/cuda/cuda_gpu_executor.cc:981] successful NUMA node read from SysFS had negative value (-1), but there must be at least one NUMA node, so returning NUMA node zero
    2020-05-21 15:44:36.449803: I tensorflow/stream_executor/cuda/cuda_gpu_executor.cc:981] successful NUMA node read from SysFS had negative value (-1), but there must be at least one NUMA node, so returning NUMA node zero
    2020-05-21 15:44:36.450426: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1688] Ignoring visible gpu device (device: 1, name: GeForce GT 1030, pci bus id: 0000:04:00.0, compute capability: 6.1) with core count: 3. The minimum required count is 8. You can adjust this requirement with the env var TF_MIN_GPU_MULTIPROCESSOR_COUNT.
    2020-05-21 15:44:36.450437: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1703] Adding visible gpu devices: 0
    Num GPUs Available:  1
    >>> exit()
    
    ## But pytorch wasn't finding the gpu
    Singularity> python
    Python 3.6.9 (default, Apr 18 2020, 01:56:04)
    [GCC 8.4.0] on linux
    Type "help", "copyright", "credits" or "license" for more information.
    >>> import torch
    >>> device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
    >>> print (device)
    cpu
    >>> print (torch.cuda.is_available())
    False
    >>> print(torch.version.cuda)
    10.2
    

So I removed pytorch 1.5.0 and installed an older version 1.0.0 instead.
    
    
    Singularity> pip3 install torch==1.0.0 torchvision==0.2.1
    
    Singularity> python
    Python 3.6.9 (default, Apr 18 2020, 01:56:04)
    [GCC 8.4.0] on linux
    Type "help", "copyright", "credits" or "license" for more information.
    >>> import torch
    >>> print (torch.cuda.is_available())
    True
    

And everything was right with the world again…

Among the countless tabs I referred to, these were especially useful to me.

  * [Cuda Toolkit Documentation](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#introduction)

  * [Blog post by Mark Buckler](http://www.markbuckler.com/post/install-cuda/)

  * [Singularity documentation](https://sylabs.io/guides/3.5/user-guide/gpu.html)

  * [Singularity Google Group](https://groups.google.com/a/lbl.gov/forum/#!msg/singularity/CezfXNjLGe0/OayDEcSUBQAJ)

  * and my best friend who gave me both good and bad solutions to my errors- Stack Overflow
