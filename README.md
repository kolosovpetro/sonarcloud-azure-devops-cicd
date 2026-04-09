# SonarCloud Scan Automation in Azure DevOps CI/CD

## Overview

This document describes how to configure automated static code analysis using SonarCloud within an Azure DevOps CI/CD pipeline. The goal is to run SonarCloud scans automatically during the build process and publish the analysis results to the SonarCloud dashboard.

## Architecture

The integration workflow is:

1. Azure DevOps pipeline builds the application.
2. SonarCloud scanner analyzes the source code during the pipeline.
3. Results are uploaded to SonarCloud.
4. SonarCloud dashboard displays code quality metrics and issues.

Pipeline flow:

Repository → Azure DevOps Pipeline → SonarCloud Analysis → SonarCloud Dashboard

## Prerequisites

Before configuring the pipeline ensure the following:

- Azure DevOps project exists
- GitHub repository exists
- SonarCloud account is created
- SonarCloud organization is created
- Azure DevOps permissions allow creating service connections

## Configuration Steps

### 1. Install SonarCloud Azure DevOps Extension

Install the SonarCloud extension from the Azure DevOps marketplace:

https://marketplace.visualstudio.com/items?itemName=SonarSource.sonarcloud

This extension adds SonarCloud tasks that can be used inside Azure DevOps pipelines.

### 2. Create GitHub Service Connection

Azure DevOps must be able to clone the GitHub repository.

Steps:

1. Open Azure DevOps Project Settings
2. Navigate to Service Connections
3. Create a new service connection
4. Select GitHub
5. Authenticate and authorize access

This allows the pipeline to fetch the repository source code.

### 3. Create SonarCloud Organization and Project

In SonarCloud:

1. Create a new organization
2. Create a new project inside the organization
3. Select manual project configuration

During this step SonarCloud will generate configuration values required for the pipeline.

### 4. Obtain SonarCloud Configuration Values

When selecting manual analysis in SonarCloud, the platform displays a command-line example containing required parameters.

Copy the following values:

- Organization
- Project name
- Project key
- SonarCloud token

These values will be used when configuring Azure DevOps.

### 5. Create SonarCloud Service Connection in Azure DevOps

Steps:

1. Navigate to Azure DevOps Project Settings
2. Open Service Connections
3. Create a new connection
4. Select SonarCloud
5. Provide the SonarCloud token obtained in the previous step
6. Authorize the connection for pipelines

This service connection allows Azure DevOps pipelines to publish scan results to SonarCloud.

### 6. Implement YAML Pipeline

The Azure DevOps pipeline must include SonarCloud tasks in the following order:

1. SonarCloudPrepare
2. Build
3. SonarCloudAnalyze
4. SonarCloudPublish

Example pipeline:

```yaml
trigger:
  branches:
    include:
      - master
      - develop

pool:
  vmImage: ubuntu-latest

steps:

- task: SonarCloudPrepare@4
  inputs:
    SonarCloud: 'sonarcloud-service-connection'
    organization: 'kolosovpetro'
    scannerMode: 'MSBuild'
    projectKey: 'sonarcloud-azure-devops-cicd'
    projectName: 'sonarcloud-azure-devops-cicd'

- task: DotNetCoreCLI@2
  displayName: Build project
  inputs:
    command: build
    projects: '**/*.sln'

- task: SonarCloudAnalyze@4

- task: SonarCloudPublish@4
  inputs:
    pollingTimeoutSec: '300'
```

### 7. Run the Pipeline

Once the pipeline is committed to the repository:

1. Push the YAML file
2. Run the Azure DevOps pipeline
3. Wait for the build and analysis to complete
4. Open the SonarCloud dashboard to view the results

## Result

After a successful pipeline execution:

- Source code is analyzed automatically during CI
- Code quality metrics appear in SonarCloud
- Security vulnerabilities, bugs, and code smells are reported
- Quality gates can be enforced in future pipelines

## Benefits

Automating SonarCloud analysis in CI/CD provides:

- Continuous code quality monitoring
- Early detection of bugs and vulnerabilities
- Automated enforcement of quality gates
- Integration with pull request workflows
- Centralized code quality reporting

## Repositories

GitHub repository:

https://github.com/kolosovpetro/sonarcloud-azure-devops-cicd

Azure DevOps repository:

https://dev.azure.com/PetroKolosovProjects/sonarcloud-azure-devops-cicd

SonarCloud projects:

https://sonarcloud.io/organizations/kolosovpetro/projects

## Documentation References

Azure DevOps Extension:

https://marketplace.visualstudio.com/items?itemName=SonarSource.sonarcloud&ssr=false#overview

SonarCloud Azure DevOps documentation:

https://docs.sonarsource.com/sonarcloud/devops-platform-integration/azure-devops-integration/

Example YAML pipelines:

https://github.com/SonarSource/sonar-scanner-azdo/tree/master/its/fixtures

## Useful Links

SonarCloud organization dashboard:

https://sonarcloud.io/organizations/kolosovpetro/projects

GitHub repository:

https://github.com/kolosovpetro/sonarcloud-azure-devops-cicd

Azure DevOps project:

https://dev.azure.com/PetroKolosovProjects/sonarcloud-azure-devops-cicd