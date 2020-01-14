#-------------------------------------------------------------------------------------------------------------
# Copyright (c) Microsoft Corporation. All rights reserved.
# Licensed under the MIT License. See https://go.microsoft.com/fwlink/?linkid=2090316 for license information.
#-------------------------------------------------------------------------------------------------------------

FROM node:10-buster

# Avoid warnings by switching to noninteractive
ENV DEBIAN_FRONTEND=noninteractive

# The node image comes with a base non-root 'node' user which this Dockerfile
# gives sudo access. However, for Linux, this user's GID/UID must match your local
# user UID/GID to avoid permission issues with bind mounts. Update USER_UID / USER_GID
# if yours is not 1000. See https://aka.ms/vscode-remote/containers/non-root-user.
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Configure apt and install packages
RUN apt-get update \
    && apt-get -y install --no-install-recommends apt-utils dialog 2>&1 \
    #
    # Verify git and needed tools are installed
    && apt-get -y install git iproute2 procps zsh python3-pip software-properties-common \
    #
    # add Ethereum enviroment
    && add-apt-repository ppa:ethereum/ethereum \
    && apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 1C52189C923F6CA9 \
    && apt-get update \
    #
    # install solc
    && apt-get -y install solc \
    #
    # Install tslint and typescript globally
    && npm install -g tslint typescript \
    #
    # [Optional] Update a non-root user to match UID/GID - see https://aka.ms/vscode-remote/containers/non-root-user.
    && if [ "$USER_GID" != "1000" ]; then groupmod node --gid $USER_GID; fi \
    && if [ "$USER_UID" != "1000" ]; then usermod --uid $USER_UID node; fi \
    # [Optional] Add add sudo support for non-root user
    && apt-get install -y sudo \
    && echo node ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/node \
    && chmod 0440 /etc/sudoers.d/node \
    #
    # for echidna
    # && cd ~ \
    # && pip3 install crytic-compile \
    # && wget --quiet https://github.com/crytic/echidna/releases/download/1.2.0.0/echidna-test-1.2.0.0-Ubuntu-18.04.tar.gz \
    # && tar -xvf ./echidna-test-1.2.0.0-Ubuntu-18.04.tar.gz \
    # && mv ./echidna-test /bin \
    # && rm ./echidna-test-1.2.0.0-Ubuntu-18.04.tar.gz \
    #
    # for Slither
    && pip3 install slither-analyzer \
    #
    # Clean up
    && apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/* \
    #
    # install oh my zsh 
    && chsh -s $(which zsh) \
    && wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | zsh || true \
    #
    # install solc-select
    && git clone https://github.com/crytic/solc-select.git \
    && ./solc-select/scripts/install.sh \
    # && echo "export PATH=/root/.solc-select:\${PATH}" >> ~/.zshrc \
    # && source ~/.zshrc 

# Switch back to dialog for any ad-hoc use of apt-get
ENV DEBIAN_FRONTEND=
