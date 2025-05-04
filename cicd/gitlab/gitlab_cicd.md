---
title: 2. Gitlab CI/CD
---

<div style="text-align: center;">
    <img src="https://miro.medium.com/v2/resize:fit:1000/1*25jwbnmYpKdXHS7M5PNIPQ.png" style="transform: scale(0.8);">
</div>

# Resources
1. https://about.gitlab.com/topics/ci-cd/
1. https://www.atlassian.com/continuous-delivery/continuous-integration
1. https://docs.gitlab.com/runner/
1. https://docs.gitlab.com/runner/executors/
1. https://about.gitlab.com/blog/2022/02/03/how-to-keep-up-with-ci-cd-best-practices/
1. https://tinplavec.medium.com/gitlab-ci-cd-best-practices-i-recommend-after-2-years-of-experience-dfc0e349e4d6
1. https://docs.gitlab.com/ee/ci/quick_start/

Credits to byan_teo_cm@aiap.sg for teaching us CI/CD concepts and writing the .gitlab.yml file for us.


# Table of Contents
1. [Quick Start](#quick-start)
1. [Why CI/CD?](#why-cicd)
1. [Continuous Integration](#ci)
1. [Continuous Delivery](#cd)
1. [CI/CD Pipeline](#cicd-pipeline-components)
   1. [Stages](#cicd-stage)
   1. [Jobs](#cicd-stage)
   1. [Caching & Artifacts](#caching)


## Quick start
1. Create a .gitlab-ci.yml. This will specify 2 things: 
* The structure and order of jobs that the runner should execute.
* The decisions the runner should make when specific conditions are encountered.

The pipeline starts and runs the jobs you defined in the .gitlab-ci.yml file.

<details>
<summary>Example of .gitlab-ci.yml </summary>

```bash
default:
  tags:
  - gcp

stages:
- test
- build
- deploy
- doc

test:conda-build:
  stage: test
  image: mambaorg/micromamba:alpine
  cache:
  - key: ${CI_PROJECT_NAME}-pip
    paths:
    - ${PIP_CACHE_DIR}
    policy: pull-push
  - key: ${CI_PROJECT_NAME}-conda
    paths:
    - ${CONDA_DIR}
    policy: push
  only:
    changes:
    - .gitlab-ci.yml
    - dev-requirements.txt
    - docs-requirements.txt
    - motextmotion-conda-env.yaml
    - requirements.txt
  before_script:
  - micromamba env create -f motextmotion-conda-env.yaml -p ${CONDA_DIR} -y
  - eval "$(micromamba shell hook --shell=bash)"
  - micromamba activate ${CONDA_DIR}
  script:
  - export PIP_CACHE_DIR=${PIP_CACHE_DIR}
  - pip install --disable-pip-version-check -r requirements.txt
  - pip install --disable-pip-version-check -r dev-requirements.txt
  - pip install --disable-pip-version-check -r docs-requirements.txt

test:setup:
  stage: test
  image: google/cloud-sdk:slim
  cache:
    key: ${CI_PROJECT_NAME}-pytest-assets
    paths:
    - ./src/tests/assets
    policy: push
  only:
    changes:
    - .gitlab-ci.yml
    - src/tests/*
  before_script:
  - echo "$GCLOUD_SERVICE_KEY" | base64 -d > "/tmp/gcloud-service-key.json"
  - gcloud auth activate-service-account
    --key-file="/tmp/gcloud-service-key.json"
  - gcloud config set project l-q-oasis-aut0
  script:
  - gsutil -m cp gs://l-q-oasis-aut0-general/raw/tests/assets/babel/*
    ./src/tests/assets/babel
  - gsutil -m cp -R gs://l-q-oasis-aut0-general/raw/tests/assets/body_model/
    ./src/tests/assets/humanml3d
  - gsutil -m cp -R gs://l-q-oasis-aut0-general/raw/tests/assets/humanml3d/glove/
    ./src/tests/assets/humanml3d/


test:quality:
  stage: test
  image: continuumio/miniconda3:main
  cache:
  - key: ${CI_PROJECT_NAME}-conda
    paths:
    - ${CONDA_DIR}
    policy: pull
  needs:
  - job: test:conda-build
    optional: true
  before_script:
  - source activate ${CONDA_DIR}
  script:
  - ruff format --check ./src
  - ruff check --extend-select="I" --ignore F401 --ignore F841 ./src
  - pylint --fail-under=1.0 --ignore=tests --disable=W1202 ./src

test:pytest:
  stage: test
  image: mambaorg/micromamba:alpine
  cache:
  - key: ${CI_PROJECT_NAME}-conda
    paths:
    - ${CONDA_DIR}
    policy: pull
  - key: ${CI_PROJECT_NAME}-pytest-assets
    paths:
    - ./src/tests/assets
    policy: pull
  needs:
  - job: test:conda-build
    optional: true
  - job: test:setup
    optional: true
  before_script:
  - export HYDRA_CONFIG_MODE=ci
  - eval "$(micromamba shell hook --shell=bash)"
  - micromamba activate ${CONDA_DIR}
  script:
  - python -m spacy download en_core_web_sm
  - pytest src/tests --junitxml=./rspec.xml
  artifacts:
    paths:
    - rspec.xml
    reports:
      junit: rspec.xml

build:docker:
  stage: build
  image:
    name: gcr.io/kaniko-project/executor:debug
    entrypoint: [ "" ]
  script:
  - /kaniko/executor --context ${CI_PROJECT_DIR} --dockerfile
    $CI_PROJECT_DIR/docker/motextmotion-gpu.Dockerfile --no-push --destination
    asia-southeast1-docker.pkg.dev/l-q-oasis-aut0/images/${CI_PROJECT_NAME}:${CI_COMMIT_SHORT_SHA}
    --tar-path ${CI_PROJECT_NAME}.tar
  only:
  - main
  - develop
  - /^release\/.*$/
  artifacts:
    paths:
    - ${CI_PROJECT_NAME}.tar
    when: on_success

deploy:docker:
  stage: deploy
  image:
    name: gcr.io/go-containerregistry/crane:debug
    entrypoint: [ "" ]
  only:
  - main
  - develop
  - /^release\/.*$/
  needs:
  - build:docker
  script:
  - crane auth login asia-southeast1-docker.pkg.dev -u _json_key_base64 -p
    "$GCLOUD_SERVICE_KEY"
  - crane push ${CI_PROJECT_NAME}.tar
    asia-southeast1-docker.pkg.dev/l-q-oasis-aut0/images/${CI_PROJECT_NAME}:latest
  - crane push ${CI_PROJECT_NAME}.tar
    asia-southeast1-docker.pkg.dev/l-q-oasis-aut0/images/${CI_PROJECT_NAME}:${CI_COMMIT_SHORT_SHA}

deploy:runai:
  stage: deploy
  image: runai/runai-cli:latest
  only:
  - main
  - develop
  - /^release\/.*$/
  needs:
  - deploy:docker
  script:
  - runai login --api-token ${RUNAI_API_TOKEN}
  - runai submit ${CI_PROJECT_NAME}:${CI_COMMIT_SHORT_SHA} -i
    asia-southeast1-docker.pkg.dev/l-q-oasis-aut0/images/${CI_PROJECT_NAME}:latest
    --working-dir /home/aisg --pvc pvc-data:/pvc-data --cpu 2 --memory 4G
    --command "bash entrypoint.sh"
  when: manual

doc:sphinx:
  stage: doc
  image: mambaorg/micromamba:alpine
  cache:
  - key: ${CI_PROJECT_NAME}-conda
    paths:
    - ${CONDA_DIR}
    policy: pull
  needs:
  - job: test:conda-build
    optional: true
  only:
    changes:
    - docs/*
  before_script:
  - eval "$(micromamba shell hook --shell=bash)"
  - micromamba activate ${CONDA_DIR}
  script:
  - sphinx-build -b html docs/ _build/html
  artifacts:
    paths:
    - _build/html

```
</details> 

## Why CICD?
---


## CI
---
Continuous Integration is the practice of **integrating all your code changes into the main branch of a shared source code repository** early and often, automatically testing each change when you commit or merge them, and automatically kicking off a build. With continuous integration, errors and security issues can be identified and fixed more easily, and much earlier in the development process.

By merging changes frequently and triggering automatic testing and validation processes, you minimize the possibility of code conflict, even with multiple developers working on the same application. A secondary advantage is that you don't have to wait long for answers and can, if necessary, fix bugs and security issues while the topic is still fresh in your mind.


## CD
---
Continuous Delivery is a software development practice that works in conjunction with CI to automate the infrastructure provisioning and application release process.

Once code has been tested and built as part of the CI process, CD takes over during the final stages to ensure it's packaged with everything it needs to deploy to any environment at any time. CD can cover everything from provisioning the infrastructure to deploying the application to the testing or production environment.

With CD, the software is built so that it can be deployed to production at any time. Then you can trigger the deployments manually or move to continuous deployment, where deployments are automated as well.


## CICD Pipeline Components
---
CICD pipelines consists of a series of **stages**, which are just a collection of *jobs*.

Stages run in sequence, while all jobs in a stage can run in parallel.

<div style="text-align: center;">
    <img src="https://docs.gitlab.com/ee/ci/runners/img/gitlab-hosted_runners_architecture_v17_0.png" style="transform: scale(0.8);">
</div>

These jobs are executed by runners, which are applications that works with GitLab CI/CD to run jobs in a pipeline. 

The runner is a normal intermediary between Gitlab and an execution environment (executor). We receive a task from Gitlab and send it to execute. An executor is a component of the GitLab Runner that determines the environment in which the job will run (typically docker containers). 

Each job runs in a newly provisioned virtual machine (VM). To ensure isolation, each runner VM is dedicated to a single job and is deleted immediately after the job completes.

A job can perform any task. For example:

```bash
build-job:
  stage: build
  script:
    - echo "Hello, $GITLAB_USER_LOGIN!"
```

In this example, a new runner will be spawned to execute the build-job.

### CICD Stage
---
Stages run in sequence, while the jobs in a stage run in parallel. For example, an early stage could have jobs that lint and compile code, while later stages could have jobs that test and deploy code. If all jobs in a stage succeed, the pipeline moves on to the next stage. If any job in a stage fails, the next stage is not (usually) executed and the pipeline ends early.

```bash
stages:
- test
- build
- deploy
- doc
```

<IMAGE HERE>

The order of the items in stages defines the execution order for jobs:

- Jobs in the same stage run in parallel.
- Jobs in the next stage run after the jobs from the previous stage complete successfully.


### CICD Job
---
The most fundemental element of a CI/CD pipeline. 

| **Keyword**        | **Description**                                                     | **Example**                   |
|--------------------|---------------------------------------------------------------------|---------------------------------------|
| `stage`            | Defines the stage this job will run in.                             | `stage: build`                        |
| `image`            | Specifies the Docker image to use for the job.                      | `image: continuumio/miniconda3:main`  |
| `needs`            | Specifies jobs from previous stages that this job depends on.       | `needs: ["test:conda-build"]`         |
| `before_script`    | Commands to run before the main script.                             | `before_script: - npm install`        |
| `script`           | The main commands to execute in the job.                            | `script: ruff format --check src`     |
| `after_script`     | Commands to run after the main script, regardless of job success.   | `after_script: - echo "Job finished"` |
| `artifacts`        | Defines files and directories to be passed between jobs and stages. | `artifacts: paths: - dist/`           |
| `only`             | Specify the conditions under which a job should run. (e.g., only on certain branches or tags). | `only: - master`                  |
| `when`             | Defines when to run the job (e.g., on success, on failure, always). | `when: on_success`                    |
| `allow_failure`    | Allows the job to fail without failing the pipeline.                | `allow_failure: true`                 |
| `cache`            | Specifies directories or files to cache between pipeline runs.      | `cache: paths: - node_modules/`       |
| `paths`            | Specifies paths for artifacts or caching.                           | `paths: - dist/`                      |


```bash
test:quality:
  stage: test
  image: continuumio/miniconda3:main
  needs:
  - job: test:conda-build
  before_script:
  - source activate ./conda/motextmotion
  - pip install -r dev-requirements.txt
  script:
  - ruff format --check src
  - ruff check --extend-select="I" --ignore F401 --ignore F841 src
  - pylint src --fail-under=1.0 --ignore=tests --disable=W1202
  after_script:
  - echo "Job finished"
  allow_failure: true
  when: on_success
```

* `stage`: Defines the stage this job will run in. In this case, the job runs in the test stage.
* `image`: Specifies the Docker image to use for the job. Here, it uses continuumio/miniconda3:main.
* `needs`: Specifies jobs from previous stages that this job depends on. This job depends on the `test:conda-build` job.
* `before_script`: Commands to run before the main script. Here, it activates a conda environment and installs development requirements.
* `script`: The main commands to execute in the job. This job runs ruff and pylint to check code quality.
* `after_script`: Commands to run after the main script, regardless of the job's success. Here, it echoes "Job finished".
* `allow_failure`: Allows the job to fail without failing the pipeline. This is set to true, meaning the pipeline will continue even if this job fails.
* `when`: Defines when to run the job. on_success means the job will run only if the previous jobs in the pipeline were successful.

### Caching
---
A cache is one or more files a job downloads and saves. Subsequent jobs that use the same cache don’t have to download the files again, so they **execute more quickly**.

The following example demonstrates how the `test:conda-build job` creates a conda environment and caches it. This allows subsequent jobs to reuse the environment without rebuilding it, thereby speeding up the pipeline execution.

```bash
test:conda-build:
  stage: test
  image: mambaorg/micromamba:alpine
  cache:
  - key: ${CI_PROJECT_NAME}-pip
    paths:
    - ${PIP_CACHE_DIR}
    policy: pull-push
  - key: ${CI_PROJECT_NAME}-conda
    paths:
    - ${CONDA_DIR}
    policy: push
```

* `default`: This section defines default settings for all jobs in the pipeline.

* `cache:path`:  Specifies the paths to cache. Here, it caches the `./pip` and the `./conda` directory.

* `cache:key`: A unique key for the cache. Use the cache:key keyword to give each cache a unique identifying key. All jobs that use the same cache key use the same cache, including in different pipelines.


```bash
before_script:
  - micromamba env create -f motextmotion-conda-env.yaml -p ${CONDA_DIR} -y
  - eval "$(micromamba shell hook --shell=bash)"
  - micromamba activate ${CONDA_DIR}
```

The script creates a micromamba environment using the specified YAML file and places it in the `${CONDA_DIR}` directory with the use of the `-p` flag. Then, all subsequent jobs need to do is activate the cached micromamba environment:

```bash
before_script:
  - source activate ${CONDA_DIR}
```


#### Caching vs Artifacts
*Artifacts should be used to permanently make available any files you may need at the end of a pipeline, for example generated binaries, required files for the next stage of the pipeline, coverage reports or maybe even a disk image.*

*But cache should be used to speed up the build process, for example if you compiling a C/C++ binary it usually takes a long time for the first build but subsequent builds are usually faster because it doesn't start from scratch, so if you were to store the temporary files made by the compiler by using cache, it would speed up the compilation across different pipelines.*

- https://stackoverflow.com/questions/46281351/what-is-the-correct-usage-of-cache-artifacts-in-gitlab-ci

This is an example of artifact usage between two stages for continuous deployment in a GitLab CI/CD pipeline. We will use [`kaniko`](https://github.com/GoogleContainerTools/kaniko) for building the docker image and `crane`(https://github.com/google/go-containerregistry/blob/main/cmd/crane/doc/crane.md) for pushing.

##### **First Stage: build:docker** 
Responsible for building a Docker image using Kaniko and storing it as a tar file.

```bash
build:docker:
  stage: build
  image:
    name: gcr.io/kaniko-project/executor:debug
    entrypoint: [ "" ]
  script:
  - /kaniko/executor --context $CI_PROJECT_DIR --dockerfile
    $CI_PROJECT_DIR/docker/motextmotion-gpu.Dockerfile --no-push --destination
    asia-southeast1-docker.pkg.dev/l-q-oasis-aut0/images/$CI_PROJECT_NAME:$CI_COMMIT_SHORT_SHA
    --tar-path $CI_PROJECT_NAME.tar
  only:
  - main
  - develop
  - /^release\/.*$/
  artifacts:
    paths:
    - $CI_PROJECT_NAME.tar
    when: on_success
```

`artifacts`: Defines the files to be passed to subsequent stages. Here, it saves the Docker image tar file ($CI_PROJECT_NAME.tar) as an artifact, which is available when the job succeeds (when: on_success).

##### **Second Stage: deploy:docker** 
Responsible for deploying the Docker image by pushing it to a container registry.

```bash
deploy:docker:
  stage: deploy
  image:
    name: gcr.io/go-containerregistry/crane:debug
    entrypoint: [ "" ]
  only:
  - main
  - develop
  - /^release\/.*$/
  needs:
  - build:docker
  script:
  - crane auth login asia-southeast1-docker.pkg.dev -u _json_key_base64 -p
    "$GCLOUD_SERVICE_KEY"
  - crane push $CI_PROJECT_NAME.tar
    asia-southeast1-docker.pkg.dev/l-q-oasis-aut0/images/$CI_PROJECT_NAME:latest
  - crane push $CI_PROJECT_NAME.tar
    asia-southeast1-docker.pkg.dev/l-q-oasis-aut0/images/$CI_PROJECT_NAME:$CI_COMMIT_SHORT_SHA
```
The second stage (deploy:docker) can access the artifact created in the first stage to push the Docker image to the container registry. 


### Runner vs Executor 
---
**GitLab Runner**

* Definition: GitLab Runner is an application that works with GitLab CI/CD to run jobs in a pipeline.
* Role: It acts as an intermediary between GitLab and the execution environment.
* Function: It receives tasks from GitLab and sends them to be executed. It manages the lifecycle of jobs, including starting, monitoring, and reporting the results back to GitLab.
* Installation: GitLab Runner can be installed on various environments, including physical machines, virtual machines, or cloud instances.


**Executor**:

* Definition: An executor is a component of the GitLab Runner that determines the environment in which the job will run.
* Role: It specifies the mechanism or environment for running the pipeline commands.
* Types: Common executors include:
  * Shell Executor: Runs jobs in the shell of the host machine.
  * Docker Executor: Runs jobs inside Docker containers.
  * Docker Machine Executor: Dynamically creates Docker machines to run jobs.
  * Kubernetes Executor: Runs jobs in a Kubernetes cluster.

* Function: The executor isolates the job execution environment, ensuring that jobs run in a clean and controlled environment.


Key Differences:
| Key Differences | Runner | Executor |
|-----------------|--------|----------|
| **Scope**       | Manages the overall job execution process. | Defines the specific environment and mechanism for job execution. |
| **Isolation**   | Can manage multiple jobs and executors. | Provides isolation for job execution, especially in environments like Docker. |
| **Performance** | Can be configured to use different executors based on the job requirements. | Different executors have different performance characteristics (e.g., Docker executors can run multiple isolated jobs on the same machine, while shell executors typically run one job at a time). |