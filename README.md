# Ploigos GitHub Workflows

This repository houses the Ploigos CI/CD workflows defined within a Github Actions [reusable workflow](https://docs.github.com/en/actions/using-workflows/reusing-workflows). Documentation regarding Ploigos can be found at https://ploigos.github.io/ploigos-docs.

## How to Use this workflow in your Application

1. Create a psr.yaml file in your application repository and supply all needed values.
   * Copy example [psr.yaml](https://github.com/ploigos/spring-petclinic/blob/main/psr.yaml) to your application repository's root directory and update values according to your application. At minimum, the following fields should be updated -
     ```yaml
     ---
     step-runner-config:

     global-defaults:
       organization: ploigos << REPLACE THIS VALUE
       service-name: petclinic << REPLACE THIS VALUE
       application-name: petclinic << REPLACE THIS VALUE

     generate-metadata:
     - implementer: Maven
     - implementer: Git
     - implementer: SemanticVersion

     package:
     - implementer: MavenPackage

     create-container-image:
     - implementer: Buildah

     push-container-image:
     - implementer: Skopeo
       config:
         destination-url: quay.io << REPLACE THIS VALUE
         container-image-push-repository: aagreen/spring-petclinic << REPLACE THIS VALUE

     deploy:
     - implementer: ArgoCDDeploy
       config:
         argocd-api: openshift-gitops-server-openshift-gitops.apps.swfocp.sandbox204.opentlc.com << REPLACE THIS VALUE
         argocd-username: admin
         argocd-skip-tls: True
         deployment-config-repo: https://github.com/ploigos/spring-petclinic-ops.git << REPLACE THIS VALUE
         deployment-config-helm-chart-path: charts/spring-petclinic-deploy << REPLACE THIS VALUE
         deployment-config-helm-chart-values-file-image-tag-yq-path: 'image.tag'
         git-email: 'ploigos+it@redhat.com' << REPLACE THIS VALUE
         argocd-sync-timeout-seconds: 130
         force-push-tags: true
       environment-config:
         DEV:
           deployment-config-helm-chart-environment-values-file: values-DEV.yaml
         TEST:
           deployment-config-helm-chart-environment-values-file: values-TEST.yaml
     ```

2. Add a Containerfile instruction to your application repository and supply all needed values.
   * Copy example [Containerfile](https://github.com/ploigos/spring-petclinic/blob/main/Containerfile) to your application repository's root directory and update values according to your application.
3. Create a GitOps repo that houses a helm chart to deploy your containerized application.
   * Example GitOps Repo - https://github.com/ploigos/spring-petclinic-ops/
4. Create a Github workflow to reference the minimal pipeline within this repository.
   * Copy example [main.yaml](https://github.com/ploigos/spring-petclinic/blob/main/.github/workflows/main.yaml) to your application repository's .github/workflows/ directory and update values according to your application. At minimum, the following fields should be updated -
     ```yaml
     ---
     name: spring-petclinic << REPLACE THIS VALUE

     on:
       push:
       pull_request_target:
         types:
         - opened
         - edited
         - synchronize
       workflow_dispatch:
     permissions:
       pull-requests: write

     jobs:
       minimal:
         uses: ploigos/ploigos-github-workflows/.github/workflows/minimal.yaml@main
         secrets: inherit
     ```

Once the above steps are complete, this will trigger a workflow run based on the Ploigos CI/CD Workflow. For more detailed information about the workflow, please refer to the [CI/CD Workflow](https://github.com/ploigos/infra-ops#cicd-workflow). 
