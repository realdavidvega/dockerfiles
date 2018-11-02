# Base image
FROM ubuntu:16.04

LABEL maintainer="David Vega Lichacz <davidvegalichacz@gmail.com>"

WORKDIR /

# Installing Python, Jupyter, Tensorflow, OpenAI Gym
###################################################
# 1. installing python2 and python3
RUN apt-get update && \
	apt install -y --no-install-recommends \
		python3-pip \
		python-pip \
		python3 \
		python

# 1.1 uppgrade pip and pip3
RUN pip3 install --upgrade pip setuptools && pip install --upgrade pip

# 2. installing jupyter, and a bunch of Science Python Packages
# packages taken from https://hub.docker.com/r/jupyter/datascience-notebook/
RUN pip3 install \
			jupyter \
			pandas \
			matplotlib==2.0.2 \
			scipy \
			seaborn \
			scikit-learn \
			scikit-Image \
			sympy \
			cython \
			patsy \
			statsmodels \
			cloudpickle \
			dill \
			numba \
			bokeh

# 3. installing Tensorflow (GPU)
# see here https://www.tensorflow.org/install/install_linux#InstallingNativePip
RUN pip3 install tensorflow

# 4. installing OpenAI Gym (plus dependencies)
RUN pip3 install gym pyopengl

# 4.1 installing roboschool and its dependencies. We love FOSS
RUN apt-get install -y --no-install-recommends cmake ffmpeg pkg-config qtbase5-dev libqt5opengl5-dev libassimp-dev libpython3.5-dev libboost-python-dev libtinyxml-dev
# This got some dependencies, so let's get going
# https://github.com/openai/roboschool
WORKDIR /gym
ENV ROBOSCHOOL_PATH="/gym/roboschool"
# installing bullet (the physics engine of roboschool) and its dependencies
RUN apt-get install -y --no-install-recommends git gcc g++ && \
	git clone https://github.com/openai/roboschool && \
	git clone https://github.com/olegklimov/bullet3 -b roboschool_self_collision && \
	mkdir bullet3/build && \
	cd    bullet3/build && \
	cmake -DBUILD_SHARED_LIBS=ON -DUSE_DOUBLE_PRECISION=1 -DCMAKE_INSTALL_PREFIX:PATH=$ROBOSCHOOL_PATH/roboschool/cpp-household/bullet_local_install -DBUILD_CPU_DEMOS=OFF -DBUILD_BULLET2_DEMOS=OFF -DBUILD_EXTRAS=OFF  -DBUILD_UNIT_TESTS=OFF -DBUILD_CLSOCKET=OFF -DBUILD_ENET=OFF -DBUILD_OPENGL3_DEMOS=OFF .. && \
	make -j4 && \
	make install

# 5. installing X and xvfb so we can see the action using VNC (remote desktop)
# and because this is the last apt, let's clean up after ourselves
RUN apt-get install -y \
			x11vnc \
			xvfb \
			fluxbox \
			wmctrl

RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# 6. Add workspace directories
RUN mkdir -p  /workspace/data /workspace/notebooks workspace/logs
RUN chmod -R a+rwx /workspace

WORKDIR /workspace

# 7. Add Backtrader gym environment
RUN mkdir /environment && cd /environment && \
    git clone https://github.com/Kismuz/btgym.git && \
    cd btgym/ && pip3 install -e . && cd /

# 8. Enable jupyter widgets
RUN jupyter nbextension enable --py --sys-prefix widgetsnbextension

# Run Jupyter
CMD cd /workspace && \
		  jupyter notebook --ip=0.0.0.0 --allow-root --no-browser \
			--NotebookApp.token='' && \
			Xvfb -s '-screen 0 1280x960x24'
