# Apache-2.0 license
# https://github.com/facebookresearch/detectron2/issues/4335#issuecomment-1167280802

FROM pytorch/pytorch:1.10.0-cuda11.3-cudnn8-devel
ENV DEBIAN_FRONTEND noninteractive

# https://github.com/NVIDIA/nvidia-docker/issues/1632#issuecomment-1112667716
RUN rm /etc/apt/sources.list.d/cuda.list
RUN rm /etc/apt/sources.list.d/nvidia-ml.list

RUN apt-get update 
RUN apt-get install -y python3-opencv ca-certificates python3-dev git wget sudo ninja-build

RUN ln -sv /usr/bin/python3 /usr/bin/python

# create a non-root user
# ARG USER_ID=1000
# RUN useradd -m --no-log-init --system  --uid ${USER_ID} appuser -g sudo
# RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers
# USER appuser
# WORKDIR /home/appuser

ENV PATH="/home/appuser/.local/bin:${PATH}"
RUN wget https://bootstrap.pypa.io/pip/get-pip.py && \
	python3 get-pip.py --user && \
	rm get-pip.py
    
# install dependencies
# See https://pytorch.org/ for other options if you use a different version of CUDA
# RUN pip3 install --user tensorboard cmake   # cmake from apt-get is too old
RUN pip3 install --user cmake   # cmake from apt-get is too old
# RUN pip3 install --user torch==1.10 torchvision==0.11.1 -f https://download.pytorch.org/whl/cu111/torch_stable.html

RUN pip3 install --user 'git+https://github.com/facebookresearch/fvcore'
# install detectron2
RUN git clone https://github.com/facebookresearch/detectron2 detectron2_repo
# set FORCE_CUDA because during `docker build` cuda is not accessible
ENV FORCE_CUDA="1"
# This will by default build detectron2 for all common cuda architectures and take a lot more time,
# because inside `docker build`, there is no way to tell which architecture will be used.
ARG TORCH_CUDA_ARCH_LIST="Kepler;Kepler+Tesla;Maxwell;Maxwell+Tegra;Pascal;Volta;Turing"
ENV TORCH_CUDA_ARCH_LIST="${TORCH_CUDA_ARCH_LIST}"

RUN pip3 install --user -e detectron2_repo

# Set a fixed model cache directory.
ENV FVCORE_CACHE="/tmp"

# set up maskdino
RUN git clone https://github.com/Itto1992/MaskDINO.git && \
    cd MaskDINO && \
    pip install -r requirements.txt && \
    cd maskdino/modeling/pixel_decoder/ops && \
    sh make.sh

WORKDIR /workspace/MaskDINO
