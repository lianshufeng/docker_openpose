# https://github.com/lianshufeng/docker_openpose
#FROM nvidia/cuda:11.4.0-cudnn8-devel-ubuntu18.04
FROM nvidia/cuda:11.4.0-cudnn8-devel-ubuntu18.04 as builder
ENV DEBIAN_FRONTEND=noninteractive

#get deps
RUN apt-get update -y && \
	apt-get install -y --no-install-recommends \
	git g++ wget make \
	python3-dev python3-pip python3-setuptools libprotobuf-dev protobuf-compiler libopencv-dev libgoogle-glog-dev libboost-all-dev libcaffe-cuda-dev libhdf5-dev libatlas-base-dev


#for python api
RUN pip3 install --upgrade pip && pip3 install numpy opencv-python 


# replace cmake as old version has CUDA variable bugs
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



# RUN apt-get remove wget unzip cmake git build-essential -y && apt-get autoremove -y

# 运行环境
FROM nvidia/cuda:11.4.0-cudnn8-devel-ubuntu18.04 as runtime
ENV DEBIAN_FRONTEND=noninteractive
COPY --from=builder /openpose /openpose

# python
RUN apt-get update -y && \
	apt-get install -y --no-install-recommends \
	git g++ wget make \
	python3-dev python3-pip python3-setuptools libprotobuf-dev protobuf-compiler libopencv-dev libgoogle-glog-dev libboost-all-dev libcaffe-cuda-dev libhdf5-dev libatlas-base-dev
RUN pip3 install --upgrade pip && pip3 install numpy opencv-python 

# cmake
RUN wget https://github.com/Kitware/CMake/releases/download/v3.16.0/cmake-3.16.0-Linux-x86_64.tar.gz && \
	tar xzf cmake-3.16.0-Linux-x86_64.tar.gz -C /opt && \
	rm cmake-3.16.0-Linux-x86_64.tar.gz
ENV PATH="/opt/cmake-3.16.0-Linux-x86_64/bin:${PATH}"

# 安装python环境
WORKDIR /openpose/build/python/openpose
RUN make install

# setup env. for pyopenpose
RUN cp ./pyopenpose.cpython-36m-x86_64-linux-gnu.so /usr/local/lib/python3.6/dist-packages
WORKDIR /usr/local/lib/python3.6/dist-packages
RUN ln -s pyopenpose.cpython-36m-x86_64-linux-gnu.so pyopenpose
ENV LD_LIBRARY_PATH=/usr/local/python/openpose


WORKDIR /openpose
ENTRYPOINT ["build/examples/openpose/openpose.bin"]
CMD ["--help"]