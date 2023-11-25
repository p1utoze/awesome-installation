# Installing the NVIDIA Container Toolkit
[Installation](#installation)

- [Installing with Apt](#installing-with-apt)
- [Installing with Yum or Dnf](#installing-with-yum-or-dnf)
- [Installing with Zypper](#installing-with-zypper)

[Configuration](#configuration)

[Prerequisites](#prerequisites)
[Configuring Docker](#configuring-docker)

[Next Steps](#next-steps)

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

## Next Steps
Install an NVIDIA GPU Driver if you do not already have one installed. You can install a driver by using the package manager for your distribution, but other installation methods, such as downloading a .run file intaller, are available. Refer to the NVIDIA Driver Installation Quickstart Guide for more information.

## Running a Sample Workload


