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

### Create Build Pipeline with SourceCode in Azure DevOps Repo

Do it with source code in Azure DevOps, not Github:

- Put source code in Azure DevOps repo (I forked this project from GitHub to Azure Devos: https://github.com/MicrosoftDocs/pipelines-dotnet-core)
- Open your Azure DevOps Organization + Project
- Pipelines: Create Pipeline
- Select a repository: Select your repo ('pipelines-dotnet-core' in my case)
- Where is your code?: Select 'Azure Repos Git'
- Configure your pipeline:
  1. if available, select suggested 'ASP.NET Core' pipeline
  2. if no .NET option is available (might happen if you have no test project or no solution file), choose it manually: 
    - Press 'Show More' 
    - Select 'ASP.NET Core' - inspect it :)
- Press 'Save and Run', 'Save and Run'
- Count to ten and voila - the new pipeline did run and is green :)
- Notice: the new pipeline confg file 'azure-pipeline.yml' was added to the root project. you can edit there or in the Azure DevOps pipeline menu

### Create Test Pipeline

- Example: <https://learn.microsoft.com/en-us/training/modules/run-quality-tests-build-pipeline/4-add-unit-tests>
- About: <https://learn.microsoft.com/en-us/azure/devops/pipelines/test/test-glossary?view=azure-devops>
- I had to fix the generated pipeline manually: add a custom filter in 'testAssemblyVer2' (my test assembly contains the name 'Tests'):

```yaml
# - task: VSTest@2
#   inputs:
#     platform: '$(buildPlatform)'
#     configuration: '$(buildConfiguration)'
#
# HACK from https://github.com/microsoft/vstest/issues/3937#issuecomment-1219217090
- task: VSTest@2
  inputs:
    platform: '$(buildPlatform)'
    configuration: '$(buildConfiguration)'
    testSelector: 'testAssemblies'
    testAssemblyVer2: |
      **\*Tests*.dll
      !**\*TestAdapter.dll
      !**\obj\**
    searchFolder: '$(System.DefaultWorkingDirectory)'
```
### Code Coverage (coverlet)

- <https://learn.microsoft.com/en-us/training/modules/run-quality-tests-build-pipeline/6-perform-code-coverage>
- https://www.linkedin.com/pulse/net-core-open-source-unit-test-coverage-reports-federico-antu%C3%B1a?trk=public_profile_article_view
  - 'collect:"XPlat Code Coverage"' solves issue on my machine, because '/p:Coverlet...' fails on my machine: Argument not recognized

```
dotnet tool install --global dotnet-reportgenerator-globaltool
dotnet test --collect:"XPlat Code Coverage" --results-directory:"./.coverage"
reportgenerator "-reports:.coverage/**/*.cobertura.xml" "-targetdir:.coverage-report/" "-reporttypes:HTML;"
```

### Pipeline Infos

- Stage/Job/Step hierarchy: <https://stackoverflow.com/questions/66705319/how-to-choose-between-step-job-and-stages-in-azure-devops-yaml>
- CMD: <https://learn.microsoft.com/en-us/azure/devops/pipelines/tasks/utility/command-line?view=azure-devops&tabs=yaml>
- Bash: <https://learn.microsoft.com/en-us/azure/devops/pipelines/tasks/utility/bash?view=azure-devops>
- Variable examples: https://learn.microsoft.com/en-us/training/modules/create-a-build-pipeline/7-publish-build-result
- Zip release example: https://learn.microsoft.com/en-us/training/modules/create-a-build-pipeline/7-publish-build-result
- Set up approvals: <https://learn.microsoft.com/en-us/training/modules/implement-code-workflow/8-require-reviewer>

### YAML

- Templates (code reuse, like functions): https://learn.microsoft.com/en-us/training/modules/create-a-build-pipeline/8-build-multiple-configurations
- Schema reference: <https://learn.microsoft.com/en-us/azure/devops/pipelines/yaml-schema/?tabs=schema&view=azure-pipelines>
- YAML in Minutes: <https://learnxinyminutes.com/docs/yaml/>

## Information

- Your Azure Devops organizations (when logged in): https://dev.azure.com/
- Azure DevOps Docs: https://docs.microsoft.com/en-us/azure/devops/pipelines/?view=azure-devops
- Create .NET Pipeline: https://docs.microsoft.com/en-us/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=net%2Ctfs-2018-2%2Cbrowser
- Testing, Unit Testing: <https://github.com/boeschenstein/testing/tree/main>
- Fork GitHub to Azure DevOps: https://stackoverflow.com/questions/40752333/fork-github-to-azuredevops
