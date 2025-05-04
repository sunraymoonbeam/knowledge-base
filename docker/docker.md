---
title: "2. Docker"
---
<div style="text-align: center;">
    <img src="https://www.ibexa.co/var/site/storage/images/_aliases/ibexa_content_full/2/2/3/0/70322-1-eng-GB/IMPORT_OlVlfvWhat-is-Docker-and-why-should-I-care.png" style="transform: scale(0.8);">
</div>


# Resources
1. https://docs.docker.com/reference/dockerfile/#entrypoint


# Table of Contents
1. [Docker Build](#docker-build)
    1. [Dockerfile](#dockerfile)
    2. [.dockerignore](#dockerignore)
    3. [Best Building Practices](#best-building-practices)
        1. [Docker Caching](#docker-caching)
        2. [Non-Privileged Access](#non-privileged-access)
2. [Docker Push](#docker-push)
    1. [Image Tags](#image-tags)
3. [Docker Run](#docker-run)
4. [Resource Management](#resource-management)
5. [Interactive Jobs](#interactive-jobs)

## Docker Build
---
To build a Docker image, you can use the following command:

```bash
docker build \
-f docker/motextmotion-gpu.Dockerfile \
-t asia-southeast1-docker.pkg.dev/l-q-oasis-aut0/images/docker-test:0.0.1 \
--platform linux/amd64 .
```
You will need two things to build a Docker image: a Dockerfile and a .dockerignore file. 

### Dockerfile
A Dockerfile is a text document that contains all the commands a user can call on the command line to assemble an image.

<details>
<summary>Sample Dockerfile</summary>

```bash
# Use this if deployed outside RunAI
#FROM nvcr.io/nvidia/cuda:12.1.0-cudnn8-devel-ubuntu20.04
FROM python:3.11-slim

ARG DEBIAN_FRONTEND="noninteractive"

ARG NON_ROOT_USER="aisg"
ARG NON_ROOT_UID="2222"
ARG NON_ROOT_GID="2222"
ARG HOME_DIR="/home/${NON_ROOT_USER}"

ARG REPO_DIR="."

RUN useradd -l -m -s /bin/bash -u ${NON_ROOT_UID} ${NON_ROOT_USER}

RUN apt update && \
    apt -y install curl git gnupg && \
    apt clean

RUN apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/3bf863cc.pub

ENV PYTHONIOENCODING utf8
ENV LANG "C.UTF-8"
ENV LC_ALL "C.UTF-8"
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES compute,utility
ENV LD_LIBRARY_PATH /usr/local/cuda/lib64:$LD_LIBRARY_PATH
ENV PATH "./.local/bin:${PATH}"

USER ${NON_ROOT_USER}
WORKDIR ${HOME_DIR}/motextmotion

# Copy only the requirements file first to leverage Docker cache
COPY --chown=${NON_ROOT_USER}:${NON_ROOT_GID} ${REPO_DIR}/requirements.txt ./requirements.txt

# Install pip requirements
RUN pip install -r requirements.txt

# Copy the rest of the application code
COPY --chown=${NON_ROOT_USER}:${NON_ROOT_GID} ${REPO_DIR} .

# Make the entrypoint script executable
RUN chmod +x ./entrypoint.sh

# Set the entrypoint
ENTRYPOINT ["./entrypoint.sh"]

# Default command
CMD ["python", "src/train_model.py", "dummy_param1=0.3", "dummy_param2=0.4"]
```

</details>


#### .dockerignore
A .dockerignore file is used to specify which files and directories should be ignored by the Docker build process. This helps to reduce the build context sent to the Docker daemon, making the build process more efficient and secure by excluding unnecessary files.

You can use the * and ** patterns to exclude files and directories from being copied into the Docker image.

* The `*.ipynb` pattern matches any .ipynb file in the current directory.

* The `**/*.ipynb` pattern matches any .ipynb file in the current directory and all subdirectories.

* The `**/data/` pattern ignores all `data` folders at any level of the directory hierarchy.

### Best Building Practices

#### Layer Caching
Docker uses a layered cache system to optimize the build process. Each instruction in a Dockerfile creates a new layer in the image, and Docker caches these layers to speed up subsequent builds. If a layer hasn't changed, Docker can reuse the cached version instead of rebuilding it.

To effectively leverage Docker's caching mechanism, you should order your Dockerfile instructions from the least frequently changing to the most frequently changing (probably src). This maximizes the reuse of cached layers.

```bash
COPY --chown=${NON_ROOT_USER}:${NON_ROOT_GID} ${REPO_DIR}/requirements.txt aiap-cv/requirements.txt

RUN pip install -r aiap-cv/requirements.txt

COPY --chown=${NON_ROOT_USER}:${NON_ROOT_GID} ${REPO_DIR} aiap-cv
```

Since the source code changes more frequently, it is placed after the dependency installation to avoid invalidating the cache for the pip install step unnecessarily.

#### Non-privileged Access
When creating a Dockerfile, it's a good practice to create and use a non-root user to run your application. This enhances security by limiting the permissions of the running application. Additionally, you should set the WORKDIR and transfer ownership permissions to the newly created user.

```bash
ARG NON_ROOT_USER="aisg"
ARG NON_ROOT_UID="2222"
ARG NON_ROOT_GID="2222"
ARG HOME_DIR="/home/${NON_ROOT_USER}"

RUN useradd -l -m -s /bin/bash -u ${NON_ROOT_UID} ${NON_ROOT_USER}

# Set the non-root user as the default user
USER ${NON_ROOT_USER}

# Ensure the non-root user has the necessary permissions to access the copied files.
COPY --chown=${NON_ROOT_USER}:${NON_ROOT_GID} ${REPO_DIR} .

# Make the entrypoint script executable
RUN chmod +x ./entrypoint.sh

# Set the entrypoint
ENTRYPOINT ["./entrypoint.sh"]
```


## Docker Push
---
To push a Docker image to a registry, you can use the following command:

```bash
docker push asia-southeast1-docker.pkg.dev/l-q-oasis-aut0/images/docker-test:0.0.1
```

**Note:** You need to authenticate with the Google Cloud Platform before you can push the image from your local machine. You can follow the guide [here](https://cloud.google.com/artifact-registry/docs/docker/pushing-and-pulling).

#### Image tags

In Docker, a tag is used to identify a specific version of a Docker image. Tags are typically used to manage different versions of an image, allowing you to specify which version you want to use. The most common convention for tagging Docker images follows the Semantic Versioning (SemVer) system, which uses a *major.minor.patch* format.'

<div style="text-align: center;">
    <img src="https://miro.medium.com/v2/resize:fit:1400/1*S0S5Qv9AK3mc1M3Vq39nPQ.jpeg" style="transform: scale(0.8);">
</div>


**docker tag** - Renamw docker images.
    ```zsh
    docker tag <old_image_name>:<old_tag> <new_image_name>:<new_tag>
    ```
    - Common flags: None
    - Usage: `docker tag myimage:latest mynewimage:latest` - Rename the image "myimage:latest" to "mynewimage:latest".


## Docker Run
---
To run a Docker container, you can use the following command:
```zsh
docker run \
  --workdir /home/aisg/ \
  -e MLFLOW_TRACKING_USERNAME=xxxx \
  -e MLFLOW_TRACKING_PASSWORD=xxxx \
  asia-southeast1-docker.pkg.dev/l-q-oasis-aut0/images/docker-test:0.0.1 \
  /bin/bash -c "\
   python -u src/train_model.py \
   setup_mlflow=true \
   mlflow_tracking_uri=https://mlflow.l-q-oasis-aut0.aisingapore.net/ \
   mlflow_exp_name=docker-practice \
   mlflow_run_name=docker-first-run \
   dummy_param1=69 \
   dummy_param2=420"
```

In this example, we mount a local directory to a directory inside the container using the `-v` flag. We also set the working directory inside the container using the `--workdir` flag. Environment variables can be passed to the container using the `-e` flag. The image name and tag specify which Docker image to run.


**docker run** - Run Docker images as containers.
   ```bash
   docker run [OPTIONS] <image_name> [COMMAND]
   ```
   - **Common flags**: `-d` (detached mode), `--rm` (auto-remove container on exit), `-v` (mount volumes), `-p` (port mapping), `-e` (set environment variables), `--workdir` (set working directory).
   - Usage 1: `docker run -d <image_name> sleep 3000` - Run the container in the background (detached mode).
   - Usage 2: `docker run --rm <image_name>` - Automatically remove the container when it exits.
   - Usage 3: `docker run -v <host_dir>:<container_dir> <image_name>` - Mount a host directory as a data volume in the container.
   - Usage 4: `docker run -p <host_port>:<container_port> <image_name>` - Map a port on the host to a port in the container.
   - Usage 5 : `docker run -d -p 8080:80 nginx` - Runs the Nginx container in the background, mapping port 8080 on the host to port 80 in the container.
   

## Resource Management
1. **List All Containers** - See all containers, including stopped ones.
   ```bash
   docker ps -a
   ```
   - Common flags: `-a` (show all containers, including stopped ones)
   - Usage: `docker ps` - Lists only running containers.
   - Usage with `-a`: `docker ps -a` - Lists all containers, including those that are stopped.

2. **List All Images** - See all Docker images on your system.
   ```bash
   docker images
   ```
   - Common flags: None
   - Usage: `docker images` - Lists all Docker images available on the system.

3. **Stop a Running Container** - Stop a container that is currently running.
   ```bash
   docker stop <container_id_or_name>
   ```
   - Common flags: None
   - Usage: `docker stop my_container` - Stops the container with the name `my_container`.
   - Usage with container ID: `docker stop abc123` - Stops the container with ID `abc123`.

4. **Remove an Image** - Delete a Docker image from your system.
   ```bash
   docker rmi <image_id_or_name>
   ```
   - Common flags: None
   - Usage: `docker rmi my_image` - Removes the image with the name `my_image`.
   - Usage with image ID: `docker rmi 123abc` - Removes the image with ID `123abc`.

5. **Check Container Resource Usage** - Monitor CPU, memory, and other resource usage.
   ```bash
   docker stats <container_id_or_name>
   ```
   - Common flags: `-a` (show all containers)
   - Usage: `docker stats` - Shows real-time resource usage for running containers.
   - Usage with `-a`: `docker stats -a` - Shows resource usage for all containers, including stopped ones.

6. **Remove All Stopped Containers** - Clean up your system by removing all stopped containers.
   ```bash
   docker container prune
   ```
   - Common flags: None
   - Usage: `docker container prune` - Removes all stopped containers.

7. **Remove All Unused Images** - Clean up your system by removing all unused images.
   ```bash
   docker image prune -a
   ```
   - Common flags: `-a` (remove all unused images, not just dangling ones)
   - Usage: `docker image prune` - Removes only dangling images (unused layers).
   - Usage with `-a`: `docker image prune -a` - Removes all unused images, including untagged ones.


## Interactive Jobs
Here are some useful commands for debugging Docker containers:

1. **Start a Shell in a New Container** - Interactively run a new container and access its shell.
   ```bash
   docker run -it <image_name> /bin/bash
   ```
   - Common flags: `-i` (interactive), `-t` (allocate a pseudo-TTY)
   - Usage: `docker run -it ubuntu /bin/bash` - Starts a new Ubuntu container with an interactive shell.
   - Note: The container stays running as long as the shell session is active. To keep a container running without a shell, you can use commands like `tail -f /dev/null` or `sleep infinity`.

2. **Start a Shell in a Running Container** - Interactively access the shell of a running container.
   ```bash
   docker exec -it <container_id_or_name> /bin/bash
   ```
   - Common flags: `-i` (interactive), `-t` (allocate a pseudo-TTY)
   - Usage: `docker exec -it my_container /bin/bash` - Opens a shell in the running container `my_container`.
   - Usage with command: `docker exec -it my_container ls` - Executes the `ls` command in `my_container`.

3. **View Container Logs** - Check the logs of a running or stopped container.
   ```bash
   docker logs <container_id_or_name>
   ```
   - Common flags: None
   - Usage: `docker logs my_container` - Displays logs for `my_container`.
   - Note: Useful for debugging or monitoring container output.

4. **Inspect Docker Resource** - Get detailed information about a container or image.
   ```bash
   docker inspect <container_id_or_name>
   docker inspect <image_name_or_id>
   ```
   - Common flags: None
   - Usage for container: `docker inspect my_container` - Shows detailed information about `my_container`.
   - Usage for image: `docker inspect ubuntu:latest` - Shows detailed information about the image `ubuntu:latest`.
   - Additional usage: `docker buildx imagetools inspect my_image:tag` - Inspects multi-platform image details for `my_image`.


## Others
### --entrypoint vs --command
The ENTRYPOINT specifies a command that will always be executed when the container starts. The CMD specifies arguments that will be fed to the ENTRYPOINT.

Both CMD and ENTRYPOINT instructions define what command gets executed when running a container.

* Dockerfile should specify at least one of CMD or ENTRYPOINT commands.

* ENTRYPOINT should be defined when using the container as an executable.

* CMD should be used as a way of defining default arguments for an ENTRYPOINT command or for executing an ad-hoc command in a container.

* CMD will be overridden when running the container with alternative arguments.



## Case Study - Qemu, Emulation

#### Multi-architecture images
Multi-architecture support is essential when applications need to run on various hardware platforms, such as x86, ARM, and others.

There are several methods to enable multi-architecture builds:

* **Emulation (QEMU)**: Emulation allows you to build and run applications for different architectures on your current machine. QEMU is a popular emulation tool that is enabled by default in Docker Desktop, which manages the Docker Engine. This method is convenient for development and testing purposes, as it does not require physical hardware for each architecture.

* **Building Specialized Nodes**: For production environments, it is often more efficient to build and deploy applications on specialized nodes that match the target architecture. This approach ensures optimal performance and leverages the full capabilities of the hardware.

* **Cross-Compilation**: Another method is cross-compilation, where the application is compiled on one architecture but targeted to run on another. This technique is useful for creating binaries for different platforms from a single build environment.

* **Building for Different Architectures and Creating a Manifest**: This method involves building separate Docker images for each target architecture and then combining them into a single multi-arch image using a manifest. This includes references to each of its layers, their corresponding sizes, the hash of the image, its size, and also the platform it’s supposed to work on. This manifest can then be referenced by a tag so that it’s easy to find. 

When a user pulls the multi-arch image, Docker automatically selects the appropriate image for the user's architecture. This approach ensures that your application can run efficiently on various platforms without requiring users to specify the architecture manually. Each Docker image is represented by a *manifest*, which is a JSON file containing all the information about a Docker image. 

```bash
docker build \
-t registry.aisingapore.net/aiap16-dsp/low_ren_hwa/aiap-cv:0.0.1 \
--platform linux/amd64,linux/arm64 .
```

```bash
docker build \
-t registry.aisingapore.net/aiap16-dsp/low_ren_hwa/aiap-cv:0.0.1 \
--platform linux/arm64/v8 .
```