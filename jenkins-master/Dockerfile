FROM ubuntu:20.04

USER root

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    openjdk-17-jdk \
    wget \
    git \
    curl \
    gnupg2 \
    && rm -rf /var/lib/apt/lists/*

RUN useradd -m -d /var/jenkins_home -s /bin/bash jenkins

WORKDIR /var/jenkins_home

RUN wget https://get.jenkins.io/war-stable/2.440.1/jenkins.war

EXPOSE 8080

CMD ["java", "-jar", "jenkins.war"]
