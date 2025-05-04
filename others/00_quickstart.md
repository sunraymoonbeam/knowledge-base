---
title: 0. Quickstart
---
# Table of Contents
1. [Docker](#docker)
   1. [Local Build](#1-build-the-docker-image)
   1. [Kaniko Build](#11-kaniko-build)
   1. [Push](#2-push-the-docker-image)
   1. [Run](#3-run-the-docker-image)
1. [Run:AI](#runai)
   1. [Submit a Run:AI job](#submitting-runai-jobs)
   1. [Job Management](#runai-resource-management)
   1. [Interactive Jobs](#interactive-runai-jobs)


## Docker
---
### 1. Build the Docker image
To build a Docker image, you can use the following command:

```zsh
docker build \
-f ./docker/motextmotion-gpu.Dockerfile \
-t asia-southeast1-docker.pkg.dev/l-q-oasis-aut0/images/docker-test:0.0.1 \
--platform linux/amd64 .
```

### 1.1 Kaniko Build
For development on Coder, we will use the `khull kaniko` wrapper to build the send a job to Run:AI to build the Docker image, and the image will be pushed to the Google Artifact Registry.

To build a docker image using `khull kaniko`, you can use the following command:

```bash
khull kaniko --context $(pwd) \
    --dockerfile $(pwd)/docker/aiap-dsp-mlops-gpu.Dockerfile \
    --destination asia-southeast1-docker.pkg.dev/l-q-oasis-aut0/images/docker-test:0.0.1 \
    --cred-file $GOOGLE_APPLICATION_CREDENTIALS \
    --gcp \
    -v pvc-data:/pvc-data
```
*change the flags to your own configuration*

### 2. Push the Docker image
To push the Docker image to the Google Artifact Registry (GAR), you can use the following commands:

```bash
docker push asia-southeast1-docker.pkg.dev/l-q-oasis-aut0/images/docker-test:0.0.1
```

**Note:** You need to authenticate with the Google Cloud Platform before you can push the image from your local machine. You can follow the guide [here](https://cloud.google.com/artifact-registry/docs/docker/pushing-and-pulling).


### 3. Run the Docker image
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

## Run:AI
---
### Submitting Run:AI jobs
Once we have built the Docker image and pushed it to the Google Artifact Registry, we can send a job to Run:AI to run the image as a container.

To send a job to Run:AI, you can use the following command:

```bash
runai submit \
  --job-name-prefix docker-test-train \
  -i asia-southeast1-docker.pkg.dev/l-q-oasis-aut0/images/docker-test:0.0.1 \
  --working-dir /home/aisg/docker-test \
  --existing-pvc claimname=pvc-data,path=/home/aisg/docker-test/data \
  --cpu 8 \
  --memory 16G \
  --backoff-limit 1 \
  --gpu 0.1 \
  -e MLFLOW_TRACKING_USERNAME=xxxx \
  -e MLFLOW_TRACKING_PASSWORD=xxxx \
  -e OMP_NUM_THREADS=2 \
  --command -- python -u src/train_model.py \
    setup_mlflow=true \
    mlflow_tracking_uri=https://mlflow.l-q-oasis-aut0.aisingapore.net/ \
    mlflow_exp_name=docker-practice \
    mlflow_run_name=docker-first-run \
    dummy_param1=69 \
    dummy_param2=420
```

**Notes:**
- The `--existing-pvc` flag is used to mount a Persistent Volume Claim (PVC) to the job. It is in the format `claimname=pvc-name,path=/path/to/mount`, where `pvc-name` is the name of the PVC (the name is set to this by the MLOps team) and `/path/to/mount` is the path to mount the PVC to in your container.

- Remember to specify `--gpu` if you are using a GPU.

###  **Run:AI Resource Management**

To manage Run:AI resources, you can use the following commands:

| S/N | Command                                     | Description                                                        |
|-----|---------------------------------------------|--------------------------------------------------------------------|
| 1   | `runai list jobs`                          | Display all jobs managed by Run:AI.                               |
| 2   | `runai describe job <job-name>`           | Get detailed information about a specific job.                    |
| 3   | `runai logs job <job-name> -f`            | Check the logs of a specific job and stream logs as they are generated. |
| 4   | `runai suspend job <job-name>`            | Suspend a currently running job.                                  |
| 5   | `runai resume job <job-name>`             | Resume a suspended job.                                          |
| 6   | `runai delete job <job-name>`             | Remove a specific job from Run:AI.                               |


### Interactive Run:AI jobs
To interactively debug a `runai` job, you can need to send a Run:AI Job that allows the container to persist infinitely so that we can enter the container.

```bash
runai submit \
  --job-name-prefix docker-test-train \
  -i asia-southeast1-docker.pkg.dev/l-q-oasis-aut0/images/docker-test:0.0.1 \
  --working-dir /home/aisg/docker-test \
  --existing-pvc claimname=pvc-data,path=/home/aisg/docker-test/data \
  --cpu 8 \
  --memory 16G \
  --backoff-limit 1 \
  --gpu 1 \
  -e MLFLOW_TRACKING_USERNAME=xxxx \
  -e MLFLOW_TRACKING_PASSWORD=xxxx \
  -e OMP_NUM_THREADS=2 \
  --interactive \
  --stdin \
  --tty \
  --command -- sleep infinity # or sleep 3000 (for 3000 seconds)
```

After the job is running, you can use the following command to enter the container:

```bash
runai bash <job-name>
```