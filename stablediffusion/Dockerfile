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
RUN adduser --disabled-password --gecos '' --shell /bin/bash sd && \
    chown -R sd:sd /app
RUN echo "sd ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/90-sd

USER sd

ENV HOME=/home/sd
RUN chmod 777 /home/sd

# Install Miniconda.
RUN curl -so ~/miniconda.sh https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh && \
    chmod +x ~/miniconda.sh && \
    ~/miniconda.sh -b -p ~/miniconda && \
    rm ~/miniconda.sh

# Create a Python 3.10 environment.
RUN /home/sd/miniconda/bin/conda install conda-build && \
    /home/sd/miniconda/bin/conda create -y --name py310 python=3.10 && \
    /home/sd/miniconda/bin/conda clean -ya

ENV CONDA_DEFAULT_ENV=py310
ENV CONDA_AUTO_UPDATE_CONDA=false
ENV CONDA_PREFIX=/home/sd/miniconda/envs/$CONDA_DEFAULT_ENV

ENV CUDA_HOME=/usr/local/cuda
ENV CPATH=$CONDA_PREFIX/include:/usr/local/cuda/include:$CPATH
ENV LIBRARY_PATH=$CONDA_PREFIX/lib/stubs:/usr/local/cuda/lib64/stubs:$LIBRARY_PATH
ENV LD_LIBRARY_PATH=$CONDA_PREFIX/lib:/usr/local/cuda/lib64:$LD_LIBRARY_PATH
ENV DYLD_LIBRARY_PATH=$CONDA_PREFIX/lib:/usr/local/cuda/lib64:$DYLD_LIBRARY_PATH
ENV PATH=$CONDA_PREFIX/bin:/home/sd/miniconda/bin:/usr/local/cuda/bin:$PATH

# Install Conda Deps
# conda install sd -c sd
RUN /home/sd/miniconda/bin/conda install -n py310 -c conda-forge ncurses && \
    /home/sd/miniconda/bin/conda install -n py310 -c nvidia/label/cuda-11.7.0 cuda-nvcc && \
    /home/sd/miniconda/bin/conda install -n py310 -c conda-forge gcc && \
    /home/sd/miniconda/bin/conda install -n py310 -c conda-forge gxx_linux-64=9.5.0

# Install PyTorch & Transformer
RUN pip3 install torch torchvision torchaudio --no-cache-dir

# StableDiffusion
RUN git clone https://github.com/Stability-AI/stablediffusion.git && \
    cd stablediffusion && \
    pip3 install -r requirements.txt --no-cache-dir && \
    pip3 install -e .

# xfomer
RUN git clone https://github.com/facebookresearch/xformers.git && \
    cd xformers && \
    git submodule update --init --recursive && \
    pip3 install -r requirements.txt --no-cache-dir && \
    pip3 install -e .

# additional libs for cv2
RUN sudo apt-get update -y && sudo apt-get install -y \
    libgl1-mesa-glx && \
    libglib2.0-0 && \
    rm -rf /var/lib/apt/lists/*
