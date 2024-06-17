# Docker SSH Server Images

This repository contains Dockerfiles for creating Docker images with SSH access for Ubuntu 22.04 and CentOS 7. Each image allows you to create a user with root privileges from environment variables `USERNAME` and `PASSWORD`, and sets the root password to `asd`.

## Prerequisites

- Docker must be installed on your system.

## Building the Images

### Ubuntu 22.04

1. **Build the Docker Image**:
    ```bash
    docker build --build-arg USERNAME=myuser --build-arg PASSWORD=mypassword -t ubuntu_vm -f ubuntu-22.04-Dockerfile .
    ```
    Replace `myuser` and `mypassword` with your desired username and password.

2. **Run the Container**:
    ```bash
    docker run -d -p 2222:22 --name ubuntu_vm ubuntu_vm
    ```

### CentOS 7

1. **Build the Docker Image**:
    ```bash
    docker build --build-arg USERNAME=myuser --build-arg PASSWORD=mypassword -t centos_vm -f centos7-Dockerfile .
    ```
    Replace `myuser` and `mypassword` with your desired username and password.

2. **Run the Container**:
    ```bash
    docker run -d -p 2222:22 --name centos_vm centos_vm
    ```

## Accessing the Containers via SSH

You can SSH into the containers using the following command:
```bash
ssh -p 2222 myuser@localhost
```
Replace `myuser` with the username you specified during the build. If you want to SSH from a remote machine, replace `localhost` with the public IP address or domain name of your Docker host.

## Dockerfile Examples

### ubuntu-22.04-Dockerfile

```dockerfile
# Use the official Ubuntu 22.04 base image
FROM ubuntu:22.04

# Update the package list and install necessary packages
RUN apt update && apt install -y \
    sudo \
    vim \
    curl \
    wget \
    net-tools \
    iputils-ping \
    openssh-server

# Add environment variables for username and password
ARG USERNAME
ARG PASSWORD

# Create a new user and set its password
RUN useradd -m -s /bin/bash $USERNAME && \
    echo "$USERNAME:$PASSWORD" | chpasswd && \
    adduser $USERNAME sudo

# Set the root password
RUN echo 'root:asd' | chpasswd

# Configure SSH server
RUN mkdir /var/run/sshd && \
    echo 'PermitRootLogin yes' >> /etc/ssh/sshd_config && \
    echo 'PasswordAuthentication yes' >> /etc/ssh/sshd_config && \
    echo 'PermitEmptyPasswords no' >> /etc/ssh/sshd_config

# Expose the SSH port
EXPOSE 22

# Set the default command to run SSH server
CMD ["/usr/sbin/sshd", "-D"]
```

### centos7-Dockerfile

```dockerfile
# Use the official CentOS 7 base image
FROM centos:7

# Update the package list and install necessary packages
RUN yum -y update && yum -y install \
    sudo \
    vim \
    curl \
    wget \
    net-tools \
    iputils \
    openssh-server \
    openssh-clients

# Add environment variables for username and password
ARG USERNAME
ARG PASSWORD

# Create a new user and set its password
RUN useradd -m -s /bin/bash $USERNAME && \
    echo "$USERNAME:$PASSWORD" | chpasswd && \
    usermod -aG wheel $USERNAME

# Set the root password
RUN echo 'root:asd' | chpasswd

# Configure SSH server
RUN ssh-keygen -A && \
    echo 'PermitRootLogin yes' >> /etc/ssh/sshd_config && \
    echo 'PasswordAuthentication yes' >> /etc/ssh/sshd_config && \
    echo 'PermitEmptyPasswords no' >> /etc/ssh/sshd_config

# Enable and start the SSH service
RUN systemctl enable sshd

# Expose the SSH port
EXPOSE 22

# Set the default command to run SSH server
CMD ["/usr/sbin/sshd", "-D"]
```

## License

This project is licensed under the MIT License.
