# Airflow Kubernetes Setup

> This setup is used and tested in airflow 1.10.10. If you are setting up airflow for the first time use 2.x versions which has lot more features and is stable. It also comes with helm chart

> [VS] UPDATE: upgraded to python 3.7 and airflow 2.3.0 

The setup files are copied directly from airflow's repo and modified to fit the requirements.

One major change is instead of building the docker image from source, we use `pip` to install airflow

## K8S setup
### Prep GitHub local env
> You need to export GitHub access token PAT to $GH_SERVICE_ACCOUNT. You will need to export your GitHub user to $GH_USER.
> It is recommended to create separate GH access token for serviceaccount with  
```
kubectl create secret docker-registry ghcr-service-account --docker-server=ghcr.io --docker-username=$GH_USER --docker-password=$GH_SERVICE_ACCOUNT
```
# Steps to build & deploy new image

## Create Docker Image

```
cd scripts/docker
docker build -t airflow .
```
> Tag image to deploy to GitHub Container Repo (ghcr.io)
```
# get IMAGE_ID
docker images | grep airflow
#  tag
docker tag IMAGE_ID ghcr.io/$GH_USER/airflow:2.3.0
```
> Login to ghcr.io
```
 echo $GH_SERVICE_ACCOUNT | docker login ghcr.io -u $GH_USER --password-stdin
```
> Push the image to ghcr.io
```
docker push ghcr.io/$GH_USER/airflow:2.3.0
```
> Check details of image on ghcr.io
```
# first you need to export your GitHub token to $GH_SERVICE_ACCOUNT and then:
 docker run --rm quay.io/skopeo/stable list-tags --creds=$GH_USER:$GH_SERVICE_ACCOUNT docker://ghcr.io/$GH_USER/airflow
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
