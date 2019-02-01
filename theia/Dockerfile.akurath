FROM ubuntu:14.04
MAINTAINER  Vijayboopathy E <vibe@initcron.org>

WORKDIR /workspace
VOLUME /workspace

RUN apt-get update && \
        apt-get install -y make g++ python git curl wget && \
        curl -sL https://deb.nodesource.com/setup | sudo bash - && \
        apt-get install -y nodejs && \
        npm -g install node-gyp akurath && \
        cd /usr/lib/node_modules/akurath/node_modules/shux/node_modules/pty.js && \
        make clean && \
        make

RUN apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D && \
    echo "deb https://apt.dockerproject.org/repo ubuntu-trusty main" >> /etc/apt/sources.list.d/docker.list && \
    apt-get update && \
    apt-get install -yq docker-engine  && \
    apt-get install -y openssh-server sudo vim && \
    mkdir /var/run/sshd && \
    echo 'StrictHostKeyChecking no' >> /etc/ssh/ssh_config

COPY motd.sh /opt/

RUN chmod +x /opt/motd.sh && \
    sh /opt/motd.sh && \
    rm -rf /opt/motd.sh

RUN useradd -ms /bin/bash devops && \
    echo 'devops:codespaces' |chpasswd && \
    adduser devops sudo && \
    echo 'devops ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers && \
    echo 'StrictHostKeyChecking no' >> /etc/ssh/ssh_config

COPY insecure_key.pem /root/.ssh/id_rsa

EXPOSE 22 8000

CMD /usr/bin/akurath run
