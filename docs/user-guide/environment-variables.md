---
layout: main
title: Environment Variables
category: User Guide
menu: menu
toc:
    - title: Environment Variables
      url: "#environment-variables"
      active: true
    - title: Build Specific
      url: "#build-specific"
    - title: General
      subitem: true
      url: "#general"
    - title: User configurable
      url: "#user-configurable"
      subitem: true
    - title: Plugins
      url: "#plugins"
    - title: Coverage (Sonar)
      url: "#coverage-sonar"
      subitem: true
    - title: Directories
      url: "#directories"
    - title: Environment Variables
      url: "#environment-variables-1"
    - title: Source Code
      url: "#source-code"
    - title: URLs
      url: "#urls"
    - title: Continuous Integration
      url: "#continuous-integration"
---
# Environment Variables

Screwdriver exports a set of environment variables that you can rely on during build runtime.

_Note: Environment variables set in one job cannot be accessed in another job. To pass variables between jobs, use [metadata](./metadata)._

## Build Specific

### General

| Name | Description |
|------|-------|
| SD_BUILD_ID | The ID of the [build](../about/appendix/domain#build) |
| SD_EVENT_ID | The ID of the [event](../about/appendix/domain#event) |
| SD_JOB_ID | The ID of the [job](../about/appendix/domain#job) |
| SD_JOB_NAME | Job name (e.g.: `main`) |
| SD_PARENT_BUILD_ID | Bracketed list of build(s) that trigger(s) this build. (e.g.: `[12345 23456]`) |
| SD_PARENT_EVENT_ID | ID of the parent event if this was a restart |
| SD_PR_PARENT_JOB_ID | ID of the real job of PR. For example, in `PR-1:main` build, this ENV references the ID of `main` job |
| SD_PIPELINE_ID | The ID of the [pipeline](../about/appendix/domain#pipeline) |
| SD_PIPELINE_NAME | The name of the pipeline (e.g.: `d2lam/myPipeline`) |
| SD_PRIVATE_PIPELINE | Whether the pipeline is private(true) or not(false) |
| SD_PULL_REQUEST | Pull Request number (e.g.: `1`; blank if non-PR) |
| SD_STEP_EXIT_CODE | Exit code from previously executed steps, only available at teardown steps (e.g.: `0` if all previous steps have passed, otherwise, the latest non-zero exit code) |
| SD_STEP_NAME | Current step name, only available for user-defined steps, not setup or teardown commands (e.g.: during teardown, would be set to the last successfully executed step) |
| SD_TEMPLATE_FULLNAME | Full template name the job is using (e.g.: `d2lam/myTemplate`; blank if not using template) |
| SD_TEMPLATE_NAME | Name of the template the job is using (e.g.: `myTemplate`; blank if not using template) |
| SD_TEMPLATE_NAMESPACE | Namespace of the template the job is using (e.g.: `d2lam`; blank if not using template) |
| SD_TEMPLATE_VERSION | Version of the template the job is using (blank if not using template) |
| SD_BUILD_CLUSTER_NAME | The name of the [build cluster](../cluster-management/configure-buildcluster-queue-worker) |
| SD_TOKEN | JWT token for the build |
| SD_SCHEDULED_BUILD | Whether the build is triggered by scheduler(true) or not(false) |
| SD_DIND_SHARE_PATH | Path to shared directory between build container and DinD container when Docker-in-Docker feature is enabled |
| CONTAINER_IMAGE | Build container image. Available when Kubernetes is used as build [executor](../cluster-management/configure-api#executor-plugin) |
| CONTAINER_CPU_LIMIT | Value of CPU allocated to build container |
| CONTAINER_MEMORY_LIMIT | Value of memory allocated to build container |

### User configurable

| Name | Default Value | Description |
|------|---------------|-------------|
| SD_ZIP_ARTIFACTS | false | **Options:** (`true`/`false`) <br><br>Compresses and uploads artifacts in a single ZIP file.<br><br>**Use case:** Reduces upload time when your build has a lot of artifacts. If the upload fails, it's likely that the zip is too larger to handle.<br><br>**Note:** Consult with your cluster admin to see if this option is available. |
| USER_SHELL_BIN | sh | The user shell bin to run the build in. Can also be the full path such as `/bin/bash`. Example repo: <https://github.com/screwdriver-cd-test/user-shell-example> |
| GIT_RECURSIVE_CLONE | true | **Options:** (`true`/`false`) <br><br>Clones submodule repositories recursively. |
| GIT_SHALLOW_CLONE | true | **Options:** (`true`/`false`) <br><br>Shallow clones source repository. |
| GIT_SHALLOW_CLONE_DEPTH | 50 | Shallow clone with a history truncated to the specified number of commits |
| GIT_SHALLOW_CLONE_SINCE |  | Shallow clone with a history truncated starting from the specified datetime (inclusive). If set, this has priority over `GIT_SHALLOW_CLONE_DEPTH`.<br><br>This uses `--shallow-since` which accepts both absolute dates (e.g.: `2019-04-01`) and relative dates (e.g.: `4 weeks ago`). |
| GIT_SHALLOW_CLONE_SINGLE_BRANCH |  | If set to `true`, it will use `--single-branch` option for shallow clone. Otherwise, `--no-single-branch` option is used. |
| GIT_SPARSE_CHECKOUT_PATH |  | If this env is set, screwdriver does sparse checkout source repository with given pattern. When users want to configure multiple directory, users define multiple directories separate with space (e.g.: `dir1 dir2`). <br><br>This uses `--no-checkout` option for clone and only checkouts defined directory. |
| SD_SKIP_REPOSITORY_CLONE |  | If set to `true`, it will skip the repository clone. |
| SD_COVERAGE_PLUGIN_ENABLED  | `true` |If set to string `false`, step `sd-teardown-screwdriver-coverage-bookend` will be skipped. |

## Plugins

These environment variables may or may not be available depending on what plugins are installed.

#### Coverage (Sonar)

| Name | Description |
|------|-------|
| SD_SONAR_AUTH_URL | Screwdriver API authentication URL that will return a Sonar access token |
| SD_SONAR_HOST | Sonar host URL |
| SD_SONAR_ENTERPRISE | Whether using Enterprise (true) or open source edition of SonarQube(false) |
| SD_SONAR_PROJECT_KEY | Sonar project key (e.g.: `pipeline:123` or `job:456`) |
| SD_SONAR_PROJECT_NAME | Sonar project name (e.g.: `d2lam/myPipeline` or `d2lam/myPipeline:main`) |
| SD_ALLOW_PRIVATE_COVERAGE_SEND | If set to the string `true`, private pipelines will also have the ability to send coverage data |

## Directories

| Name | Description |
|------|-------|
| SD_ARTIFACTS_DIR | Location of built/generated files. <br><br>**Note**: The `sd-teardown-screwdriver-artifact-bookend` step uploads artifacts from this directory into the Store unless the build is `ABORTED`. |
| SD_META_DIR | Location of the [metadata](./metadata) directory |
| SD_META_PATH | Location of the [metadata](./metadata) file |
| SD_ROOT_DIR | Location of workspace (e.g.: `/sd/workspace`) |
| SD_SOURCE_DIR | Location of checked-out code (e.g.: `sd/workspace/src/github.com/d2lam/myPipeline`) |
| SD_SOURCE_PATH | Location of source path which triggered current build. See [Source Paths](./configuration/sourcePaths). |
| SD_CONFIG_DIR | Location of parent pipeline's repository (only set for [child pipelines](./configuration/externalConfig)) (e.g.: `sd/workspace/config`) |

## Environment Variables

| Name | Description |
|------|-------|
| &lt;environment_variable&gt; | Environment variable specified under the "environment" section in your [screwdriver.yaml](configuration/) |

Please be aware if you are using dot notations in the environment variables, like:

```
environment:
   REGION.INSTANCE: 'xyz'
```

Then `process.env.REGION.INSTANCE` won't work, and you must use `process.env['REGION.INSTANCE']` dot notation to access as well.

## Source Code

| Name | Description |
|------|-------|
| SCM_URL | SCM URL that was checked out (e.g.: `https://github.com/d2lam/myPipeline`) |
| GIT_URL | SCM URL that was checked out with .git appended (e.g.: `https://github.com/d2lam/myPipeline.git`) |
| CONFIG_URL | SCM URL of the parent pipeline repository (only set for [child pipelines](./configuration/externalConfig)) |
| GIT_BRANCH | Reference for PR or the branch (e.g.: `origin/refs/${PRREF}` or `origin/${BRANCH}`) |
| PR_BASE_BRANCH_NAME | Base branch name the PR is opened against (e.g.: `${BRANCH}`) |
| PR_BRANCH_NAME | Branch name of the PR (e.g.: `origin/${BRANCH}` or `upstream/${BRANCH}`) |
| SD_BUILD_SHA | The Git commit SHA (e.g.: `b5a94cdabf23b21303a0e6d5be5e96bd6300847a`) |

## URLs

| Name | Description |
|------|-------|
| SD_API_URL | Link to the Screwdriver API URL (e.g.: `https://api.screwdriver.cd/v4/`) |
| SD_BUILD_URL | Link to the Screwdriver build API URL (e.g.: `https://api.screwdriver.cd/v4/builds/1`) |
| SD_STORE_URL | Link to the Screwdriver Store URL (e.g.: `https://store.screwdriver.cd/v1/`) |
| SD_UI_URL | Link to the Screwdriver UI URL (e.g.: `https://cd.screwdriver.cd/`) |
| SD_UI_BUILD_URL | Link to the Screwdriver UI build URL (e.g.: `https://cd.screwdriver.cd/pipelines/259/builds/173`) |

## Continuous Integration

| Name | Description |
|------|-------|
| CI | `true` |
| CONTINUOUS_INTEGRATION | `true` |
| SCREWDRIVER | `true` |
