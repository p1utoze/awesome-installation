# Installing the NVIDIA Container Toolkit
[Installation](#installation)

- [Installing with Apt](#installing-with-apt)
- [Installing with Yum or Dnf](#installing-with-yum-or-dnf)
- [Installing with Zypper](#installing-with-zypper)

[Configuration](#configuration)

[Prerequisites](#prerequisites)
[Configuring Docker](#configuring-docker)

[Next Steps](#next-steps)
- [Running a Sample Workload with Docker](#running-a-sample-workload-with-docker)

## Installation
### **Installing with Apt**
1. **Configure the repository:**

curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
  && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list \
  && \
    sudo apt-get update
2. **Install the NVIDIA Container Toolkit packages:**

sudo apt-get install -y nvidia-container-toolkit

### **Installing with Yum or Dnf**
1. **Configure the repository:**

curl -s -L https://nvidia.github.io/libnvidia-container/stable/rpm/nvidia-container-toolkit.repo | \
  sudo tee /etc/yum.repos.d/nvidia-container-toolkit.repo
2. **Install the NVIDIA Container Toolkit packages:**

sudo yum install -y nvidia-container-toolkit

### **Installing with Zypper**
*1. *Configure the repository:**

sudo zypper ar https://nvidia.github.io/libnvidia-container/stable/rpm/nvidia-container-toolkit.repo
2. **Install the NVIDIA Container Toolkit packages:**

sudo zypper --gpg-auto-import-keys install -y nvidia-container-toolkit

## **Configuration**
### Prerequisites
- You installed a supported container engine (Docker, Containerd, CRI-O, Podman).

- You installed the NVIDIA Container Toolkit.

**Configuring Docker**
Configure the container runtime by using the nvidia-ctk command:

sudo nvidia-ctk runtime configure --runtime=docker
The nvidia-ctk command modifies the /etc/docker/daemon.json file on the host. The file is updated so that Docker can use the NVIDIA Container Runtime.

Restart the Docker daemon:

sudo systemctl restart docker

## **Next Steps**
Install an NVIDIA GPU Driver if you do not already have one installed. You can install a driver by using the package manager for your distribution, but other installation methods, such as downloading a .run file installer, are available. You can check out the [Nvidia for Ubuntu](nvidia-ubuntu.md) in this repository. Refer to the NVIDIA Driver Installation Quickstart Guide for more information.

## **Running a Sample Workload**
### Running a Sample Workload with Docker
After you install and configure the toolkit and install an NVIDIA GPU Driver, you can verify your installation by running a sample workload.

Run a sample CUDA container:

sudo docker run --rm --runtime=nvidia --gpus all ubuntu nvidia-smi
Your output should resemble the following output:

```
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 535.86.10    Driver Version: 535.86.10    CUDA Version: 12.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  Tesla T4            On   | 00000000:00:1E.0 Off |                    0 |
| N/A   34C    P8     9W /  70W |      0MiB / 15109MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
```

