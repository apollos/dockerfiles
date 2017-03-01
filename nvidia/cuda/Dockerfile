FROM ppc64le/ubuntu:16.04
LABEL maintainer "Yu Song <sysongyu@cn.ibm.com>"
LABEL com.nvidia.volumes.needed="nvidia_driver"

RUN apt-get update && apt-get install -y wget vim curl apt-utils linux-headers-$(uname -r) build-essential python

RUN NVIDIA_GPGKEY_SUM=d1be581509378368edeec8c1eb2958702feedf3bc3d17011adbf24efacce4ab5 && \
    NVIDIA_GPGKEY_FPR=ae09fe4bbd223a84b2ccfce3f60f4b3d7fa2af80 && \
    apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/ppc64el/7fa2af80.pub && \
    apt-key adv --export --no-emit-version -a $NVIDIA_GPGKEY_FPR | tail -n +5 > cudasign.pub && \
    echo "$NVIDIA_GPGKEY_SUM  cudasign.pub" | sha256sum -c --strict - && rm cudasign.pub && \
    echo "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/ppc64el /" > /etc/apt/sources.list.d/cuda.list
ENV CUDA_VERSION 8.0.61
LABEL com.nvidia.cuda.version="${CUDA_VERSION}"
ENV CUDA_PKG_VERSION 8-0=$CUDA_VERSION-1
RUN apt-get update && apt-get install -y --no-install-recommends \
        cuda-nvrtc-$CUDA_PKG_VERSION \
        cuda-nvgraph-$CUDA_PKG_VERSION \
        cuda-cusolver-$CUDA_PKG_VERSION \
        cuda-cublas-$CUDA_PKG_VERSION \
        cuda-cufft-$CUDA_PKG_VERSION \
        cuda-curand-$CUDA_PKG_VERSION \
        cuda-cusparse-$CUDA_PKG_VERSION \
        cuda-npp-$CUDA_PKG_VERSION \
        cuda-cudart-$CUDA_PKG_VERSION && \
    ln -s cuda-8.0 /usr/local/cuda && \
    rm -rf /var/lib/apt/lists/*
RUN echo "/usr/local/cuda/lib64" >> /etc/ld.so.conf.d/cuda.conf && \
    ldconfig
RUN echo "/usr/local/nvidia/lib" >> /etc/ld.so.conf.d/nvidia.conf && \
    echo "/usr/local/nvidia/lib64" >> /etc/ld.so.conf.d/nvidia.conf
ENV PATH /usr/local/nvidia/bin:/usr/local/cuda/bin:${PATH}
ENV LD_LIBRARY_PATH /usr/local/nvidia/lib:/usr/local/nvidia/lib64

RUN apt-get update && apt-get install -y --no-install-recommends \
        cuda-core-$CUDA_PKG_VERSION \
        cuda-misc-headers-$CUDA_PKG_VERSION \
        cuda-command-line-tools-$CUDA_PKG_VERSION \
        cuda-nvrtc-dev-$CUDA_PKG_VERSION \
        cuda-nvml-dev-$CUDA_PKG_VERSION \
        cuda-nvgraph-dev-$CUDA_PKG_VERSION \
        cuda-cusolver-dev-$CUDA_PKG_VERSION \
        cuda-cublas-dev-$CUDA_PKG_VERSION \
        cuda-cufft-dev-$CUDA_PKG_VERSION \
        cuda-curand-dev-$CUDA_PKG_VERSION \
        cuda-cusparse-dev-$CUDA_PKG_VERSION \
        cuda-npp-dev-$CUDA_PKG_VERSION \
        cuda-cudart-dev-$CUDA_PKG_VERSION \
        cuda-driver-dev-$CUDA_PKG_VERSION && \
    rm -rf /var/lib/apt/lists/*
ENV LIBRARY_PATH /usr/local/cuda/lib64/stubs:${LIBRARY_PATH}

#CUDNN

RUN rm -rf /var/lib/apt/lists/*
ENV CUDNN_VERSION 5.1.10
LABEL com.nvidia.cudnn.version="${CUDNN_VERSION}"
RUN CUDNN_DOWNLOAD_SUM=663aac0328f821d90ae9c74ee43e90751706546c2ce769ea9c96f92864300af6 && \
    curl -fsSL http://developer.download.nvidia.com/compute/redist/cudnn/v5.1/cudnn-8.0-linux-ppc64le-v5.1.tgz -O && \
    echo "$CUDNN_DOWNLOAD_SUM  cudnn-8.0-linux-ppc64le-v5.1.tgz" | sha256sum -c --strict - && \
    tar -xzf cudnn-8.0-linux-ppc64le-v5.1.tgz -C /usr/local && \
    rm cudnn-8.0-linux-ppc64le-v5.1.tgz && \
    ldconfig

#Build env
RUN wget https://bootstrap.pypa.io/get-pip.py
RUN python get-pip.py && \
	rm -rf get-pip.py

RUN apt-get update && apt-get install -y openjdk-8-jdk openjdk-8-jre

#ssh
RUN apt-get update && apt-get install -y openssh-server
RUN mkdir /var/run/sshd
RUN echo 'root:screencast' | chpasswd
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile
RUN echo "root:123456" | chpasswd

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
