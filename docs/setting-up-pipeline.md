***Objective***
The aim of this section is to understand the Jenkins pipeline to deploy DVNA and solve the [Problem Statement](https://devsecops-report.netlify.app/problem-statements/).

# Jenkins Pipeline

## Why use Jenkins pipeline ?
Jenkins is an open continuous integration server which has the ability to support the automation of software development processes. You can create multiple automation jobs with the help of use cases, and run them as a Jenkins pipeline.

Here are the reasons why you use should use Jenkins pipeline:

- Jenkins pipeline is implemented as a code which allows multiple users to edit and execute the pipeline process.
- Pipelines are robust. So, if your server undergoes an unforeseen restart, the pipeline will be automatically resumed.
- You can pause the pipeline process and make it wait to resume until there is an input from the user.
- Jenkins Pipelines support big projects. You can run multiple jobs, and even use pipelines in a loop.

##  Setup Jenkins Pipeline

Here, we are using [Apache Maven](https://maven.apache.org/) as a plugin which is a software project management and comprehension tool. Based on the concept of a project object model (POM), Maven can manage a project's build, reporting and documentation from a central piece of information.

Jenkins provides a particular job type, which explicitly provides options for configuring and executing a Maven project. This job type is called the `Maven project` Let’s see how we can create a Maven project in Jenkins and run the same.

### Install Maven Plugin in Jenkins

- Click on the Manage Jenkins as shown below.
  
![image](/pictures/1.png)

  ![image](/pictures/new_1.png)

-  Under the System Configuration section, click on the Manage Plugins options.
 
   ![image](/pictures/manage-plugin.png)

- Under the Plugin Manager, click on the Available tab and search for the maven plugin. It will show the Maven Integration plugin as a result and check the checkbox and select `install without restart`.

  ![image](/pictures/maven-install.png)

- Once the plugin installs successfully, click the checkbox to restart Jenkins.

- After the restart of Jenkins, the Maven Jenkins plugin will be installed successfully and ready for configuration.

## Create A Maven project in Jenkins.

- Firstly, we need to create a job. To create this, click on `New Item` option.
  
- Now, enter item name as Jenkin-Maven and selct Maven project as shown below and click on **OK**.
  ![image](/pictures/jenkin-maven.png)
- Here, under `general` section:
    - I gave the description of the project.
    - Under `Source Code Management` I checked the `Git` option and provided the [Github URl](https://github.com/jenkins-docs/simple-java-maven-app). This helps jenkins to know where to fetch the project from.
   
- Under `Build Triggers`:
    - I checked `Build whenever a SNAPSHOT dependency is built` this is convenient for automatically performing continuous integration. Jenkins will check the snapshot dependencies from the dependency element in the POM, as well as plugins and extensions used in POMs.
    - Later, clicked on `save` button.


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
  

## SSH Access Configuration

- Secure Shell Protocol (SSH) provides a secure channel over an unsecured network by using a client–server architecture, connecting an SSH client application with an SSH server. The standard TCP port for SSH is 22.
  
## Installation

- In order to SSH into VM we need to install it!

      sudo apt-get install openssh-server

- Enable the ssh service by:
  
      sudo systemctl enable ssh

- Start the ssh service:
  
      sudo systemctl start ssh
    
## SSH into VM

- Goto VM - Settings > Network > Advanced > Port Forwarding

![image](/pictures/vm-box.png)

- After installing SSH, create a key pair on a client machine.
   
        ssh-keygen -t ed25519 

- The first prompt from the ssh-keygen command will ask you where to save the keys, I pressed `enter` to save as it was.
- Similarly, `Creating passphrase` just pressed `enter`.
- once the key is generated, place the public key on the server which you want to connect to. Following the command below:
  
        ssh-copy-id username@your_server_address

- While copying the id I got an error "cannot create .ssh/ authorized keys permission denied", so I changed the permissions of the authorized_keys file and the folder/parent folders in which it is located.
 
        chmod 700 ~/.ssh
        chmod 600 ~/.ssh/authorized_keys
- Also, I changed the permissions of home directory to remove write access for the group and others.
- 
        chmod go-w ~

- Provide the information as shown above.

        ssh username@server_ip_address

- I got an error saying permission denied, I refered this  [documentation](https://www.digitalocean.com/community/questions/ssh-permission-denied-please-try-again) and did the necessary changes which are:
    - Type the below command in your termianl:
                
          sudo nano /etc/ssh/sshd_config

    - And change permissions as given below:

          PermitRootLogin yes
          PasswordAuthentication yes
   
- And don't forget to reload:
       sudo systemctl restart ssh.service

- 
- After installing SSH, create a key pair on a client machine.
   
      ssh-keygen -t rsa
- The first prompt from the ssh-keygen command will ask you where to save the keys, I pressed `enter` to save as it was.
- Similarly, `Creating passphrase` just pressed `enter`.
- once the key is generated, place the public key on the server which you want to connect to. Following the command below:
  
      ssh-copy-id username@your_server_address



## Setup SSH keys for Jenkins 
- You will need to create a public/private key as the Jenkins user on your Jenkins server, then copy the public key to the user you want to do the deployment with on your target server.
  
- Generate public and private key on build server as user jenkins and paste the pub file contents onto the target server.
  
- Make sure your .ssh dir has permissoins 700 and your authorized_keys file has permissions 644.
  
- In the jenkins web control panel, install the plugin [Publish Over SSH](https://plugins.jenkins.io/publish-over-ssh/ and nagivate to "Manage Jenkins" -> "Configure System" -> "Publish over SSH".
  
- Either enter the path of the file e.g. "var/lib/jenkins/.ssh/id_rsa", or paste in the same content as on the target server.
  
- Enter your passphrase, server and user details, and you are good to go!

