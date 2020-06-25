FROM ubuntu:18.04 AS build
# install development tools
RUN apt-get update && \
    apt-get install -y curl sudo build-essential jq vim && \
    curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash - && \
    apt-get install -y nodejs && \
    npm install -g yarn

# add theia installation file
COPY package.json run_theia.sh /theia-app/
RUN chmod +x /theia-app/run_theia.sh

# build theia
WORKDIR /theia-app
RUN yarn && \
    NODE_OPTIONS="--max_old_space_size=4096" yarn theia build && \
    yarn autoclean --init && \
    echo *.ts >> .yarnclean && \
    echo *.ts.map >> .yarnclean && \
    echo *.spec.* >> .yarnclean && \
    yarn autoclean --force && \
    yarn cache clean

# create debian package
RUN npm run build-deb




FROM ubuntu:18.04 AS package

COPY --from=build /theia-app/theia-example_0.0.1_all.deb /
RUN apt-get update && apt-get install -y curl sudo && \
    curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash - && \
    apt-get install -y ./theia-example_0.0.1_all.deb && \
    rm theia-example_0.0.1_all.deb

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


#COPY entrypoint.sh /usr/local/bin

EXPOSE 22 8000

ENV SHELL=/bin/bash

#ENTRYPOINT entrypoint.sh

CMD ["theia","start","--hostname=0.0.0.0","--port=8000"]

