FROM ubuntu:22.04 as builder
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update -y && \
    apt-get upgrade -y && \
    apt-get install -y \
        wget apt-utils lsb-core cmake git \
        libopencv-dev \
        protobuf-compiler \
        libprotobuf-dev \
        libgoogle-glog-dev \
        libboost-all-dev \
        hdf5-tools \
        libhdf5-dev \
        libatlas-base-dev



RUN git clone https://github.com/CMU-Perceptual-Computing-Lab/openpose.git

WORKDIR /openpose/scripts/ubuntu
RUN sed -i 's/\<sudo -H\>//g' install_deps.sh; \
    sed -i 's/\<sudo\>//g' install_deps.sh; \
    sync; sleep 1;

# Downloads all available models. You can reduce image size by being more selective.
WORKDIR /openpose/build
RUN cmake -DGPU_MODE:String=CPU_ONLY \
          -DOWNLOAD_BODY_25_MODEL:Bool=ON \
          -DDOWNLOAD_BODY_MPI_MODEL:Bool=ON \
          -DDOWNLOAD_BODY_COCO_MODEL:Bool=ON \
          -DDOWNLOAD_FACE_MODEL:Bool=ON \
          -DDOWNLOAD_HAND_MODEL:Bool=ON \
          -DUSE_MKL:Bool=OFF \
          ..

# you may find that you need to adjust this.
RUN make -j$((`nproc`+1))

# RUN apt-get remove wget unzip cmake git build-essential -y && apt-get autoremove -y

FROM ubuntu:22.04 as runtime
ENV DEBIAN_FRONTEND=noninteractive
COPY --from=builder /openpose/models /openpose/models
COPY --from=builder /openpose/build /openpose/build

RUN apt-get update -y && \
    apt-get upgrade -y && \
    apt-get install -y \
        libopencv-dev \
        protobuf-compiler \
        libprotobuf-dev \
        libgoogle-glog-dev \
        libboost-all-dev \
        hdf5-tools \
        libhdf5-dev \
        libatlas-base-dev \
		build-essential -y && \
	apt-get clean -y && \
	apt-get autoremove -y
		
WORKDIR /openpose

ENTRYPOINT ["build/examples/openpose/openpose.bin"]

CMD ["--help"]