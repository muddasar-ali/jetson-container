ARG TENSORRT="7"
ARG CUDA="10"

FROM hakuyyf/tensorrtx:trt${TENSORRT}_cuda${CUDA}

# Get opencv 3.4 for bionic based images
RUN rm /etc/apt/sources.list.d/timsc-ubuntu-opencv-3_3-bionic.list
RUN rm /etc/apt/sources.list.d/timsc-ubuntu-opencv-3_3-bionic.list.save
RUN add-apt-repository -y ppa:timsc/opencv-3.4

RUN apt-get update
RUN apt-get install -y libopencv-dev libopencv-dnn-dev libopencv-shape3.4-dbg

# git clone tensorrtx
#RUN git clone https://github.com/wang-xinyu/tensorrtx.git




#
# OpenCV
#
#ARG L4T_APT_KEY
#ARG L4T_APT_SOURCE



#COPY jetson-ota-public.asc /etc/apt/trusted.gpg.d/jetson-ota-public.asc

#RUN echo "deb https://repo.download.nvidia.com/jetson/common r32.4 main" > /etc/apt/sources.list.d/nvidia-l4t-apt-source.list && \
#    cat /etc/apt/sources.list.d/nvidia-l4t-apt-source.list && \
RUN apt-get update && \
    apt-get install -y \
            libopencv-dev &&\
	ln -s /usr/include/opencv2 /usr/include/opencv
#		  libopencv-python
#    && rm /etc/apt/sources.list.d/nvidia-l4t-apt-source.list \
#    && rm -rf /var/lib/apt/lists/*






#
#Libzmq installation
#

RUN apt-get update -y &&\
    apt-get install -y libzmq3-dev 

#
#install unzipper for weights in tensorrtx
#
RUN apt-get install unzip 

#
#Tensorrtx Installation
#    
   
RUN git clone https://github.com/muddasar-ali/tensorrtx.git && \
    cd tensorrtx/yolov5 &&\
    pip3 install -r requirements.txt &&\
    unzip weights.zip &&\
    cd build &&\
    cmake .. &&\
    make &&\
    ./yolov5 -s

#
#GSTREAMER INSTALLATION WITH GST-RSTP-SERVER
#

RUN apt-get install -y libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly gstreamer1.0-libav gstreamer1.0-doc gstreamer1.0-tools gstreamer1.0-x gstreamer1.0-alsa gstreamer1.0-gl gstreamer1.0-gtk3 gstreamer1.0-qt5 gstreamer1.0-pulseaudio && \
        git clone git://anongit.freedesktop.org/gstreamer/gst-rtsp-server && \
        cd gst-rtsp-server && \
        git checkout remotes/origin/1.2 && \
        ./autogen.sh --noconfigure && GST_PLUGINS_GOOD_DIR=$(pkg-config --variable=pluginsdir gstreamer-plugins-bad-1.0) ./configure && make

#
#Installation of json-c
#

RUN git clone https://github.com/json-c/json-c.git && \
	mkdir json-c-build && \
	cd json-c-build && \
	cmake ../json-c && \
	make && \
	make test && \
	make USE_VALGRIND=0 test && \
	make install


#
# JupyterLab
#
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash - && \
    apt-get update && \
    apt-get install -y nodejs && \
    rm -rf /var/lib/apt/lists/* && \
    apt-get clean && \
    pip3 install jupyter jupyterlab==2.2.9 --verbose && \
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    
RUN jupyter lab --generate-config
RUN python3 -c "from notebook.auth.security import set_password; set_password('nvidia', '/root/.jupyter/jupyter_notebook_config.json')"

CMD /bin/bash -c "jupyter lab --ip 0.0.0.0 --port 8888 --allow-root &> /var/log/jupyter.log" & \
	echo "allow 10 sec for JupyterLab to start @ http://$(hostname -I | cut -d' ' -f1):8888 (password nvidia)" && \
	echo "JupterLab logging location:  /var/log/jupyter.log  (inside the container)" && \
	/bin/bash
