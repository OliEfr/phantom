FROM nvidia/cuda:12.1.0-cudnn8-devel-ubuntu22.04

# general setup
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    wget \
    curl \
    git \
    unzip \
    tar \
    build-essential \
    cmake \
    python3.10 \
    python3.10-dev \
    python3.10-distutils \
    python3-pip \
    python3.10-venv \
    libgl1-mesa-glx \
    libglib2.0-0 \
    libsm6 \
    libxext6 \
    libxrender-dev \
    libgomp1 \
    libgl1-mesa-glx \
    libglib2.0-0 \
    libegl1-mesa \
    libgl1-mesa-dri \
    ffmpeg \
    && rm -rf /var/lib/apt/lists/* && apt-get clean

RUN update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.10 1 && update-alternatives --install /usr/bin/python python /usr/bin/python3.10 1 && update-alternatives --install /usr/bin/pip pip /usr/bin/pip3 1

ENV NVIDIA_VISIBLE_DEVICES ${NVIDIA_VISIBLE_DEVICES:-all}
ENV NVIDIA_DRIVER_CAPABILITIES ${NVIDIA_DRIVER_CAPABILITIES:+$NVIDIA_DRIVER_CAPABILITIES,}graphics


RUN python3 -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"
ENV VIRTUAL_ENV="/opt/venv"
RUN echo 'source /opt/venv/bin/activate' >> ~/.bashrc

# Install project. Mainly taken from ./install.sh of upstream.
# This expects that the entire code has been cloned and additional files have been downloaded.
WORKDIR /app

COPY . .

# Install SAM2
WORKDIR /app/submodules/sam2
RUN pip install -v -e ".[notebooks]"

# Install Hamer
WORKDIR /app/submodules/phantom-hamer
RUN pip install --no-build-isolation -e .[all]
RUN pip install -v -e third-party/ViTPose

# Install PyTorch and mmcv
WORKDIR /app
RUN pip install --index-url https://download.pytorch.org/whl/cu121 torch==2.1.0 torchvision==0.16.0
RUN pip install mmcv==1.3.9
RUN pip install mmcv-full -f https://download.openmmlab.com/mmcv/dist/cu121/torch2.1/index.html
RUN pip install numpy==1.26.4

# Install phantom-robosuite
WORKDIR /app/submodules/phantom-robosuite
RUN pip install -e .

# Install phantom-robomimic
WORKDIR /app/submodules/phantom-robomimic
RUN pip install -e .

# Install additional packages
WORKDIR /app
RUN pip install joblib mediapy open3d pandas
RUN pip install transformers==4.42.4
RUN pip install PyOpenGL==3.1.4
RUN pip install Rtree
RUN pip install git+https://github.com/epic-kitchens/epic-kitchens-100-hand-object-bboxes.git
RUN pip install protobuf==3.20.0
RUN pip install hydra-core==1.3.2
RUN pip install omegaconf==2.3.0
RUN pip install h5py

# Install phantom-E2FGVI
WORKDIR /app/submodules/phantom-E2FGVI
RUN pip install -e .

# Install phantom (main package)
WORKDIR /app
RUN pip install -e .

# Finish everything up
WORKDIR /home
CMD ["/bin/bash"]