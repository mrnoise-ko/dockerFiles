FROM ubuntu:focal

MAINTAINER Steffen Fuchs "fuchs@kobold.com"

ENV TZ=Europe/Berlin
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get update &&  apt-get install  -y 
RUN apt-get install -y ruby-full 
RUN apt-get install -y gcc  
RUN apt-get install -y build-essential
RUN apt-get -y install gcovr
RUN ruby -v
RUN gem install ceedling && ceedling version
