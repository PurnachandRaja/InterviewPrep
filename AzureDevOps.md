#### Azure Boards Questions

### 1.How do we add custom fields in work items?
This can be achieved through the inheritance process

Organization settings >> Select process under Boards >> Right click a process (from Basic, Agile, Scrum, CMMI) on processes tab >> Create inherited process

Now we can add New work item type or we can edit existing work item for eg, select user story >> Layout >> we can add new fields to existing groupd by clicking three dots >> New field or you can create a New field itself by selecting New field option or create a New group using New group option on top.

Custom fields are not available on existing projects, to use we need to create new project with the new process we created.

### 2.Work Items (let us say User stories) are not visible under BOARDS tab?
- By default only default area work items are visible under the BOARDS tab.
- And when AREA Path is defined for the given project, the given items are not visible with AREA path.

AREA Path:
- Area paths allow you to group work items by team, product, or feature area.
- These fields allow you to define a hierarchy of paths.

We can fix it by,

To Add Areas: 
Project settings >> Boards >> Project configuration >> Areas Tab(We can add multiple areas)
Fix:
Project settings >> Boards >> Team configuration >> Areas Tab >> On default area select Include sub areas by clicking three dots.



#### Azure Repos Questions 

 ### 1. We have 50 commits in Develop branch and out of which only 5 commits needs to be pushed in Release branch which will eventually deployed in prodcution?
This can be achieved with a concept called as git cherry picking. It is the act of picking a commit from a branch and applying it to another
```
git cherry-pick commitSha
``` 
In your Azure Repo go to develop branch, open the commit you want to merge, you will find an option cherry-pick. Click it and select the target branch in this case release. It will create a temporary branch and ask to create a Pull Request to develop. Once PR is merged the cherry-picked commit will be available in develop.

### 2.Ram has developed an application module and while developing he has made around 100 commits locally, now he needs to push all his changes in remote Develop branch as a single commit (We don't want developers messy history in Develop branch) ?
This can be achieved by using git squashing. Squash will combine all the commits in to one commit
```
git rebase -i HEAD~100
```
Push his feature branch to remote. Create a PR from feature to develop. While clicking complete use merge type as Squash commit. Optionally you can also check delete the feature branch after merging.

### 3.Ramesh has few uncommit changes in his local branch and now he has to empty his current work directory to accommodate emergency change(ECR, Bug fix), How can he handle such scenario without losing his uncommit changes?
We can use git stash to save uncommitted changes(both staged and unstaged) for later use, however this feature is not available on Azure Repos.

```
git stash (stash uncommit changes)
git stash list (to list all stashed changes)
git stash pop (To bring back lastest stash to working directory)
git stash drop (To drop/delete the most recent stash)
```

### 4.Kiran has packaged and pushed his project module in Azure Artifacts and now other developer Julia who is working on some other module has a direct dependency on Kiran's module. How can Julia project consume packages fron Azure Artifacts locally to sucessfully build project?
- Check if Julia has has required permissions to access the private feed.
- Add Azure Artifacts feed source URL in Visual Studio manage packages for solution
- Post that Install the required feed and check project dependencies

### 5.Let us say we have multiple project folders in a single repository, and we do have build pipelines created for each project. Now only that particular project build should be triggered automatically when there is a change in that particular folder but not all?
Yes we can use path filteration to achieve this
Override triggers setting and specify the exact path for the build pipeline
 - In Build pipeline >> Triggers >> Check Override the YAML continuous integration trigger from here >> Path filters >> Add path to the folder

Mention the path in the YAML file for build pipeline
```yaml
# specific path build
trigger: 
    branches:
        include:
        - main
        - develop
    paths:
        include:
        - docs
        exclude: 
        - docs/README.md
```
### 6.We have multiple build pipelines and those builds are sequential in nature and directly dependent on each other and should trigger automatically when the previous build is successfully completed. How do we achieve this in Azure DevOps?
We can achieve it using build completion option in trigger
- In Build Pipeline >> Triggers >> Build Completion >> Add >> Select the Build Pipeline after which you want to trigger this pipeline (You also needs to select the branch)

Mention the pipelines field in the YAML file of the build pipeline 
```yaml
#this is defined in app-ci pipeline
resources:
    pipelines:
    - pipeline: securitylib
      source: security-lib-ci
      trigger:
        branches:
            include:
            - releases/*
            exclude:
            - releases/old*
```

### 7.How to restrict a developer by not allowing him to push any messy commit history into a remote develop/release branch from his/her feature branch?
We can use enforce Branch policies to achieve this
Branch Policies:
 - By enforcing branching policies on branches
 - Making sure only specific types of merge is permitted while creating PR

 Go to Repos >> Branches >> develop (any branch) >> click three dots >> Branch policies >> Limit merge types >> squash merge

 Options in Branch policies:
 Require a minimum no of reviewers
 Check for linked work items
 Check for comment resolution
 Limit merge types

 ### 8.What is the maximum size of commits we can push in Azure Repos? And how can we restrict the push size?
