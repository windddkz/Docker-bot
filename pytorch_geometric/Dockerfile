FROM nvidia/cuda:11.8.0-cudnn8-devel-ubuntu22.04

RUN rm -rf /etc/apt/sources.list.d/cuda.list && \
    rm -rf /etc/apt/sources.list.d/nvidia-ml.list && \
    apt-get update -y &&  apt-get install -y \
    curl \
    ca-certificates \
    vim \
    sudo \
    git \
    bzip2 \
    libx11-6 && \
    rm -rf /var/lib/apt/lists/*

RUN echo "/usr/local/nvidia/lib" >> /etc/ld.so.conf.d/nvidia.conf && \
    echo "/usr/local/nvidia/lib64" >> /etc/ld.so.conf.d/nvidia.conf

ENV LIBRARY_PATH /usr/local/cuda/lib64/stubs
ENV PATH /usr/local/nvidia/bin:/usr/local/cuda/bin:${PATH}
ENV LD_LIBRARY_PATH /usr/local/nvidia/lib:/usr/local/nvidia/lib64

# NVIDIA container runtime.
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES compute,utility

# PyTorch (Geometric) installation

# Create a working directory.
RUN mkdir /app
WORKDIR /app

# Create a non-root user and switch to it.
RUN adduser --disabled-password --gecos '' --shell /bin/bash user && \
    chown -R user:user /app
RUN echo "user ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/90-user
USER user

# All users can use /home/user as their home directory.
ENV HOME=/home/user
RUN chmod 777 /home/user

# Install Miniconda.
RUN curl -so ~/miniconda.sh https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh && \
    chmod +x ~/miniconda.sh && \
    ~/miniconda.sh -b -p ~/miniconda && \
    rm ~/miniconda.sh

ENV PATH=/home/user/miniconda/bin:$PATH
ENV CONDA_AUTO_UPDATE_CONDA=false

# Create a Python 3.10 environment.
RUN /home/user/miniconda/bin/conda install conda-build && \
    /home/user/miniconda/bin/conda create -y --name py310 python=3.10 && \
    /home/user/miniconda/bin/conda clean -ya

ENV CONDA_DEFAULT_ENV=py310
ENV CONDA_PREFIX=/home/user/miniconda/envs/$CONDA_DEFAULT_ENV
ENV PATH=$CONDA_PREFIX/bin:$PATH

ENV CPATH=/usr/local/cuda/include:$CPATH
ENV LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
ENV DYLD_LIBRARY_PATH=/usr/local/cuda/lib:$DYLD_LIBRARY_PATH


# Install PyTorch
# conda install pyg -c pyg
RUN conda install pytorch torchvision torchaudio pytorch-cuda=11.7 -c pytorch -c nvidia && \
    conda clean -ya

# Install PyG.
RUN pip install pyg-lib torch-scatter torch-sparse -f https://data.pyg.org/whl/torch-1.13.0+cu117.html --no-cache-dir && \
    pip install torch-cluster torch-spline-conv -f https://data.pyg.org/whl/torch-1.13.0+cu117.html --no-cache-dir && \
    pip install torch-geometric --no-cache-dir
