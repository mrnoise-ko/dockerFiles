FROM ubuntu:focal

MAINTAINER Steffen Fuchs "fuchs@kobold.com"

RUN ln -snf /usr/share/zoneinfo/$CONTAINER_TIMEZONE /etc/localtime && echo $CONTAINER_TIMEZONE > /etc/timezone
RUN apt-get update &&  apt-get install  -y 
RUN apt-get install -y gcc  
RUN apt-get install -y build-essential
RUN apt-get -y install gcovr
RUN apt-get install -y cmake
RUN cmake --version
