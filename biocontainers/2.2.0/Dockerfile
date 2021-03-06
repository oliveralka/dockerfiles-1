################## BASE IMAGE ######################

FROM biocontainers/biocontainers:latest

################## METADATA ######################
LABEL base.image="biocontainers:latest"
LABEL version="2.2"
LABEL software="OpenMS"
LABEL software.version="2.2"
LABEL description="C++ libraries ans tools for MS/MS data analysis"
LABEL website="http://www.openms.org/"
LABEL documentation="http://ftp.mi.fu-berlin.de/pub/OpenMS/release2.2.0-documentation/html/index.html"
LABEL license="http://www.openms.org/"
LABEL tags="Proteomics"

################## MAINTAINER ######################
MAINTAINER Hannes Rost <hannes.rost@utoronto.ca>

USER root

# setup build system
RUN      apt-get -y update && \
         apt-get install -y --no-install-recommends --no-install-suggests g++ autoconf patch libtool make git g++ automake && \
         apt-get install -y --no-install-recommends --no-install-suggests software-properties-common python-software-properties && \
         apt-get clean && \
         apt-get purge && \
         rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# fix to get cmake 3.x in ubuntu 14.04
RUN add-apt-repository ppa:george-edison55/cmake-3.x
RUN      apt-get -y update && \
         apt-get install -y cmake && \
         apt-get clean && \
         apt-get purge && \
         rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# install build dependencies: qt, libsvm, glpk
RUN      apt-get -y update && \
         apt-get install -y cmake && \
         apt-get install -y --no-install-recommends --no-install-suggests qt4-dev-tools qt4-default qt4-qtconfig qt4-qmake && \
         apt-get install -y --no-install-recommends --no-install-suggests libqt4-dev libqt4-opengl-dev libqt4-svg && \
         apt-get install -y --no-install-recommends --no-install-suggests libqtwebkit4 libqtwebkit-dev && \
         apt-get install -y --no-install-recommends --no-install-suggests libsvm-dev libglpk-dev libzip-dev zlib1g-dev libxerces-c-dev libbz2-dev && \
         apt-get install -y --no-install-recommends --no-install-suggests libboost-date-time-dev \
                                 libboost-iostreams-dev \
                                 libboost-regex-dev \
                                 libboost-math-dev \
                                 libboost-random-dev && \
         apt-get clean && \
         apt-get purge && \
         rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# build contrib
WORKDIR /home/biodocker/

RUN git clone https://github.com/OpenMS/contrib.git && rm -rf contrib/.git/
RUN mkdir contrib-build

WORKDIR /home/biodocker/contrib-build

RUN cmake -DBUILD_TYPE=SEQAN ../contrib && rm -rf archives src
RUN cmake -DBUILD_TYPE=WILDMAGIC ../contrib && rm -rf archives src
RUN cmake -DBUILD_TYPE=EIGEN ../contrib && rm -rf archives src
RUN cmake -DBUILD_TYPE=COINOR ../contrib && rm -rf archives src
RUN cmake -DBUILD_TYPE=SQLITE ../contrib && rm -rf archives src

WORKDIR /home/biodocker/

###################################
# Install OpenMS

RUN git clone https://github.com/OpenMS/OpenMS.git && cd OpenMS && git checkout tags/Release2.2.0 && \
  hash=`git log | head -n 1 | cut -f 2 -d ' '` && \
  sed -i "s/OPENMS_GIT_SHA1/\"$hash\"/" src/openms/source/CONCEPT/VersionInfo.cpp && \
  rm -rf .git/ share/OpenMS/examples/ 

WORKDIR /usr/local/

RUN git clone https://github.com/OpenMS/THIRDPARTY.git openms_thirdparty && rm -rf .git Windows MacOS Linux/32bit
ENV PATH /usr/local/openms_thirdparty/All/:$PATH
ENV PATH /usr/local/openms_thirdparty/All/LuciPHOr2:$PATH
ENV PATH /usr/local/openms_thirdparty/All/MSGFPlus:$PATH
ENV PATH /usr/local/openms_thirdparty/All/Sirius:$PATH
ENV PATH /usr/local/openms_thirdparty/Linux/64bit/:$PATH
ENV PATH /usr/local/openms_thirdparty/Linux/64bit/Comet:$PATH
ENV PATH /usr/local/openms_thirdparty/Linux/64bit/Fido:$PATH
ENV PATH /usr/local/openms_thirdparty/Linux/64bit/MyriMatch:$PATH
ENV PATH /usr/local/openms_thirdparty/Linux/64bit/OMSSA:$PATH
ENV PATH /usr/local/openms_thirdparty/Linux/64bit/Percolator:$PATH
ENV PATH /usr/local/openms_thirdparty/Linux/64bit/Sirius:$PATH
ENV PATH /usr/local/openms_thirdparty/Linux/64bit/SpectraST:$PATH
ENV PATH /usr/local/openms_thirdparty/Linux/64bit/XTandem:$PATH

WORKDIR /home/biodocker/
RUN mkdir openms-build
WORKDIR /home/biodocker/openms-build

RUN cmake -DCMAKE_PREFIX_PATH="/home/biodocker/contrib-build/;/usr/;/usr/local" -DCMAKE_INSTALL_PREFIX=/usr/local/ -DBOOST_USE_STATIC=OFF -DHAS_XSERVER=Off ../OpenMS && \
   make TOPP -j6 && make UTILS -j6 && \
   make install -j6 && \
   rm -rf src doc CMakeFiles

ENV PATH /usr/local/bin/:$PATH
ENV LD_LIBRARY_PATH /usr/local/lib/:$LD_LIBRARY_PATH

WORKDIR /usr/local/
RUN zip -r openms2.2.0.zip lib/lib* share/OpenMS/* bin/*
WORKDIR /home/biodocker/

# sudo docker run -v /media/data/shared_vm/:/data:rw -it $CONTAINER_ID cp /usr/local/openms2.2.0.zip /data/

#################### END INSTALLATION #########################


