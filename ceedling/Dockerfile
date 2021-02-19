FROM ubuntu:focal

MAINTAINER Steffen Fuchs "fuchs@kobold.com"

RUN apt-get update &&  apt-get install  -y 
#RUN apt-get install -y ruby-full  
RUN gpg2 --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
RUN \curl -sSL https://get.rvm.io | bash -s stable --rails
RUN apt-get install -y gcc  
RUN apt-get install -y build-essential
RUN gem install ceedling
