# Working of pipeline

***Objective***

The aim of this section is to perform static analysis on DVNA using SAST tools in a Jenkins pipeline and solve the 4th point of the [Problem Statement](https://devsecops-report.netlify.app/problem-statements/).
To start creating the Jenkins pipeline, login into the jenkin web interface.

-  In the `Dashboard`, create a `New Item` and enter a `item name`(e.g. `dvna-pipeline`) and select `pipeline` and click `OK`.
  
-  It was redirected to the `configuration` page. Here:
  
    - Under `general` section:
  
      - Gave description of the application being deployed.
  
      - I checked the `Discard old builds` as they keep metadata related to old builds.
  
    - Under `pipeline` section, I selected the defination dropdown to `Pipeline script from SCM`.
  
     - In `SCM` dropdown I selected `GIT`, and gave the `repository URL`.
  
      - Click `Save` to save and aplly the configurations. 
  
- Go to `Dashboard` -> `dvna-pipeline` -> `build now` -> `console output`.
  
## Jenkinsfile

- Jenkinsfile is a text file that contains the definition of a Jenkins Pipeline and is checked into source control.The following are the contents of the Jenkinsfile which implements a continuous delivery pipeline:

        pipeline {
            agent any

            stages {
                stage ('Initialization') {
                    steps {
                        sh 'echo "Starting the build!"'
                    }
                }

                stage ('Build') {
                    environment {
                        MYSQL_USER="dvna"
                        MYSQL_DATABASE="dvna"
                        MYSQL_PASSWORD="Prlokhande_5398"
                        MYSQL_RANDOM_ROOT_PASSWORD="yes"
                        MYSQL_HOST="mysql-db"
                        MYSQL_PORT=3306
                    }
                    steps {
                        sh 'echo "MYSQL_USER=$MYSQL_USER\nMYSQL_DATABASE=$MYSQL_DATABASE\nMYSQL_PASSWORD=$MYSQL_PASSWORD\nMYSQL_RANDOM_ROOT_PASSWORD=$MYSQL_RANDOM_ROOT_PASSWORD\nMYSQL_HOST=$MYSQL_HOST\nMYSQL_PORT=$MYSQL_PORT" > ~/vars.env'
                        sh 'docker run --rm -d --name dvna-mysql --env-file ~/vars.env mysql:5.7 tail -f /dev/null'
                        sh 'docker run --rm -d --name dvna-app --env-file ~/vars.env --link dvna-mysql:mysql-db -p 9090:9090 appsecco/dvna'
                        sh 'docker cp dvna-app:/app/ ~/'        
                    }
                } 
                

                stage ('Remove DVNA from Jenkins') {
                    steps {
                        sh 'rm -rf ~/app'
                        sh 'docker stop dvna-app && docker stop dvna-mysql'
                        sh 'docker rmi appsecco/dvna'
                    }
                }

                stage ('Deploy DVNA to Production') {
                    steps {
                        sh 'ssh -tt -o StrictHostKeyChecking=no prod-vm@192.168.1.55 "docker stop dvna-app && docker stop dvna-mysql && docker rm dvna-app && docker rm dvna-mysql && docker rmi appsecco/dvna || true"'
                        sh 'scp ~/vars.env prod-vm@192.168.1.55:~/'
                        sh 'ssh -tt -o StrictHostKeyChecking=no prod-vm@192.168.1.55 "docker run -d --name dvna-mysql --env-file ~/vars.env mysql:5.7 tail -f /dev/null"'
                        sh 'ssh -tt -o StrictHostKeyChecking=no prod-vm@192.168.1.55 "docker run -d --name dvna-app --env-file ~/vars.env --link dvna-mysql:mysql-db -p 9090:9090 appsecco/dvna"'
                    }
                }

            }
        }

## Stages

The pipeline is divided into various stages based on the operations being performed, which are as follows:

## Initialization

This is the first stage which is used to just display the start of the building stage.

## Build

This stage contains the environment variables, in which `vars.env` file is created and for `dvna-mysql` container. `dvna-app` and `dvna-mysql` containers will run and will be copied into the home directory.

## Static and Dynamic Analysis

All the stages that follow the Build Stage, except for the last two stages, are for performing static analysis  and dynamic analysis on DVNA and later being stored in a folder `reports` in `jenkins` home directory.

## Remove DVNA from Jenkins

After the scans are complete, the containers running in Jenkins VM are stopped and removed. Since we're working with a containerized application (DVNA), we need to perform tests on the latest available image on DockerHub. Hence, we remove the existing local `appsecco/dvna` docker image to avoid running a container with older release of the application image. On the other hand, you don't need to remove the mysql:5.7 image, since we require v5.7 and not the latest version.

## Deployment

Finally, the satge `deploy to dvna`, operations are performed on VM over SSH which was configured previously. The two containers, `dvna-app` and `dvna-mysql` are run and successfully deployed.

# Static Application Security Testing(SAST)

SAST is a testing methodology that analyses source code, byte code and binaries for bugs and design errors to find [security vulnerabilities](https://www.synopsys.com/blogs/software-security/types-of-security-vulnerabilities/), before the application is compiled. 

SAST does not require a working application and can take place without code being executed. It helps developers identify vulnerabilities in the initial stages of development and quickly resolve issues without breaking builds or passing on vulnerabilities to the final release of the application.

![image](pictures/1.png)
## SAST Tools for Node.js Applications

As given the name Damn Vulnerable Nodejs Application, it is quite obvious to figure the tech stack used, Nodejs is the server-side language used along with a SQL database.  

The following are the tools used to perform static analysis on Nodejs applications with steps to install and configure them with Jenkins.


### njsscan

njsscan is a open source static security code scanner for Node.js applications. Finds insecure paatterns in Node.js code and HTML templates.

The tool is written in python, we need to install it using pip3, I went ahead and updated apt package and installed python3-pip.

    sudo apt update && sudo apt install python3-pip

Intall,njsscan using pip3:

    pip3 install njsscan

***NOTE***: I got an error while installing through njsscan, I entered into the `root` directory and installed it, which worked for me or you can also create a virtual environment and install it.

I referred the documentation which provides us [command line options](https://github.com/ajinabraham/njsscan#command-line-options). Later, I added the script in the jenkinsfile with the following syntax:

    mkdir reports
    njsscan -o ~/reports/nodejsscan-report.json --json  ./dvna

## njsscan pipeline

I added the following stage in the Jenkinsfile to perform the scan, and store the report in JSON format on the Jenkins machine:

    stage('NodeJsScan') {
        steps {
            sh 'njsscan --json -o ~/reports/nodejsscan-report ~/app || true'
        }
    }
## AuditJS

Audits JavaScript is a SAST tool which uses [OSS Index v3 REST API](https://ossindex.sonatype.org/rest) to identify known vulnerabilities and outdated package versions.

- To install npm and nodejs in dvna container in production server, I followed the commandsas given below, the package versions are: npm v6.14.14 and nodejs v14.17.4.

    sudo apt update
    sudo curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
    sudo apt install -y nodejs

- Install auditjs using npm:
  
        sudo npm install -g auditjs

- Scan the directory which holds the files for DVNA and store the scan result in ~/reports/auditjs-report.

        auditjs ossi > ~/reports/auditjs-report ./app

## AuditJS pipeline

Finally, I added the following stage to the Jenkinsfile to execute the script I wrote:

    stage('Auditjs') {
        steps {
            sh 'cd ~/app; auditjs ossi > ~/reports/auditjs-report || true'
        }
    }

## SAST Pipeline

Add the following stages to the Jenkinsfile for performing SAST of DVNA:

    stage('NodeJsScan') {
        steps {
            sh 'njsscan --json -o ~/reports/nodejsscan-report ~/app || true'
        }
    }

    stage('Auditjs') {
        steps {
            sh 'cd ~/app; auditjs ossi > ~/reports/auditjs-report || true'
        }
    }

