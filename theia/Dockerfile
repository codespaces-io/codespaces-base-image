FROM ubuntu:18.04


WORKDIR /opt/theia
VOLUME /workspace

RUN apt-get update && \
        apt-get install -y make g++ python git curl wget sudo 

RUN apt-get install -yq apt-transport-https ca-certificates curl gnupg-agent software-properties-common  && \
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - \
    && add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs)  stable" \
    && apt-get update && \
    apt-get install -yq docker-ce  docker-ce-cli containerd.io  && \
    apt-get install -y openssh-server vim && \
    mkdir /var/run/sshd && \
    echo 'StrictHostKeyChecking no' >> /etc/ssh/ssh_config

COPY package.json .

RUN curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.5/install.sh | bash \ 
    && export NVM_DIR="$HOME/.nvm" \
    && [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  \
    && [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion" \
    && nvm install 8 \
    && npm install -g yarn \
    && yarn  \
    && yarn theia build 

COPY motd.sh  /opt/

RUN chmod +x /opt/motd.sh && \
    sh /opt/motd.sh && \
    rm -rf /opt/motd.sh

COPY insecure_key.pem /root/.ssh/id_rsa

RUN useradd -ms /bin/bash devops && \
    echo 'devops:codespaces' |chpasswd && \
    adduser devops sudo && \
    echo 'devops ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers && \
    echo 'StrictHostKeyChecking no' >> /etc/ssh/ssh_config && \ 
    chmod 400 /root/.ssh/id_rsa 


COPY entrypoint.sh /usr/local/bin

EXPOSE 22 8000

ENV SHELL=/bin/bash

ENTRYPOINT entrypoint.sh

CMD yarn theia start /workspace --hostname 0.0.0.0 --port 8000
