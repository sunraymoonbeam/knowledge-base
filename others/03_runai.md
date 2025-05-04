---
title: "3. RunAI"
---
<div style="text-align: center;">
    <img src="https://cdn.prod.website-files.com/61e95e54543a7c0a5967ffd1/636aad25e8103d62c6fc1281_RunAI-featured.png" style="transform: scale(0.8);">
</div>


# Resources
1. https://docs.run.ai/v2.13/Researcher/Walkthroughs/quickstart-overview/


# Table of Contents
1. [Submitting Run:AI jobs](#submitting-runai-jobs)
1. [Resource Management](#resource-management)
   1. [Job Management](#job-management)
   1. [Node Management](#node-management)
1. [Interactive Jobs](#interactive-jobs)


## Submitting Run:AI jobs
---
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


## **Resource Management**
---

### **Job Management**

| S/N | Command                                     | Description                                                        |
|-----|---------------------------------------------|--------------------------------------------------------------------|
| 1   | `runai list jobs`                          | Display all jobs managed by Run:AI.                               |
| 2   | `runai describe job <job-name>`           | Get detailed information about a specific job.                    |
| 3   | `runai logs job <job-name> -f`            | Check the logs of a specific job and stream logs as they are generated. |
| 4   | `runai suspend job <job-name>`            | Suspend a currently running job.                                  |
| 5   | `runai resume job <job-name>`             | Resume a suspended job.                                          |
| 6   | `runai delete job <job-name>`             | Remove a specific job from Run:AI.                               |


### **Node Management**
A node in Run:AI is a physical or virtual machine that is part of the Run:AI cluster. Nodes provide the computational resources needed to run jobs. Each node runs one or more pods, which are the smallest deployable units in Kubernetes.

| S/N | Command                                     | Description                                                         |
|-----|---------------------------------------------|---------------------------------------------------------------------|
| 1   | `runai list nodes`                          | Display all nodes in the Run:AI cluster.                            |
| 2   | `runai describe node <node-name>`           | Get detailed information about a specific node.                     |

Example of listing nodes:
```bash
runai list nodes
```

Output:
```bash
                                                                                       │ GPU  
NAME                                          STATUS               IP Address  ROLE    │ TYPE
────                                          ──────               ──────────  ────    │ ────
gke-runai-100e-cluster-cpu-np0-9c7e1192-5jbw  SysctlChanged,Ready  10.0.0.54   <none>  │ -
gke-runai-100e-cluster-cpu-np0-9c7e1192-inrp  SysctlChanged,Ready  10.0.0.53   <none>  │ -
gke-runai-100e-cluster-gpu-np0-e5f3c3cf-r6hh  SysctlChanged,Ready  10.0.0.55   <none>  │ NVIDIA-A100-SXM4-40GB
```

You will notice that this gives the same output as ```kubectl get nodes```. Why? TBC

Example of describing a node:
```bash
runai describe node gke-runai-100e-cluster-gpu-np0-e5f3c3cf-r6hh
```
Output:
```bash
NAME:        gke-runai-100e-cluster-gpu-np0-e5f3c3cf-r6hh
STATUS:      SysctlChanged,Ready
IP Address:  10.0.0.55
ROLE:        <none>
CPU:
  CAPACITY:  12
MEMORY:
  CAPACITY:  83.5 GiB
GPU:
  TYPE:       NVIDIA-A100-SXM4-40GB
  CAPACITY:   1
  UNHEALTHY:  0
```

## Interactive Jobs
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
```                                    |