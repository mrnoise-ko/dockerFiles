FROM ubuntu:focal

MAINTAINER Steffen Fuchs "fuchs@kobold.com"

RUN apt-get update &&  apt-get install  -y 
#RUN apt-get install -y ruby-full 
RUN apt-get install -y gcc  
RUN apt-get install -y build-essential
RUN sudo apt install curl g++ gcc autoconf automake bison libc6-dev \
        libffi-dev libgdbm-dev libncurses5-dev libsqlite3-dev libtool \
        libyaml-dev make pkg-config sqlite3 zlib1g-dev libgmp-dev \
        libreadline-dev libssl-dev
RUN gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
RUN curl -sSL https://get.rvm.io | bash -s stable
RUN source ~/.rvm/scripts/rvm
RUN rvm list known
RUN rvm install ruby-3.0.0
RUN rvm --default use ruby-3.0.0
RUN gem install ceedling
