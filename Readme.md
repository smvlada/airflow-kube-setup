# Airflow Kubernetes Setup

> This setup is used and tested in airflow 1.10.10. If you are setting up airflow for the first time use 2.x versions which has lot more features and is stable. It also comes with helm chart

> [VS] UPDATE: upgraded to python 3.7 and airflow 2.3.0 

The setup files are copied directly from airflow's repo and modified to fit the requirements.

One major change is instead of building the docker image from source, we use `pip` to install airflow

## Create Docker Image

```
cd scripts/docker
docker build -t airflow-poc .
```
> Tag image to deploy to GitHub Container Repo
```
# get IMAGE_ID
docker images | grep airflow-poc
#  tag
docker tag IMAGE_ID ghcr.io/USER/airflow-poc:latest
```
> Push the image to GitHub
```
docker push ghcr.io/USER/airflow-poc:latest
```

## Deploy in kubernetes

1. Find $IMAGE in the repository
2. Find $TAG in the repository
2. Change it with the docker image URL and tag respectively

```
cd scripts/
./kube/deploy.sh -d persistent_mode
```
The other supported modes is `git_mode`

## Walkthrough blog

[Deploying airflow on kubernetes](https://bhavaniravi.com/blog/deploying-airflow-on-kubernetes/)
