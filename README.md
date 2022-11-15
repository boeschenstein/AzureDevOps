# Azure DevOps

## First Example - simple Angular app

From https://dev.to/mcklmt/build-and-deploy-angular-app-with-azure-devops-3nnf

Sign-in to Azure Pipelines, choose "Start free with GitHub"

https://docs.microsoft.com/en-us/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops

Create Project

https://docs.microsoft.com/en-us/azure/devops/organizations/projects/create-project?view=azure-devops&tabs=preview-page

Make sure you have a 'Repo' available in the project.

Create app

`ng new HelloWorld --strict false --routing false --style css`

Test app (`ng serve`) and push it to the repo.

Go to your new project and create a new pipeline under 'Pipelines'

- I have to choose "Azure Repos Git"
- select your Repo
- 'Configure your pipeline': select 'Node.js with Angular'

Edit Pipeline:

- change node versionSpec to 14.x or 16.x
- add this as first line in 'script':

`cd '$(System.DefaultWorkingDirectory)/HelloWorld'`

'Save and Run' pipeline and make sure it is green. Congrats!

Pipeline so far:

```yaml
trigger:
- main

pool:
  vmImage: ubuntu-latest

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '16.x'
  displayName: 'Install Node.js'

- script: |
    cd '$(System.DefaultWorkingDirectory)/HelloWorld'
    npm install -g @angular/cli
    npm install
    ng build --prod
  displayName: 'npm install and build'
```

## Azure DevOps

What is Azure DevOps <https://learn.microsoft.com/en-us/azure/devops/learn/what-is-devops>

## Azure DevOps Example

<https://learn.microsoft.com/en-us/training/modules/get-started-with-devops/>

- Create Organization
- Create Project
- Create Board and Tasks

### Pipelines

Generate a new Pipelines: <https://learn.microsoft.com/en-us/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops>

>In my case, I had to select 'ASP.NET Core' template manually. This creates the following pipeline:

```yaml
# ASP.NET Core
# Build and test ASP.NET Core projects targeting .NET Core.
# Add steps that run tests, create a NuGet package, deploy, and more:
# https://docs.microsoft.com/azure/devops/pipelines/languages/dotnet-core

trigger:
- master

pool:
  vmImage: ubuntu-latest

variables:
  buildConfiguration: 'Release'

steps:
- script: dotnet build --configuration $(buildConfiguration)
  displayName: 'dotnet build $(buildConfiguration)'
```

Key Concepts: <https://learn.microsoft.com/en-us/azure/devops/pipelines/get-started/key-pipelines-concepts?view=azure-devops>

### Create Pipeline with SourceCode in Azure DevOps Repo

Do it with source code in Azure DevOps, not Github:

- Put source code in Azure DevOps repo (I forked this project from GitHub to Azure Devos: https://github.com/MicrosoftDocs/pipelines-dotnet-core)
- Open your Azure DevOps Organization + Project
- Pipelines: Create Pipeline
- Select a repository: Select your repo ('pipelines-dotnet-core' in my case)
- Where is your code?: Select 'Azure Repos Git'
- Configure your pipeline: press 'Show More'
- Select 'ASP.NET Core' - inspect it :)
- Press 'Save and Run', 'Save and Run'
- Count to ten and voila - the new pipeline did run and is green :)
- Notice: the new pipeline confg file 'azure-pipeline.yml' was added to the root project. you can edit there or in the Azure DevOps pipeline menu

### YAML

- Stage/Job/Step hierarchy: <https://stackoverflow.com/questions/66705319/how-to-choose-between-step-job-and-stages-in-azure-devops-yaml>
- CMD: <https://learn.microsoft.com/en-us/azure/devops/pipelines/tasks/utility/command-line?view=azure-devops&tabs=yaml>
- Bash: <https://learn.microsoft.com/en-us/azure/devops/pipelines/tasks/utility/bash?view=azure-devops>

## Information

- Your Azure Devops organizations (when logged in): https://dev.azure.com/
- Azure DevOps Docs: https://docs.microsoft.com/en-us/azure/devops/pipelines/?view=azure-devops
- Create .NET Pipeline: https://docs.microsoft.com/en-us/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=net%2Ctfs-2018-2%2Cbrowser
- Testing, Unit Testing: <https://github.com/boeschenstein/testing/tree/main>
- Fork GitHub to Azure DevOps: https://stackoverflow.com/questions/40752333/fork-github-to-azuredevops
