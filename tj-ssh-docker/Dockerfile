FROM docker.io/nvidia/cuda:11.3.0-devel-ubuntu20.04

USER root

ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update -qq && apt-get install -y -q --fix-missing \
        build-essential python3-dev libevent-dev libffi-dev musl-dev \
        openssh-server software-properties-common vim \
        curl wget git unzip zlib1g-dev locales \
        supervisor nginx sudo net-tools zenity xz-utils \
    && apt autoclean -y \
    && apt autoremove -y \
    && apt-get clean -qq && rm -rf /var/lib/apt/lists/*

RUN locale-gen en_US.UTF-8
ENV LANG=en_US.UTF-8 LANGUAGE=en_US.en LC_ALL=en_US.UTF-8

# Python environment
ENV PATH=/opt/conda/bin:$PATH
# conda needs an untainted base environment to function properly
# that's why a new separate conda environment is created
RUN curl "https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh" --output ~/miniconda.sh && \
    /bin/bash ~/miniconda.sh -b -p /opt/conda && \
    rm -rf ~/.cache ~/miniconda.sh

SHELL ["/bin/bash", "-c"]

# python library
RUN pip install --no-cache-dir setuptools wheel \
    && pip install --no-cache-dir torch==1.12.1+cu113 torchvision==0.13.1+cu113 torchaudio==0.12.1 --extra-index-url https://download.pytorch.org/whl/cu113

RUN echo "export PATH=/opt/conda/bin:$PATH" >> /etc/profile \
    && echo "PermitRootLogin yes" >> /etc/ssh/sshd_config \
    && echo 'root:toor' | chpasswd \
    && service ssh start

EXPOSE 22

CMD ["/usr/sbin/sshd","-D"]
