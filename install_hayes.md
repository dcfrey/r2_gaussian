# Installation E17

Tested on HAYES.

## Install Conda

```
cd ~
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
source ~/miniconda3/bin/activate
conda init --all
```

## Install CUDA 11.6

```
cd ~
wget https://developer.download.nvidia.com/compute/cuda/11.6.0/local_installers/cuda_11.6.0_510.39.01_linux.run
bash cuda_11.6.0_510.39.01_linux.run --installpath=~/cuda-11.6
```

Tick off the box for driver installation (requires sudo rights).
CUDA 11.6 is compatible with driver versions up from 510.0.

## Install g++ 9.3

Compatibility of CUDA 11.6 with g++ only up to 9.x:

```
conda install -c conda-forge gxx_linux-64=9.3.0
```

Create symbolic link:

```
ln -s <miniconda-directory>/envs/r2_gaussian/bin/x86_64-conda-linux-gnu-g++ x86_64-conda-linux-gnu-g++
```

## Update Shell Script

Edit `~/.bashrc`:

```
nano ~/.bashrc
```

Add the following lines at the end:
```
export CUDA_HOME=$HOME/cuda-11.6
export PATH=$CUDA_HOME/bin:$PATH
export LD_LIBRARY_PATH=$CUDA_HOME/lib64:$LD_LIBRARY_PATH
```

Save with Ctrl+S, close with Ctrl+X. Source into current shell:

```
source ~/.bashrc
```

## Check versions

```
nvidia-smi          # driver should be >= 510.0, cuda should be >= 11.6
nvcc --version      # should be 11.6
g++ --version       # should be 9.3 (?)
```

## Setup

```
conda env create -f environment.yml
wget https://github.com/CERN/TIGRE/archive/refs/tags/v2.3.zip
unzip v2.3.zip
pip install TIGRE-2.3/Python --no-build-isolation
```

## Download Example Data

Projections:

```
gdown https://drive.google.com/uc?id=1cgYzvxkrlN0u5F9Auiy6oBEqyqf3Wf69
```

75 angle:

```
gdown https://drive.google.com/uc?id=12_IA9jMzK7HjuhQSoNX2Uf5HZIfqOtpA
```

# Troubleshooting

## Missing libcuda.so

`libcuda.so` is a library providing access to the CUDA driver API. CUDA-accelerated deep learning frameworks such as `CuDNN` depend on it.

During training:

```
Could not load library libcudnn_cnn_infer.so.8. Error: libcuda.so: cannot open shared object file: No such file or directory
Please make sure libcudnn_cnn_infer.so.8 is in your library path!
```

Search available installs (that are not contained within `stubs`):
```
find /usr/ -name "libcuda.so"
```

Create symlink from installed 32-bit version to local installation:
```
ln -s /usr/lib/i386-linux-gnu/libcuda.so ~/cuda-11.6/lib64/
```

Did not work:
```
Could not load library libcudnn_cnn_infer.so.8. Error: libcuda.so: wrong ELF class: ELFCLASS32
Please make sure libcudnn_cnn_infer.so.8 is in your library path!
```

Thus, 64-bit version has to be installed. To download version 8, NVIDIA account is necessary. Version 9 should also be compatible with CUDA 11.
```
https://developer.nvidia.com/cudnn-downloads?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=22.04&target_type=deb_local
```


