# Milestone 1

### Team Members
1. Kumar Utsav (kutsav)
2. Raman Preet Singh (rpsingh2)
3. Rohit Arora (rarora4)

### Description

We are using the following node js project as our target project: 
```
https://github.com/DevOps-HeadBangers/markdown-js
```
A build to this project installs all the dependencies and also runs the test cases.

### Tools Used

Jenkins Build Server

NPM Package Manager

Apache Tomcat 8 

Git

### Build Section

#### Install Jenkins 

1. Download and [install Tomcat8](http://www.liquidweb.com/kb/how-to-install-apache-tomcat-8-on-ubuntu-14-04/) on your system.

2. Download [jenkins.war](http://mirrors.jenkins-ci.org/war/latest/jenkins.war) file.

3. Place the war file in the /opt/tomcat/webapps directory.

4. Run tomcat server using bin/startup.sh file.

5. Open browser and go to [http://localhost:8080/jenkins](http://localhost:8080/jenkins) to run jenkins. 

#### Configure Jenkins

1. Go to Jenkins on the top left corner and choose Manage Jenkins -> Manage Plugins.

2. Install GitHub Plugin, Email Extension and NodeJS Plugins from there.

3. Now, go to Manage Jenkins -> Configure System -> NodeJS. Click Add NodeJS and click install automatically. This will give you a list of node versions. Choose according to the version installed on your system. Save.

4. To configure Email Extension,  go to Manage Jenkins -> Configure System -> Extended E-mail Notification. Now, enter the details based on your SMTP server. Under Triggers, choose Always to send an email on every build. Get Some Hints from [here](https://www.safaribooksonline.com/library/view/jenkins-the-definitive/9781449311155/ch04s08.html).

5. When you are done, save.

#### Create Jobs

1. Create a new job by clicking on Create New Job.

2. Provide the job name, choose freestyle project and click OK.

3. Now configure your job to add the target git project to the job by choosing Git from Source Code Management         Options and enter the git repository.

4. Under Build Environment, check the 'Provide Node & npm bin/ folder to PATH' option and choose the NodeJS            installation from the previous step.

5. If you want to have multiple jobs for multiple branches of the project, then give the branch name under the         Source Code Management for one of the branches.

6. Now, for other branches clone the previous job with a name reflecting the branch name and also provide the branch    name under the Source Code Management for the corresponding branch. 


#### Test Build

Go to the job page and click Build Now in the left pane. If, you get a blue sphere then that means the build was successful else you will get a red sphere.

#### Capabilities

##### The ability to trigger a build in response to a git commit via a git hook.

Whenever a new commit is made to a particular branch of the repo, post-commit hook will be executed and it will start the corresponding Jenkins build. The code for post-commit is as follows: 

```
#!/bin/bash

curl http://localhost:8080/jenkins/job/<jenkins_job_name>/build

```
![Capability 1](https://github.com/DevOps-HeadBangers/Milestone1/blob/master/Screencast/Curl.gif) 

##### The ability to execute a build job via a script or build manager (e.g., shell, maven), which ensures a clean build each time.

1. Go to the Configure page of your job and under Build section choose Add Build Step and under that choose Execute     Shell.
2. Now, type the following commands and save :
```
rm -rf node_modules
npm install
npm test
```

Now, go back to the job page and click Build Now in the left pane.

![Capability 2](https://github.com/DevOps-HeadBangers/Milestone1/blob/master/Screencast/Script.gif) 

##### The ability to determine failure or success of a build job, and as a result trigger an external event (run post-build task, send email, etc).

1. Go the Configure page of your job. Under Post-build Actions, click on Post Build Action and choose Editable Email    Notification and configure it based on your needs.

2. Now, when you build you will get the email notification for the corresponding build.

![Capability 3](https://github.com/DevOps-HeadBangers/Milestone1/blob/master/Screencast/Email.gif) 

##### The ability to have multiple jobs corresponding to multiple branches in a repository. Specifically, a commit to a branch, release, will trigger a release build job. A commit to a branch, dev, will trigger a dev build job.

1. Checkout the branch for which you want to build
2. Keep the following code in the post-commit hook of the repo. Provide Executable (+x) permissions to the post-commit hook. Replace the  <branch_name>'s with the branch names     for your repo.

```
#!/bin/bash

if [ `git rev-parse --abbrev-ref HEAD` == "<branch_name>" ]; then
   curl http://localhost:8080/jenkins/job/<jenkins_job_name>-<branch_name>/build
elif [ `git rev-parse --abbrev-ref HEAD` == "<branch_name>" ]; then
   curl http://localhost:8080/jenkins/job/<jenkins_job_name>-<branch_name>/build
fi
``` 

Now, when you commit to the branch, the corresponding job will build based on the conditions in the post-commit     hook. 

![Capability 4](https://github.com/DevOps-HeadBangers/Milestone1/blob/master/Screencast/GitBranch.gif) 

##### The ability to track and display a history of past builds (a simple list works) via http 

The http request for the capability:
```
http://localhost:8080/jenkins/job/<jenkins_job_name>/api/json?pretty=true
```
![Capability 5](https://github.com/DevOps-HeadBangers/Milestone1/blob/master/Screencast/History.gif)
Sample output:

```
{
  "actions" : [
    
  ],
  "description" : "",
  "displayName" : "Milestone1",
  "displayNameOrNull" : null,
  "name" : "Milestone1",
  "url" : "http://localhost:8080/jenkins/job/Milestone1/",
  "buildable" : true,
  "builds" : [
    {
      "number" : 5,
      "url" : "http://localhost:8080/jenkins/job/Milestone1/5/"
    },
    {
      "number" : 4,
      "url" : "http://localhost:8080/jenkins/job/Milestone1/4/"
    },
    {
      "number" : 3,
      "url" : "http://localhost:8080/jenkins/job/Milestone1/3/"
    },
    {
      "number" : 2,
      "url" : "http://localhost:8080/jenkins/job/Milestone1/2/"
    },
    {
      "number" : 1,
      "url" : "http://localhost:8080/jenkins/job/Milestone1/1/"
    }
  ],
  "color" : "blue",
  "firstBuild" : {
    "number" : 1,
    "url" : "http://localhost:8080/jenkins/job/Milestone1/1/"
  },
  "healthReport" : [
    {
      "description" : "Build stability: 1 out of the last 5 builds failed.",
      "iconClassName" : "icon-health-60to79",
      "iconUrl" : "health-60to79.png",
      "score" : 80
    }
  ],
  "inQueue" : false,
  "keepDependencies" : false,
  "lastBuild" : {
    "number" : 5,
    "url" : "http://localhost:8080/jenkins/job/Milestone1/5/"
  },
  "lastCompletedBuild" : {
    "number" : 5,
    "url" : "http://localhost:8080/jenkins/job/Milestone1/5/"
  },
  "lastFailedBuild" : {
    "number" : 3,
    "url" : "http://localhost:8080/jenkins/job/Milestone1/3/"
  },
  "lastStableBuild" : {
    "number" : 5,
    "url" : "http://localhost:8080/jenkins/job/Milestone1/5/"
  },
  "lastSuccessfulBuild" : {
    "number" : 5,
    "url" : "http://localhost:8080/jenkins/job/Milestone1/5/"
  },
  "lastUnstableBuild" : null,
  "lastUnsuccessfulBuild" : {
    "number" : 3,
    "url" : "http://localhost:8080/jenkins/job/Milestone1/3/"
  },
  "nextBuildNumber" : 6,
  "property" : [
    
  ],
  "queueItem" : null,
  "concurrentBuild" : false,
  "downstreamProjects" : [
    
  ],
  "scm" : {
    
  },
  "upstreamProjects" : [
    
  ]
}
```
