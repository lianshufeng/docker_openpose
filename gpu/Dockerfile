# https://github.com/lianshufeng/docker_openpose
# FROM nvidia/cuda:11.7.0-devel-ubuntu22.04
# FROM nvidia/cuda:11.4.0-cudnn8-devel-ubuntu18.04
FROM nvidia/cuda:11.4.3-cudnn8-devel-ubuntu20.04


#get deps
RUN apt-get update && \
DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
python3-dev python3-pip python3-setuptools git g++ wget make libprotobuf-dev protobuf-compiler libopencv-dev \
libgoogle-glog-dev libboost-all-dev libcaffe-cuda-dev libhdf5-dev libatlas-base-dev

#for python api
RUN pip3 install --upgrade pip
RUN pip3 install numpy opencv-python 

#replace cmake as old version has CUDA variable bugs
RUN wget https://github.com/Kitware/CMake/releases/download/v3.16.0/cmake-3.16.0-Linux-x86_64.tar.gz && \
tar xzf cmake-3.16.0-Linux-x86_64.tar.gz -C /opt && \
rm cmake-3.16.0-Linux-x86_64.tar.gz
ENV PATH="/opt/cmake-3.16.0-Linux-x86_64/bin:${PATH}"

#get openpose
WORKDIR /openpose
RUN git clone https://github.com/CMU-Perceptual-Computing-Lab/openpose.git .

#build it
WORKDIR /openpose/build


# Downloads all available models. You can reduce image size by being more selective.
RUN cmake -DOWNLOAD_BODY_25_MODEL:Bool=ON \
          -DDOWNLOAD_BODY_MPI_MODEL:Bool=ON \
          -DDOWNLOAD_BODY_COCO_MODEL:Bool=ON \
          -DDOWNLOAD_FACE_MODEL:Bool=ON \
          -DDOWNLOAD_HAND_MODEL:Bool=ON \
          ..


RUN cmake -DBUILD_PYTHON=ON .. && make -j `nproc`


RUN apt-get remove wget unzip cmake git build-essential -y && apt-get autoremove -y

WORKDIR /openpose


ENTRYPOINT ["build/examples/openpose/openpose.bin"]

CMD ["--help"]