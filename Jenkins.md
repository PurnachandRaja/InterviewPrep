### 1.Explain your CI-CD Workflow?
Source Code standards, Test and Package, Versioning and Release Notes, Promote to Production.
We use Automated Pipelines. 
1. Build Pipeline: Jenkins/Jenkinsfile
Manage CI Cycle. Testing, Packaging & Releasing
Build Pipeline stages:
Checkout & Setup(empty by default)
Build
Lint & Unit Tests
Generate Manifest
Create TestEnv, Deploy Manifest & Acceptance Test(FT, ICT, etc)
Quality & Security Scans: aqua, arachini, fortify, scorebot, sonarqube, sonatype (parallel)
Post Build: Notifications & Cleanup

2. Git Flow Release Pipeline: Jenkins/Jenkinsfile.release
Moving code from Develop to Main
Release Pipeline Stages:
Checkout: Checkout the HEAD of the Integration Branch (develop)
Setup: Install any dependencies the pipeline needs to execute. Download GitFlow CLI
Validate Integration Branch: Is it ok for me to merge the code from develop into main? Your develop branch last build should be green.
Start Release: Release Strategy
1.Determine your current artifact version
2.Calculate the next release version a.b.c 
3.Create a temporary release/a.b.c branch
Update Release Version: Update your artifact version in your source. via new commit
Change Log: 
1. Obtain the list of commits that contain a [JIRA-123] ticket
2. Query JIRA for information on the tickets
3. Generate and commit a new changelog/a.b.c.md file
Finish Release:
1. Merge changes to main
2. Tag HEAD of master with a.b.c
3. Merge changes to develop from release/a.b.c
4. Delete release/a.b.c temporary branch
Update Development Version: Next Release strategy
1. Calculate the next development version
2. Bump develop to the next development version
3. Commit changes
Push Changes: Push changes in main and develop back to GitHub
GitHUb Release: Generate a GitHub release using the changelog/a.b.c file and the a.b.c tag in the HEAD  of main
Cleanup 

### 2.How to define the Artifactory servers details in Jenkins file ?

### 3.What is the purpose of the Multi branch pipeline in Jenkins ?
The Multi-Branch Pipeline is a plugin in Jenkins that allows you to automatically create a Jenkins pipeline job for each branch of a project in your version control system (e.g. Git, SVN). This plugin provides a powerful way to organize and automate your Jenkins pipelines.
The main purpose of the Multi-Branch Pipeline is to automate the process of building, testing, and deploying code changes in a CI/CD pipeline. Each branch of your codebase can have its own pipeline job, which can run in parallel with other pipeline jobs for other branches. This allows you to quickly test changes and merge them into your main branch when they are ready.

### 4.What is the purpose of the Jenkins slave ?
In Jenkins, a slave (also known as an agent or node) is a separate process that runs on a different machine or on the same machine as the Jenkins master. The purpose of the Jenkins slave is to perform the actual build and test tasks that are specified in the Jenkins job, while the Jenkins master manages and schedules the jobs.

The use of a slave allows you to distribute the build and test workload across multiple machines, which can significantly reduce build times and improve overall performance. Slaves can be located on different operating systems or hardware architectures, allowing you to test your code on a variety of environments.

### 5.How to install the Jenkins third party plugin without Downtime ?
To install a Jenkins third-party plugin without downtime, you can use the Jenkins Plugin Manager to perform the installation process. Here are the steps to do it:

Log in to your Jenkins instance and navigate to the "Manage Jenkins" section.

Click on "Manage Plugins" and go to the "Available" tab.

Search for the plugin you want to install and select it.

Click on the "Download now and install after restart" button to download the plugin.

Before clicking the "Restart Jenkins when installation is complete and no jobs are running" button, go to the "Manage Nodes" section and identify the nodes that are currently executing jobs.

Once you have identified the nodes, temporarily disable them by setting their usage to "exclusive" in the "Configure" screen.

Once you have disabled the nodes, return to the "Manage Plugins" screen and click the "Restart Jenkins when installation is complete and no jobs are running" button to restart Jenkins.

After Jenkins has restarted, navigate to the "Manage Nodes" section and re-enable the nodes by setting their usage back to "normal".

By following these steps, you can install a third-party plugin in Jenkins without downtime, as the jobs that are running on the disabled nodes will continue to run until they are finished, and new jobs will be automatically distributed to the other nodes that are still available.

### 6.Write an sample declarative Pipeline?
```Jenkinsfile
pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        // build your code here
      }
    }
    stage('Test') {
      steps {
        // run tests here
      }
    }
    stage('Deploy') {
      steps {
        // deploy your code here
      }
    }
  }
}
```




