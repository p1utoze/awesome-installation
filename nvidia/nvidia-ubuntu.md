# NVIDIA CUDA TOOLKIT + DRIVER INSTALLATION

The NVIDIA® CUDA® Toolkit provides a development environment for creating high performance GPU-accelerated applications and especially to leverage GPU computing for training deep-learning models. 
The major challenge is local / on-prem installation which consumes time and becomes complex. 
The rest of the instructions are for the users who use **Ubuntu 20.04, 22.04, 22.10** with Nvidia Graphic Cards. 

#### Pre-requisites

***-> 2.1. Verify You Have a CUDA-Capable GPU***
To verify that your GPU is CUDA-capable, go to your distribution’s equivalent of System Properties, or, from the command line, enter:

```
lspci | grep -i nvidia
```
If you do not see any settings, update the PCI hardware database that Linux maintains by entering update-pciids (generally found in /sbin) at the command line and rerun the previous lspci command.

If your graphics card is from NVIDIA and it is listed in https://developer.nvidia.com/cuda-gpus, your GPU is CUDA-capable.

The Release Notes for the CUDA Toolkit also contain a list of supported products.

2.2. Verify You Have a Supported Version of Linux
The CUDA Development Tools are only supported on some specific distributions of Linux. These are listed in the CUDA Toolkit release notes.

To determine which distribution and release number you’re running, type the following at the command line:

```
uname -m && cat /etc/*release
```
You should see output similar to the following, modified for your particular system:

```
x86_64
Red Hat Enterprise Linux Workstation release 6.0 (Santiago)
```
The x86_64 line indicates you are running on a 64-bit system. The remainder gives information about your distribution.

-> ***2.3. Verify the System Has gcc Installed***
The gcc compiler is required for development using the CUDA Toolkit. It is not required for running CUDA applications. It is generally installed as part of the Linux installation, and in most cases the version of gcc installed with a supported version of Linux will work correctly.

To verify the version of gcc installed on your system, type the following on the command line:

```
gcc --version
```
If an error message displays, you need to install the development tools from your Linux distribution or obtain a version of gcc and its accompanying toolchain from the Web.


***3.10.1. Prepare Ubuntu***
Perform the pre-installation actions.

The kernel headers and development packages for the currently running kernel can be installed with:

```
sudo apt-get install linux-headers-$(uname -r)
```
Remove Outdated Signing Key:

```
sudo apt-key del 7fa2af80
```
Choose an installation method: local repo or network repo.

***3.10.2. Local Repo Installation for Ubuntu***
Install local repository on file system:

```
sudo dpkg -i cuda-repo-<distro>_<version>_<architecture>.deb
```
Enroll ephemeral public GPG key:

```
sudo cp /var/cuda-repo-<distro>-X-Y-local/cuda-*-keyring.gpg /usr/share/keyrings/
```
Add pin file to prioritize CUDA repository:

```
wget https://developer.download.nvidia.com/compute/cuda/repos/<distro>/x86_64/cuda-<distro>.pin
 sudo mv cuda-<distro>.pin /etc/apt/preferences.d/cuda-repository-pin-600
```

***3.10.3. Network Repo Installation for Ubuntu***
The new GPG public key for the CUDA repository is 3bf863cc. This must be enrolled on the system, either using the cuda-keyring package or manually; the apt-key command is deprecated and not recommended.

Install the new cuda-keyring package:

```
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
```

```
sudo apt-get update
```

```
sudo apt-get install cuda-drivers-530
```

```
sudo apt-get install cuda-toolkit-12-1
sudo reboot
```

![[Pasted image 20231104150038.png]]

### 13.1.1. Environment Setup[]( https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#environment-setup "Permalink to this headline")

The `PATH` variable needs to include `export PATH=/usr/local/cuda-12./bin${PATH:+:${PATH}}`. Nsight Compute has moved to `/opt/nvidia/nsight-compute/` only in rpm/deb installation method. When using `.run` installer it is still located under `/usr/local/cuda-12.2/`.

To add this path to the `PATH` variable:

```
export PATH=/usr/local/cuda-12.2/bin${PATH:+:${PATH}}
```

In addition, when using the runfile installation method, the `LD_LIBRARY_PATH` variable needs to contain `/usr/local/cuda-12.2/lib64` on a 64-bit system, or `/usr/local/cuda-12.2/lib` on a 32-bit system

- To change the environment variables for 64-bit operating systems:
    
```
    export LD_LIBRARY_PATH=/usr/local/cuda-12.2/lib64\
                             ${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```
    
- To change the environment variables for 32-bit operating systems:
    
```
    export LD_LIBRARY_PATH=/usr/local/cuda-12.2/lib\
                             ${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

### To-Do:
- Add instructions for setting up on other Linux distributions, over the Windows platform, Installing through pip or Anaconda
- Add Detailed Driver| Toolkit | Kernel Compatibility chart
- Add Deep Learning frameworks compatibility charts like Tensorflow, Pytorch, etc
