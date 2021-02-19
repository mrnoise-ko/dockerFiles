FROM ubuntu:focal

MAINTAINER Steffen Fuchs "fuchs@kobold.com"

RUN apt-get update &&  apt-get install  -y 
RUN apt-get install -y ruby-full 
RUN apt-get install -y gcc  
RUN apt-get install -y build-essential
RUN ruby -v
RUN gem install ceedling && ceedling version
