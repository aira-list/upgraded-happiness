# GitLab CI/CD-pipelines

The follow paragraphs describe how to setup a continuous integration and delivery pipeline in GitLab that consists of various tasks (e.g. build and test) which are executed in an automated fashion.

[GitLab](https://about.gitlab.com/) offers built-in support for continious integration, deployment and delivery. If you add a **[`.gitlab-ci.yml`](https://git.list.lu/help/ci/yaml/_index.md#include)** file to the root directory of your repository and configure your project to use a [Runner](https://git.list.lu/help/ci/yaml/_index.md#include), then each commit or push will trigger a pipeline execution by this Runner.

## Overview

This article illustrates the configuration of a pipeline that performs continuous integration (build) as well as continuous delivery steps (package and deployment) for a Java based application. More concretely, every pushed commit triggers a code build and packages the resulting application into a docker image that is pushed to the LIST Artifactory. Afterwards, the service will be automatically deployed in a [Kubernetes](https://kubernetes.io/) cluster. The following figure illustrates the pipeline workflow.

![CI/CD workflow](/res/images/guides/cicd-pipelines/devops.svg "CI/CD workflow")

All jobs performed by the GitLab Runner are executed in a [Docker](https://www.docker.com/) container. This approach ensures that each build is done in a self-contained, reproducible environment, thereby enhancing consistency, reducing dependency issues, and streamlining the development-to-deployment workflow.

The remaining sections describe the implementation of the different steps executed by the Runner in more detail.

## Using AIDA CI/CD templates

In order to facilitate the creation of CI/CD jobs, we developed a set of [templates](https://git.list.lu/swe/ci-templates) that you can use in your pipeline. The following snippet shows how to import those templates and define the different pipeline stages in a `.gitlab-ci.yaml`.

```yaml
include:
  - project: "SWE/ci-templates"
    file: maven/build.gitlab-ci.yml
    ref: 1.0.1
  - project: "SWE/ci-templates"
    ref: 1.0.1
    file: "docker/build.gitlab-ci.yml"
  - project: "SWE/ci-templates"
    ref: 1.0.1
    file: "helm/deploy.gitlab-ci.yml"

stages:
  - build
  - package
  - deploy
```

## Continuous integration

Continuous integration (CI) is the practice of merging changes as much as possible back into the main branch. The changes are validated by creating each time a build and performing automated tests against the build.

### Building the application

Building the Java application and packaging it into an executable jar is the first step in our CI pipeline. To ensure a clean build we use a compatible docker image of [Apache Maven](https://maven.apache.org/) to build, test & package the code into an executable JAR.

The following YAML snippet show the necessary configuration.

```yaml
maven-build:
  extends: .maven-build
  image: maven:latest
  stage: build
```

The `extends` key defines that our job should be based on the `.maven-build` job specified in the included templates. In the remaining job description we only have to specify the used docker image and link the job to the `build` stage.

## Continuous delivery

Continuous delivery (CD) is an extension of continuous integration to make sure that you can release new changes quickly and in a sustainable way.

### Packaging the application

In this step of the pipeline, the application is wrapped into a docker image that will be pushed to our private [docker repository](https://artefacts.list.lu) managed via Artifactory. For that purpose the `.gitlab-ci.yml` has to be extended like follows.

```yaml
docker-build:
  extends: .docker-build
  variables:
    IMAGE_NAME: artefacts.list.lu/my_project/my_application
```

The variable `IMAGE_NAME` defines the name of the docker image that should be build and afterwards pushed into our private docker repository. The job is based on the included `.docker-build` job, that is linked to the `package` stage, connects to the private docker repository, builds the image, tags it with the current commit hash and pushes it into the private registry defined. Please ensure that you set up a private docker registry via the environment variables `REGISTRY_HOST`, `REGISTRY_USER` and `REGISTRY_PWD` in the CI/CD setting of your GitLab project.

### Deploying the application

In a final step the registered docker image will be fetched and deployed in a target K8s environment. Regarding the `.gitlab-ci.yml` we have to add the following job.

```yaml
helm-deploy:
  extends: .helm-deploy
  variables:
    RELEASE_NAME: my-app-release
    PRIVATE_HELM_ALIAS: list-charts
    CHART_NAME: list-charts/base-chart
    CHART_VERSION: 1.3.0
    CHART_VALUES: .deploy/values.yaml
    NAMESPACE: my-app-namespace
```

The job is based on the included `.helm-deploy` job, which deploys the previously build container on a K8s cluster using a tool called [Helm](https://helm.sh).
The `variables` section defines the required deployment parameters, e.g. the name of the release, an alias for the private helm repo as well as the chart name and version. The path to a `values.yaml` file that is used together with the helm chart to generate the deployment config and finally the target cluster namespace. In this example we use the AIDA `base-chart` for deploying our application. For further information on how to use the `base-chart` see it's [README](https://git.list.lu/aida/swe/helm-charts/base-chart) on GitLab. Please also ensure that the proper Helm chart repository and K8s cluster are configured via the `HELM_REPOSITORY_KEY` and `KUBE_CONFIG` variables in the CI/CD setting of your GitLab project.

For other jobs than the ones mentioned above, please check out the AIDA [ci-templates](https://git.list.lu/swe/ci-templates) on GitLab.
