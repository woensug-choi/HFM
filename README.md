# Studying Machine Learning with TensorFlow

Repository to study machine learning using Tensorflow. Inspired by HFM
Related repositories,
- PINN : https://maziarraissi.github.io/PINNs/
- HFM : https://maziarraissi.github.io/HFM/
- DeepVIV : https://maziarraissi.github.io/DeepVIV/

# Contents
<!-- TOC generated with https://github.com/ekalinin/github-markdown-toc -->
<!--
 cat fls_model_standalone.md | ./gh-md-toc -
-->

* [System requirements](#System-requirements)
* [Installation](#Installation)
  * [For CPU Version](#For-CPU-Version)
  * [For GPU Version](#For-GPU-Version)
* [Build and Run](#Build-and-Run)

***

## System requirements
- Ubuntu 20.04
- (For GPU version) NVIDIA GPU capable of CUDA 11.2 and its drivers
- Docker

## Installation

### For CPU version

- Docker
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
sudo usermod -aG docker $USER
```
And start new terminal window

- Final test
```bash
# Test docker
docker run hello-world

# Test Tensorflow
docker run -it tensorflow/tensorflow bash
```

### For GPU version
- Docker
Install docker as CPU version installation

- NVIDIA drivers
Choose `[proprietrary, tested]` driver on Additional Derivers at Software & Updates.
For example, `nvidia-driver-460`

To test the correct installation,
```
nvidia-smi
```

- Install NVIDIA docker container runtime
```
curl -s -L https://nvidia.github.io/nvidia-container-runtime/gpgkey | sudo apt-key add -
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-container-runtime/$distribution/nvidia-container-runtime.list |\
    sudo tee /etc/apt/sources.list.d/nvidia-container-runtime.list
sudo apt-get update
sudo apt-get install nvidia-container-runtime
```
And restart docker
```
sudo systemctl stop docker
sudo systemctl start docker
```

- Final test
  - Test docker with nvidia gpu
    ```
    docker run --gpus all nvidia/cuda:11.2.0-cudnn8-runtime-ubuntu20.04 nvidia-smi
    ```
    If you see something like this, it's a success
    ```
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.91.03    Driver Version: 460.91.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Quadro RTX 3000...  Off  | 00000000:01:00.0 Off |                  N/A |
    | N/A   77C    P0    65W /  N/A |   5054MiB /  5934MiB |     99%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+

    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    +-----------------------------------------------------------------------------+
    ```

  - Test Tensorflow with gpu
    ```bash
    docker run --gpus all -it tensorflow/tensorflow:latest-gpu bash
    ```

## Build and Run

- Clone this repository
```
git clone git@github.com:woensug-choi/ML_FSI.git
```
Will clone the https://github.com/woensug-choi/ML_FSI into a directory `ML_FSI`

- Get input data
```bash
# At ML_FSI/2D_Cylinder directory
pip3 install gdown
gdown 'https://drive.google.com/uc?id=1ZeGE3QNp0QDkKlVu7NF2O-jcvaGSq3Zu'
```

- Build Docker image (Upto 20 min)
at the directory where the Dockerfile is,
```bash
# If CPU
docker build -t ml_fsi_cpu -f ./TensorFlow_CPU.Dockerfile .
# If GPU
docker build -t ml_fsi_gpu -f ./TensorFlow_GPU.Dockerfile .
```

- Run Docker image with a python script
at the directory where the python script is,
```bash
# At ML_FSI/2D_Cylinder directory

# If CPU
docker run -it --rm -v $PWD:/tmp -w /tmp ml_fsi_cpu python ./Cylinder2D.py
# If GPU
docker run -it --rm --gpus all -v $PWD:/tmp -w /tmp ml_fsi_gpu python ./Cylinder2D.py
```
