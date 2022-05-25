FROM centos:centos6
MAINTAINER xezpeleta@tknika.eus
# Forked from haysclark/docker-adlmflexnetserver

#########################################
##             CONSTANTS               ##
#########################################
# path for Network Licence Manager
ARG NLM_URL=http://download.autodesk.com/us/support/files/network_license_manager/11_13_1_2_v2/Linux/nlm11.13.1.2_ipv4_ipv6_linux64.tar.gz
# path for temporary files
ARG TEMP_PATH=/tmp/flexnetserver

#########################################
##        ENVIRONMENTAL CONFIG         ##
#########################################
# add the flexlm commands to $PATH
ENV PATH="${PATH}:/opt/flexnetserver/"
ENV PATH="${PATH}:/vendors/"

#########################################
##         RUN INSTALL SCRIPT          ##
#########################################

RUN yum update -y && yum install -y \
    redhat-lsb-core \
    wget && \
    yum clean all

RUN mkdir -p ${TEMP_PATH} && cd ${TEMP_PATH} && \
    wget --progress=bar:force ${NLM_URL} && \
    tar -zxvf *.tar.gz && rpm -vhi *.rpm && \
    rm -rf ${TEMP_PATH}

RUN mkdir -p /tmp/esiflex
COPY FLEXnet_v11.16.2.1_Linux-lsb3-x86_64.tar.gz /tmp/esiflex
RUN cd /tmp/esiflex && tar -xzvf *.tar.gz && \
    mv * /opt/flexnetserver && cd /opt/flexnetserver && \
    ./install_fnp.sh

# lmadmin is required for -2 -p flag support
RUN groupadd -r lmadmin && \
    useradd -r -g lmadmin lmadmin

#########################################
##              VOLUMES                ##
#########################################
# Save here your licenses
VOLUME ["/licenses"]
# Save here your vendor daemons
VOLUME ["/vendors"]

#########################################
##            EXPOSE PORTS             ##
#########################################
EXPOSE 2080
EXPOSE 42630-42640
EXPOSE 27000-27009
EXPOSE 28000-28009

# do not use ROOT user
USER lmadmin

ADD utils.sh /utils.sh
ADD entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
# no CMD, use container as if 'lmgrd'

