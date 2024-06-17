# Docker SSH Server Images

This repository contains Dockerfiles for creating Docker images with SSH access for Ubuntu 22.04 and CentOS 7. Each image allows you to create a user with root privileges from environment variables `USERNAME` and `PASSWORD`, and sets the root password to `asd`.

## Prerequisites

- Docker must be installed on your system.

## Building the Images

### Ubuntu 22.04

1. **Build the Docker Image**:
    ```bash
    docker build --build-arg USERNAME=myuser --build-arg PASSWORD=mypassword -t custom_ubuntu_ssh:22.04 -f ubuntu-22.04-Dockerfile .
    ```
    Replace `myuser` and `mypassword` with your desired username and password.

2. **Run the Container**:
    ```bash
    docker run -d -p 2222:22 --name custom_ubuntu_ssh_vm custom_ubuntu_ssh:22.04
    ```

### CentOS 7

1. **Build the Docker Image**:
    ```bash
    docker build --build-arg USERNAME=myuser --build-arg PASSWORD=mypassword -t custom_centos_ssh:7 -f centos7-Dockerfile .
    ```
    Replace `myuser` and `mypassword` with your desired username and password.

2. **Run the Container**:
    ```bash
    docker run -d -p 2222:22 --name custom_centos_ssh_vm custom_centos_ssh:7
    ```

## Accessing the Containers via SSH

You can SSH into the containers using the following command:
```bash
ssh -p 2222 myuser@localhost

