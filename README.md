# flux2-lite
A small repo with the simplest .yaml manifests to test FluxV2 right away on your cluster

## Why?
Whether you are migrating from deprecated [FluxV1](https://github.com/fluxcd/flux) or decided to go GitOps by testing FluxV2, the existing documentation can be intimidating: you may want to quickly test FluxV2 without using the CLI and its default behaviour of bootstraping a new repo containing your Flux setup. 

This repo is here to help you quickly get hands on by providing you the simplest manifests template.

## Prerequisites
- If you are already using a GitOps agent on your Kubernetes cluster, deactivate the reconciliation for the namespace you want to test
- All the entities related to the namespace you are testing are already defining it in their manifest (`metadata: / namespace:`)
- All the entities related to the namespace you are testing are inside a folder
- Make sure that the namespace `flux-system` does not exist in your cluster

## Setup
- kubectl apply the file [install.yaml](/flux-system/install.yaml)
  - it's a copy of the official manifest for Flux [v2.1.1](https://github.com/fluxcd/flux2/releases/tag/v2.1.1)
  - that will create the namespace `flux-system` and setup the 5 basic components of FluxV2 : https://fluxcd.io/flux/components/
-  for your basic test, you only need to use Source and Kustomize controllers
- first configure the Secret [secrets/your-git-user.yaml](/flux-system/secrets/your-git-user.yaml) accordingly
  - kubectl apply this file
- then configure the [sources/your-repo.yaml](/flux-system/sources/your-repo.yaml) manifest with the information about the repo describing your cluster state
  - you can explore easily all available options by installing [Flux CLI](https://fluxcd.io/flux/installation/#install-the-flux-cli) and run `flux create source git --help`
  - the `--explore` option is particularly useful to generate the manifest you want to use
  - kubectl apply this file
- tail the logs of the pod `source-controller` in the `flux-system` namespace and make sure that the pull is working
- now configure the [reconcilers/your-namespace.yaml](/flux-system/reconcilers/your-namespace.yaml) manifest with the information about the folder of the namespace you want to watch with FluxV2 now
  - you can explore easily all available options by installing [Flux CLI](https://fluxcd.io/flux/installation/#install-the-flux-cli) and run `flux create kustomization --help`
  - the `--explore` option is particularly useful to generate the manifest you want to use
  - kubectl apply this file
- tail the logs of the pod `kustomize-controller` in the `flux-system` namespace and make sure that the reconciliation is working
- make a test by commit / pushing a modification in the repo describing your cluster state
- check the reconciliation happening in the logs of the `kustomize-controller` pod in the `flux-system` namespace
- congratulations you are running FLuxV2!

## Bonus : Slack notifications
- TBD

## Going further
- TBD