- Pushes are limited to 5GB at a time. 

To restrict,
Go to Project settings >> Repositories >> Select a Repo >> Policies >> Switch on Maximum file size toggle

#### Pipelines Questions:

### 1.Consider i have multiple commonly used pipeline variables. How do i make sure those variables are defined once and re-used or referenced in multiple pipelines when ever required?
We can use variable groups
Variable Groups:
- By creating a variable group and defining commonly used variables

In Pipelines section >> Library >> Variable group >> Add common variables to the variable group
To use >> Open any Release pipeline >> Variables Tab >> Variable groups >> Link variable group >> Select the relevant variable group >> Variable group scope (Release or Stages)

Variable groups can be even used to link secrets from an Azure Key Vault as variables. There is a toggle to Link secrets from an Azure key vault as variables while creating variable group. Later we can download password from key vault in pipeline using key vault task.

### 2.Consider i have multiple commonly used pipeline tasks. How do i make sure those tasks are created & defined once and re-used or referenced in multiple pipelines(build or release) when ever required?
We can use task groups to achieve this
Task Groups:
- By grouping one or more commonly used task in a Task Group.
- Managing the tasks from a central location and calling or referencing the task easily.

In Pipelines section >> Go to any of you build or release pipeline >> Tasks Tab >> Select all the tasks you want to group >> Right click and select create task group

Ref: https://learn.microsoft.com/en-us/azure/devops/pipelines/library/task-groups?view=azure-devops

To use while adding tasks search for the name of task group you created and select it.

Task Groups are only available in classic model.

### 3. What is the difference between environments and deployment groups under pipelines?
Deployment Groups:
- A deployment group is a logical set of deployment target machines that have agents installed on each one
- Deployment groups represent the physical environments; for example, 'Dev', 'Test', or 'Production' environment.

Deployment groups are only available with Classic release pipelines

Environments:
- An environment is a collection of resources that you can target with deployments from a pipeline.
- Environment is a grouping of resources, the resources themselves represent actual deployment targets. The kubernetes resource and virtual machine resource types are currently supported

#### CI/CD Questions:

### 1.Ram has created a ASP.NET Core application and now he wish to automate the entire build and release process through Azure DevOps, guide the developer with the entire end to end process? And deploy the application on Azure WebApp using IaC methodology?
For .NET App
Create Azure Repo and ask developer to push the source code in to remote along with IaaC configuration files. Create Service connection to connect to azure cloud services.
Create a build pipeline classic or YAML based 
- Build the solution using MS-build or VS-build.
- Run code analysis through SonarQube, VeraCode etc.
- Run Code Coverage.
- Copy ARM or Terraform configuration files.
- Publish the artifacts either on Azure File share or Azure Pipeline "drop" location.
Release
- Use Terraform config files to create an Azure WebApp
- Post WebApp creation run ASP.NET application deployment task 

For Java Application 
Create a Azure repo and push the code along with IaaC configuration files to remote repository
Create a Build pipeline classic or YAML based
 - Run the Unit Tests
 - Build the application using Maven Build
 - Run Code Coverage 
 - Run the code analysis through SonarQube
 - Copy the tf config files using copy task
 - Publish artifacts to Azure pipeline drop location using publish task
Create a Release Pipeline
 - Install tf and run tf commands to create a Azure WebApp
 - Post WebApp creation run the deployment task to deploy the artifact and run it


### 2.Automate entire build and release of a Java application. And deploy application on AKS Cluster using IaaC And use PaaC(Pipeline as a code)?
Pre-requisites:
- AKS Cluster
- ACR
- Service connection to connect to Azure Cloud services
Create Azure Repos and push source code along with Dockerfile Kubernetes manifest files(Deployment, Service)
Create a build and release pipeline using YAML

Build:
- The solution will be based on the steps mentioned in Dockerfile
- Along with it we can run code analysis and code coverage
- Once image is built it will be pushed to ACR
- Publish the manifests files to Azure drop location
Deploy:
- Pull the image from ACR Registry
- Deployment will be triggered through k8's manifest files

### 3.Explain your ADO or CI/CD workflow?
Webhook is configured from service hooks which triggers pipeline jobs for every new code push and PR. Pipeline will run multiple stages like git clone, build code using maven build and run Unit tests, run sonarqube analysis and posts back to sonar Dashboard, Generates a manifest. Which then deployed on to a test environment which is where FT's will be executed after that the artifact will be published dockerhub and the kubernetes cluster will pull the latest image to deploy to production

### 4. Write an sample YAML Pipeline?

