# Adapted from https://github.com/microsoft/vscode-dev-containers
# Using regular RUN statements for readability
# If image size and/or layers become a problem, see https://docs.docker.com/develop/develop-images/multistage-build/

# Using Debian 10 (buster)
FROM debian:buster

# Avoid warnings by switching to noninteractive
ENV DEBIAN_FRONTEND=noninteractive

# This Dockerfile adds a non-root 'vscode' user with sudo access. However, for Linux,
# this user's GID/UID must match your local user UID/GID to avoid permission issues
# with bind mounts. Update USER_UID / USER_GID if yours is not 1000. See
# https://aka.ms/vscode-remote/containers/non-root-user for details.
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Configure apt and install packages
RUN apt-get update
RUN apt-get -y install --no-install-recommends apt-utils dialog 2>&1 

# Set locale
# Completely non-interactive thanks to https://serverfault.com/a/894545/102552
# Uncomment en_US.UTF-8 for inclusion in generation
RUN apt-get install -y locales
RUN sed -i 's/^# *\(en_US.UTF-8\)/\1/' /etc/locale.gen
RUN locale-gen
RUN echo "export LC_ALL=en_US.UTF-8" >> ~/.bashrc
RUN echo "export LANG=en_US.UTF-8" >> ~/.bashrc
RUN echo "export LANGUAGE=en_US.UTF-8" >> ~/.bashrc

# Verify git and needed tools are installed
RUN apt-get -y install git iproute2 procps git vim curl apt-transport-https lsb-release bash-completion

# Install nodejs 12 LTS
RUN curl -sL https://deb.nodesource.com/setup_12.x | bash -
RUN apt-get install -y nodejs

# Install yarn
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list
RUN apt-get update && apt-get -y install --no-install-recommends yarn

# Create a non-root user to use if preferred - see https://aka.ms/vscode-remote/containers/non-root-user.
RUN groupadd --gid $USER_GID $USERNAME
RUN useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME
# Add sudo support for the non-root user
RUN apt-get install -y sudo
RUN echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME
RUN chmod 0440 /etc/sudoers.d/$USERNAME

# Clean up
RUN apt-get autoremove -y
RUN apt-get clean -y
RUN rm -rf /var/lib/apt/lists/*

# Switch back to dialog for any ad-hoc use of apt-get
ENV DEBIAN_FRONTEND=
