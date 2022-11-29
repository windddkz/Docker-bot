FROM nvidia/cuda:11.7.0-cudnn8-devel-ubuntu22.04

RUN rm -rf /etc/apt/sources.list.d/cuda.list && \
    rm -rf /etc/apt/sources.list.d/nvidia-ml.list && \
    apt-get update -y &&  apt-get install -y \
    curl \
    wget \
    ca-certificates \
    vim \
    sudo \
    git \
    bzip2 \
    libx11-6 && \
    rm -rf /var/lib/apt/lists/*

# Create a working directory.
RUN mkdir /app
WORKDIR /app

# Create a non-root user and switch to it.
RUN adduser --disabled-password --gecos '' --shell /bin/bash pyg && \
    chown -R pyg:pyg /app
RUN echo "pyg ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/90-pyg

USER pyg

ENV HOME=/home/pyg
RUN chmod 777 /home/pyg

# Install Miniconda.
RUN curl -so ~/miniconda.sh https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh && \
    chmod +x ~/miniconda.sh && \
    ~/miniconda.sh -b -p ~/miniconda && \
    rm ~/miniconda.sh

# Create a Python 3.10 environment.
RUN /home/pyg/miniconda/bin/conda install conda-build && \
    /home/pyg/miniconda/bin/conda create -y --name py310 python=3.10 && \
    /home/pyg/miniconda/bin/conda clean -ya

ENV CONDA_DEFAULT_ENV=py310
ENV CONDA_AUTO_UPDATE_CONDA=false
ENV CONDA_PREFIX=/home/pyg/miniconda/envs/$CONDA_DEFAULT_ENV

ENV CPATH=$CONDA_PREFIX/include:/usr/local/cuda/include:$CPATH
ENV LIBRARY_PATH=$CONDA_PREFIX/lib/stubs:/usr/local/cuda/lib64/stubs:$LIBRARY_PATH
ENV LD_LIBRARY_PATH=$CONDA_PREFIX/lib:/usr/local/cuda/lib64:$LD_LIBRARY_PATH
ENV DYLD_LIBRARY_PATH=$CONDA_PREFIX/lib:/usr/local/cuda/lib64:$DYLD_LIBRARY_PATH
ENV PATH=$CONDA_PREFIX/bin:/usr/local/cuda/bin:$PATH

# conda install pyg -c pyg

# Install PyTorch & PyG
RUN pip3 install torch torchvision torchaudio --no-cache-dir && \
    pip3 install pyg-lib torch-scatter torch-sparse -f https://data.pyg.org/whl/torch-1.13.0+cu117.html --no-cache-dir && \
    pip3 install torch-cluster torch-spline-conv -f https://data.pyg.org/whl/torch-1.13.0+cu117.html --no-cache-dir && \
    pip3 install torch-geometric --no-cache-dir
