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
When a issue in happening during the repo pull or the reconciliation of a namespace folder, you can be easily notified on Slack like this (using the [Notification controller](https://fluxcd.io/flux/components/notification/)):
- create a new Slack app on your workspace and configure the Secret [secrets/your-slack-app-token.yaml](/flux-system/secrets/your-slack-app-token.yaml) accordingly
  - kubectl apply this file
- put the Slack channel name you want to use in [notifications/your-slack-channel.yaml](/flux-system/notifications/your-slack-channel.yaml)
  - kubectl apply this file
- customize the file [notifications/alerts.yaml](/flux-system/notifications/alerts.yaml) with YOUR_CLUSTER_NAME
  - you can explore easily all available options by installing [Flux CLI](https://fluxcd.io/flux/installation/#install-the-flux-cli) and run `flux create alert --help`
  - the `--explore` option is particularly useful to generate the manifest you want to use
  - kubectl apply this file
- tail the logs of the pod `notification-controller` in the `flux-system` namespace
- make a test by commit / pushing a modification with a typo in the repo describing your cluster state
- make sure the alert is detected in the logs and that your Slack channel is updated with this error message
- commit / push the typo fix
- you are all set now!

## Going further
- Documentation about the GitOps Toolkit components Flux V2 is using : https://fluxcd.io/flux/components/
- _Migrating from Flux v1 to v2 with Leigh Capili_ : https://www.youtube.com/watch?v=vwvTwLQhXVI
  - excellent walkthrough

