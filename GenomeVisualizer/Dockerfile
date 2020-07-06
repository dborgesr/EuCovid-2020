# Image to process and analyze Nanopore RNA-seq data
FROM ubuntu:18.04
MAINTAINER Diego Borges <dborgesrmit@gmail.com>
USER root

ENV APT_PACKAGES=" \
		software-properties-common \
		apt-transport-https \
		ca-certificates \
		locales \
		fonts-liberation \
		apt-utils \
		libreadline-dev \
		zlib1g-dev \
		libgsl0-dev \
		wget \
#		build-essential \
		curl \
		zip \
		"

#Get apt packages
RUN apt-get update && \
	apt-get install -y --no-install-recommends $APT_PACKAGES && \
 	rm -rf "/var/lib/apt/lists/*" && \
	apt-get clean && \
	rm -rf /var/cache/apt

#Get conda dependencies
COPY environment.yml /tmp/environment.yml
#Get Anaconda and intialize python env
ENV CONDA_VERSION="4.7.12.1"
RUN curl -s -L https://repo.anaconda.com/miniconda/Miniconda3-$CONDA_VERSION-Linux-x86_64.sh > miniconda.sh && \
    bash miniconda.sh -u -b -p /opt/conda && \
    rm miniconda.sh && \
    export PATH=/opt/conda/bin:$PATH && \
    conda env create -f /tmp/environment.yml && \
#    conda update conda && \
#    conda update --all --yes && \
	conda clean -tipy
ENV PATH=/opt/conda/envs/env/bin/:/opt/conda/bin:$PATH

# Set Eucovid as working directory
USER root
RUN mkdir /data && \
 		mkdir /data/Eucovid && \
		chmod -R 0777 /data/Eucovid
WORKDIR /data/Eucovid
COPY ./start.sh /app/
RUN chmod 755 /app/start.sh

#set bash as default shell and add activation of environment to bash start
ENV SHELL=/bin/bash
#Set bash up to start and activate env environment
RUN echo "export PATH=/opt/conda/envs/env/bin/:/opt/conda/bin/:$PATH" > ~/.bashrc && \
	echo "source activate base" >> ~/.bashrc

#Configure jupyter
ENV JUPYTER_PREFIX="/root/"
RUN jupyter notebook --generate-config && \
	sed -i "/c.NotebookApp.open_browser/c c.NotebookApp.open_browser = False" "$JUPYTER_PREFIX".jupyter/jupyter_notebook_config.py \
        && sed -i "/c.NotebookApp.ip/c c.NotebookApp.ip = '*'" "$JUPYTER_PREFIX".jupyter/jupyter_notebook_config.py \
        && sed -i "/c.NotebookApp.notebook_dir/c c.NotebookApp.notebook_dir = '/data/Eucovid'" "$JUPYTER_PREFIX".jupyter/jupyter_notebook_config.py \
        && sed -i "/c.NotebookApp.token/c c.NotebookApp.token = 'eucovid'" "$JUPYTER_PREFIX".jupyter/jupyter_notebook_config.py \
        && sed -i "/c.NotebookApp.iopub_data_rate_limit/c c.NotebookApp.iopub_data_rate_limit = 10000000" "$JUPYTER_PREFIX".jupyter/jupyter_notebook_config.py

#Define default startup behavior
ENV EUPLO_IMAGE=genome_visualizer
ENV EUPLO_DEPLOY=false
ENV EUPLO_ARCH=x86
ENV EUPLO_OS=ubuntu
ENV JUPYTER_PORT=8891

EXPOSE $JUPYTER_PORT
EXPOSE 80
CMD ["bash", "/app/start.sh"]
