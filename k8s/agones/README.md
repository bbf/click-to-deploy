# Overview

Agones

For more information on Agones, see the [Agones website](https://agones.dev/).

## About Google Click to Deploy

Popular open source software stacks on Kubernetes packaged by Google and made available in Google Cloud Marketplace.

# Installation

## Quick install with Google Cloud Marketplace

Get up and running with a few clicks! Install Agones to a Google Kubernetes Engine cluster using Google Cloud Marketplace.

### Prerequisites
To install Agones, make sure your user has cluster-admin access:

#### Kubernetes Engine
kubectl create clusterrolebinding cluster-admin-binding \
  --clusterrole cluster-admin --user `gcloud config get-value account`


Follow the [on-screen instructions](https://console.cloud.google.com/marketplace/details/google/agones).




================ OLD BELOW HERE ================

## Command line instructions

### Prerequisites

#### Set up command line tools

You'll need the following tools in your environment:

- [gcloud](https://cloud.google.com/sdk/gcloud/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
- [docker](https://docs.docker.com/install/)
- [openssl](https://www.openssl.org/)

Configure `gcloud` as a Docker credential helper:

```shell
gcloud auth configure-docker
```

#### Create a Google Kubernetes Engine cluster

Create a cluster from the command line. If you already have a cluster that
you want to use, this step is optional.

```shell
export CLUSTER=sample-app-cluster
export ZONE=us-west1-a

gcloud container clusters create "$CLUSTER" --zone "$ZONE"
```

#### Configure kubectl to connect to the cluster

```shell
gcloud container clusters get-credentials "$CLUSTER" --zone "$ZONE"
```

#### Clone this repo

Clone this repo and the associated tools repo:

```shell
git clone --recursive https://github.com/GoogleCloudPlatform/click-to-deploy.git
```

#### Install the Application resource definition

An Application resource is a collection of individual Kubernetes components,
such as Services, Deployments, and so on, that you can manage as a group.

To set up your cluster to understand Application resources, run the following command:

```shell
kubectl apply -f "https://raw.githubusercontent.com/GoogleCloudPlatform/marketplace-k8s-app-tools/master/crd/app-crd.yaml"
```

You need to run this command once for each cluster.

The Application resource is defined by the
[Kubernetes SIG-apps](https://github.com/kubernetes/community/tree/master/sig-apps) community. The source code can be found on
[github.com/kubernetes-sigs/application](https://github.com/kubernetes-sigs/application).

### Install the Application

Navigate to the `sample-app` directory:

```shell
cd click-to-deploy/k8s/sample-app
```

#### Configure the app with environment variables

Choose an instance name and
[namespace](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)
for the app. In most cases, you can use the `default` namespace.

```shell
export APP_INSTANCE_NAME=sample-app-1
export NAMESPACE=default
```

Set the sample parameter:

```shell
export SAMPLE_APP_PARAMETER1=3
```

Configure the container image:

```shell
export IMAGE_SAMPLE_APP="marketplace.gcr.io/google/nginx1:latest"
```

The image above is referenced by
[tag](https://docs.docker.com/engine/reference/commandline/tag). We recommend
that you pin each image to an immutable
[content digest](https://docs.docker.com/registry/spec/api/#content-digests).
This ensures that the installed application always uses the same images,
until you are ready to upgrade. To get the digest for the image, use the
following script:

```shell
export IMAGE_SAMPLE_APP=$(docker pull $IMAGE_SAMPLE_APP | awk -F: "/^Digest:/ {print gensub(\":.*$\", \"\", 1, \"$IMAGE_SAMPLE_APP\")\"@sha256:\"\$3}")
```

#### Expand the manifest template

Use `envsubst` to expand the template. We recommend that you save the
expanded manifest file for future updates to the application.

```shell
awk 'FNR==1 {print "---"}{print}' manifest/* \
  | envsubst '$APP_INSTANCE_NAME $NAMESPACE $IMAGE_SAMPLE_APP $SAMPLE_APP_PARAMETER1' \
  > "${APP_INSTANCE_NAME}_manifest.yaml"
```

#### Apply the manifest to your Kubernetes cluster

Use `kubectl` to apply the manifest to your Kubernetes cluster.

```shell
kubectl apply -f "${APP_INSTANCE_NAME}_manifest.yaml" --namespace "${NAMESPACE}"
```

#### View the app in the Google Cloud Console

To get the Console URL for your app, run the following command:

```shell
echo "https://console.cloud.google.com/kubernetes/application/${ZONE}/${CLUSTER}/${NAMESPACE}/${APP_INSTANCE_NAME}"
```

To view the app, open the URL in your browser.

# Using the app

## How to use Agones

How to use Sample App

## Follow the on-screen steps

To set Sample App, follow these on-screen steps to customize your installation:

* Do something

# Scaling

How to scale Agones

# Backup and restore

How to backup and restore Agones

## Backing up Agones

## Restoring your data

# Updating

# Logging and Monitoring

