## What is pipeline

Pipeline is a series of events or tasks performed in a specific sequence to transform the code from version control into a stable software product by employing a suite of plugins in Jenkins.

## Setting Up Jenkins Pipeline

We have already setup Jenkins on a VM in the previous section. Now we will be using Jenkins to build a pipeline which will be responsible for fetching the latest application code from Github, building it and then copying it over to the application VM and deploying it.I referred the [documentation](https://www.jenkins.io/doc/pipeline/tour/hello-world/) and followed the steps as below:

### General

- Click on `New Item` within JENKINS.
- Provide the name for you new item, which in mycase is `Maven-project` and select `pipeline` and click on OK.
- Check the Github Project option and provide the URL for the project as we will be fetching code from Github.

## Build Triggers

- Check the GitHub hook trigger for GITScm Polling option as it allows us to trigger builds automatically using GitHub hooks.

## pipeline

- From the Definition dropdown, choose the Pipeline script from SCM option as we will be using a jenkinsfile.

## Configuring Jenkins

### What is Jenkinsfile?
Jenkins Pipeline has a customizable and scalable automation system that lets you build distribution pipeline scripts -  “Pipeline as Code”, these scripts are called JenkinsFile. 
A JenkinsFile stores the entire CI/CD process as code on the local machine. As such, the file can be reviewed and checked into a Source Code Management (SCM) platform (be it Git or SVN) along with your code. Hence, the term “Pipeline as Code”.


The structure of my jenkinsfile is as follows:

         
    pipeline {
        agent any

        stages {
            stage ('Compile Stage') {

                steps {
                    withMaven(maven : 'maven_3_6_0') {
                        sh 'mvn clean compile'
                    }
                }
            }

            stage ('Testing Stage') {

                steps {
                    withMaven(maven : 'maven_3_6_0') {
                        sh 'mvn test'
                    }
                }
            }


            stage ('Deployment Stage') {
                steps {
                    withMaven(maven : 'maven_3_6_0') {
                        sh 'mvn deploy'
                    }
                }
            }
        }
    }


###  SSH Configuration

- After installing SSH, create a key pair on a client machine.
   
      ssh-keygen -t rsa
- The first prompt from the ssh-keygen command will ask you where to save the keys, I pressed `enter` to save as it was.
- Similarly, `Creating passphrase` just pressed `enter`.
- once the key is generated, place the public key on the server which you want to connect to. Following the command below:
  
      ssh-copy-id sammy@your_server_address

- While ssh'ing into the client I got an error as `Connection Refused`, so in the VM I selected `file` and `Host Network Manager` and created `vboxnet0` as shown below:

![image](/pictures/desktop.png)




