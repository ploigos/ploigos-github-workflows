# Ploigos GitHub Workflows

This repository imeplements the [standard Ploigos CI/CD workflows](https://ploigos.github.io/ploigos-docs/#_cicd_process_workflow) using [Github Actions](https://docs.github.com/en/actions). Each standard workflow is impelemented as a [GitHub Actions reusable workflow](https://docs.github.com/en/actions/using-workflows/reusing-workflows). For more information about Ploigos, see the [Ploigos documentation](https://ploigos.github.io/ploigos-docs).

## How to Use this workflow in your Application

1. Create a psr.yaml file in your application repository and supply all needed values.
   * Copy example [psr.yaml](https://github.com/ploigos/spring-petclinic/blob/main/psr.yaml) to your application repository's root directory and update values according to your application. At minimum, the following fields should be updated -
     ```yaml
     ---
     step-runner-config:

     global-defaults:
       organization: ploigos #REPLACE THIS VALUE
       service-name: petclinic #REPLACE THIS VALUE
       application-name: petclinic #REPLACE THIS VALUE

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
         destination-url: ploigos.jfrog.io #REPLACE THIS VALUE
         container-image-push-repository: ploigos/spring-petclinic #REPLACE THIS VALUE
     
     deploy:
     - implementer: ArgoCDDeploy
       config:
         argocd-api: argocd-server.devsecops.svc.cluster.local #REPLACE THIS VALUE
         argocd-skip-tls: True
         deployment-config-repo: https://github.com/ploigos/spring-petclinic-ops.git #REPLACE THIS VALUE
         deployment-config-helm-chart-path: charts/spring-petclinic-deploy #REPLACE THIS VALUE
         deployment-config-helm-chart-values-file-image-tag-yq-path: 'image.tag'
         git-email: 'ploigos+it@redhat.com' #REPLACE THIS VALUE
         argocd-sync-timeout-seconds: 130
         force-push-tags: true
       environment-config:
         DEV:
           deployment-config-helm-chart-environment-values-file: values-DEV.yaml
         TEST:
           deployment-config-helm-chart-environment-values-file: values-TEST.yaml
     
     report:
     - implementer: ResultArtifactsArchive
       config:
       results-archive-destination-url: https://ploigos.jfrog.io/artifactory/results/ #REPLACE THIS VALUE
     ```
2. Add a psr-secrets.yml file that houses all of your secret values. As an example, the spring-petclinic app uses the psr-secrets.yml file within our github-runner helm chart [here](https://github.com/ploigos/openshift-actions-runner-chart/blob/main/templates/external-secrets.yaml). It's being provided secrets from a Hashicorp Vault server using the [External Secrets](https://external-secrets.io/) operator. Below is an example of the rendered kubernetes secret which then is mounted into the github runner pod -
   ```yaml
   step-runner-config:
     config-decryptors:
     - implementer: ObfuscationDefaults
   global-defaults:
     container-registries:
       <registry0-url>:
         username: <your-username>
         password: <your-password>
       <registry1-url>:
         username: <your-username>
         password: <your-password>
   deploy:
   - implementer: ArgoCDDeploy
     config:
       argocd-username: <your-username>
       argocd-password: <your-password>
       git-username: <your-username>
       git-password: <your-password>
   report:
   - implementer: ResultArtifactsArchive
     config:
       results-archive-destination-username: <your-username>
       results-archive-destination-password: <your-password>
   ```
4. Add a Containerfile instruction to your application repository and supply all needed values.
   * Copy example [Containerfile](https://github.com/ploigos/spring-petclinic/blob/main/Containerfile) to your application repository's root directory and update values according to your application.
5. Create a GitOps repo that houses a helm chart to deploy your containerized application.
   * Example GitOps Repo - https://github.com/ploigos/spring-petclinic-ops/
6. Create a Github workflow to reference the minimal pipeline within this repository.
   * Copy example [main.yaml](https://github.com/ploigos/spring-petclinic/blob/main/.github/workflows/main.yaml) to your application repository's .github/workflows/ directory and update values according to your application. At minimum, the following fields should be updated -
     ```yaml
     ---
     name: spring-petclinic #REPLACE THIS VALUE

     on:
       schedule:
       - cron: '0 0 * * *' # every night at 12:00AM UTC
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
