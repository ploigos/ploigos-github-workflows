# Ploigos GitHub Workflows

This repository houses the Ploigos workflows defined within a Github Actions [reusable workflow](https://docs.github.com/en/actions/using-workflows/reusing-workflows). For ease of use, there are templates that can be used within the examples directory. Please replace all values that begin with `REPLACE_`.

## Setup Instructions

1. Create a psr.yaml file in your application repository and supply all needed values.
   * `cp ./examples/psr.yaml <application repository>/.`
   * `find & replace all REPLACE_ values`
2. Add a Containerfile instruction to your application repository and supply all needed values.
    * `cp ./examples/Containerfile <application repository>/.`
    * `find & replace all REPLACE_ values`
3. Create a GitOps repo that houses a helm chart to deploy your containerized application.
    * `cp -r ./examples/gitops/* <application gitops repository>/.`
    * `find & replace all REPLACE_ values`
    * Commit Application GitOps repository to Github
4. Create a github workflow to reference the minimal pipeline within this repository.
    * `cp ./examples/workflow-main.yaml <application repository>/.github/workflows/main.yaml`
    * `find & replace all REPLACE_ values`
    * Commit all changes to Application repository to Github

Once setup is complete, this will trigger a workflow run based on the Ploigos CI/CD Workflow. For more detailed information about the workflow, please refer to the [CI/CD Workflow](https://github.com/ploigos/infra-ops#cicd-workflow). 
