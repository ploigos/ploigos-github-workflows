# Ploigos GitHub Workflows

In order to use this workflow for your application, you must do the following -

Create a psr.yaml file in your application repository

Example - ./example/psr.yaml

Add a Containerfile instruction to your application repository

Create a GitOps repo that houses a helm chart to deploy your containerized application

Create a github workflow to reference the minimal pipeline within this repository.

Example - ./example/workflow-main.yaml