```YAML
# This is a YAML pipeline for building a Java Maven project, including JaCoCo code coverage and SonarQube analysis, packaging it as a Docker image, and publishing the artifact to an Azure Pipelines artifact feed.

trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: MavenAuthenticate@0
  inputs:
    mavenServiceConnection: '<name of your Maven service connection>'

- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
    options: '-B'
    goals: 'package sonar:sonar'
    codeCoverageToolOption: 'JaCoCo'
    sonarQubeRunAnalysis: true
    sqAnalysisName: 'My Project'
    sqProjectKey: 'my-project'
    sqLanguage: 'java'
    sqSourceFiles: 'src/main/java'
    sqTestFiles: 'src/test/java'
    sqJavaBinaries: '**/target/classes'
    sqExtraProperties: 'sonar.coverage.jacoco.xmlReportPaths=jacoco/jacoco.xml'

- task: Docker@2
  inputs:
    containerRegistry: '<name of your Azure Container Registry service connection>'
    repository: '<name of your Docker image repository>'
    command: 'build'
    Dockerfile: '$(Build.SourcesDirectory)/Dockerfile'
    buildContext: '$(Build.SourcesDirectory)'
    tags: |
      latest
      $(Build.BuildNumber)

- task: Docker@2
  inputs:
    containerRegistry: '<name of your Azure Container Registry service connection>'
    repository: '<name of your Docker image repository>'
    command: 'push'
    tags: |
      latest
      $(Build.BuildNumber)

- task: CopyFiles@2
  inputs:
    SourceFolder: '$(System.DefaultWorkingDirectory)/target'
    Contents: '**/*.jar'
    TargetFolder: '$(Build.ArtifactStagingDirectory)'

- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(Build.ArtifactStagingDirectory)'
    ArtifactName: 'drop'
    publishLocation: 'Container'
```

#### Other Questions

### 1.How do we enable event-driven notifications in Azure DevOps and send them across as notifications on other messaging apps?
We can use service hooks to achieve this

Service hooks:
- Service hooks let you run tasks on other services when events happen in your project in Azure DevOps
     
     Publisher ------> Subscription |----- Consumer
               Event                |----- Actions

     - Service hook publisher defines a set of events that you can subscribe to
     - Subscription listen for these events and define actions to take based on event
     - Subscription also target consumers, which are external services that can run their own action when event occur

Eg: You want to send a message on a slack channel your build status.

Go to project settings >> Service hooks >> Create a Subscription >> Slack >> Trigger on this type of event(select when you want to trigger eg: Build completed) >>  Select Filters (Build pipeline , Build status) >> Perform this action >> Add Slack webhook URL

You can get slack webhook URL
Create a new app >> Incoming Webhooks >> Toggle on Activate Incoming Webhooks >> Click Add New Webhook to Workspace >> Select channel name >> click allow 

Copy the URL generated and add it in Azure DevOps Service hooks step from above.''

### 2.How do we enforce policy to automatically include a group as a PR approvers?
This can be achieved by adding a branch policy and adding a group in automatic reviewers

To add a group
Project settings >> Teams >> New Team

Go to Repos >> Branches >> develop >> Branch policies >> Turn on Require minimum no of reviewers & specific minimum no of reviewers >> Automatically included reviewers >> Add new reviewer policy >> Reviewers box (search and add a group) >> Add Minimun no of reviewers >> Save

### 3. What are the different types of deployment strategies in Azure DevOps?
RunOnce:
 - Is the simplest deployment strategy wherin all the lifecycle hooks, namely pre-deploy, deploy, route traffic, and postRouteTraffic are executed once.
Rolling:
- A rolling deployment replaces instances of the previous version of an application with instances of the new version of the application on a fixed set of virtual machines(rolling set) in each iteration.
Canary:
- Canary deployment strategy is an advanced deployment strategy that helps mitigate the risk involved in rolling out new versions of applications.
- By using this strategy, you can roll out changes to a small subset of servers first.

### 4.Can we recover recently deleted project in Azure DevOps?
- Yes within the first 28 days from deletion, a project can be recovered.
- Post that the projects will be permanently deleted.

Only an Organisation owner can restore a deleted project or
Be part of the Project Collection Administrators group

To Rescover,
Organization settings >> Projects >> Bottom you'll find recently deleted projects >> Select project >> Restore Projects

### 5.Difference between Azure DevOps Services and Azure DevOps Server?
Azure DevOps Services:
- Is the cloud offering, provides a scalable, reliable, and globally available hosted service.
- Backed by a 99.9% SLA, Monitored 24/7, and available in local data centers around the world.

Azure DevOps Server:
- Is the on-premises offering, build on SQL Server back-end.
- Usually we choose the on-premises version when we need our data to stay within their network.
- Or, when we want access to SQL Server reporting services that integrate with Azure DevOps Server data and tools.