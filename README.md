# flux2-lite
A small repo with the simplest .yaml manifests to test Flux2 right away on your cluster

## Why?
Whether you are migrating from deprecated [FluxV1](https://github.com/fluxcd/flux) or decided to go GitOps by testing FLuxV2, the existing documentation can be intimidating: you may want to quickly test FluxV2 without using the CLI, and its default behaviour of bootstraping a new repo containing your Flux setup. This repo is here to help you quickly get hands on by providing you the simplest pattern.

## Prerequisites
- If you are already using a GitOps agent on your Kubernetes cluster, deactivate the reconciliation for the namespace you want to test
- Make sure especially that the namespace `flux-system` does not exist in your cluster

## Setup
- kubectl apply the file install.yaml
  - it's a copy of the official manifest for [v2.1.1](https://github.com/fluxcd/flux2/releases/tag/v2.1.1)
  - that will create the namespace `flux-system` and setup the 5 basic components of FluxV2 : https://fluxcd.io/flux/components/
-  for your basic test, you only need to use source and kusotmize controllers
-  first configure the sources/your-repo.yaml manifest with the information about your repo describing your cluster state
- make sure to prepare the secrets/your-git-user.yaml too
  - apply this secret
- apply the manifest secrets/your-git-user.yaml
- tail the logs of the pod `source-controller` in your `flux-system` namespace and make sure that the pull is working
- configure the reconcilers/your-namespace.yaml manifest with the information about the folder of your namesapce you want to watch with FluxV2 now
- apply the manifest reconcilers/your-namespace.yaml
- tail the logs of the pod `kustomize-controller` in your `flux-system` namespace and make sure that the reconciliation is working
- make a test by commit / pushing in the repo describing your cluster state
- check the reconciliation happening  in the logs of the `kustomize-controller` pod in your `flux-system` namespace
