FROM ubuntu:20.04

USER root

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    openjdk-17-jdk \
    git \
    curl \
    openssh-server \
    && rm -rf /var/lib/apt/lists/*

RUN useradd -m -s /bin/bash jenkins

RUN mkdir /var/run/sshd

EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]
