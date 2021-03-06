# Kaniko

This build template builds source into a container image using Google's
[`kaniko`](https://github.com/GoogleCloudPlatform/kaniko) tool.

>kaniko doesn't depend on a Docker daemon and executes each command within a
>Dockerfile completely in userspace.  This enables building container images in
>environments that can't easily or securely run a Docker daemon, such as a
>standard Kubernetes cluster.
> - [Kaniko website](https://github.com/GoogleCloudPlatform/kaniko)

kaniko is meant to be run as an image, `gcr.io/kaniko-project/executor`. This
makes it a perfect tool to be part of Tekton.

## Install the Task

```
kubectl apply -f https://raw.githubusercontent.com/tektoncd/catalog/master/kaniko/kaniko.yaml
```

## Inputs

### Parameters

* **IMAGE**: The Docker image name to apply to the newly built image.
  (_required_)
* **DOCKERFILE**: The path to the `Dockerfile` to execute (_default:_
  `./Dockerfile`)

### Resources

* **source**: A `git`-type `PipelineResource` specifying the location of the
  source to build.

## ServiceAccount

kaniko builds an image and pushes it to the destination defined as a parameter.
In order to properly authenticate to the remote container registry, it needs to
have the proper credentials. This is achieved using a `ServiceAccount`.

For an example on how to create such a `ServiceAccount` to push an image to
DockerHub, see the
[Authentication](https://github.com/tektoncd/pipeline/blob/master/docs/auth.md#basic-authentication-docker)
documentation page.

## Usage

This TaskRun runs the Task to fetch a Git repo, and build and push a container
image using Kaniko

```
apiVersion: tekton.dev/v1alpha1
kind: TaskRun
metadata:
  name: example-run
spec:
  taskRef:
    name: kaniko
  inputs:
    params:
    - name: IMAGE
      value: gcr.io/my-repo/my-image
    resources:
    - name: source
      resourceSpec:
        type: git
        params:
        - name: url
          value: https://github.com/my-user/my-repo
```
