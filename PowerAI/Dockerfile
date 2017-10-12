FROM apollos/ubuntu-cuda8.0-cudnn5.1:16.04
LABEL maintainer "Yu Song <sysongyu@cn.ibm.com>"

RUN curl -fsSL  https://download.boulder.ibm.com/ibmdl/pub/software/server/POWER/Linux/mldl/ubuntu/mldl-repo-local_4.0.0_ppc64el.deb -O && \
	dpkg -i mldl-repo-local_4.0.0_ppc64el.deb && \
	apt-get update && apt-get install -y libopenblas-base libopenblas-dev libopenblas sudo power-mldl
ENV PATH ${PATH}:/opt/DL/caffe/bin
ENV PYTHONPATH ${PYTHONPATH}:/opt/DL/caffe/python
EXPOSE 22
EXPOSE 5000
CMD ["/usr/sbin/sshd", "-D"]
