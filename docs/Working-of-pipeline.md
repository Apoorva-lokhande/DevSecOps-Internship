# Working of pipeline

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
                            MYSQL_PASSWORD=<password>
                            MYSQL_RANDOM_ROOT_PASSWORD=<yes or no>
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

                    stage ('Deploy to DVNA') {
                        steps {
                            sh 'ssh -tt -o StrictHostKeyChecking=no prod-vm@192.168.1.55 "docker stop dvna-app && docker stop dvna-mysql && docker rm dvna-app && docker rm dvna-mysql && docker rmi appsecco/dvna || true"'
                            sh 'scp ~/vars.env prod-vm@192.168.1.55:~/'
                            sh 'ssh -tt -o StrictHostKeyChecking=no prod-vm@192.168.1.55 "docker run -d --name dvna-mysql --env-file ~/vars.env mysql:5.7 tail -f /dev/null"'
                            sh 'ssh -tt -o StrictHostKeyChecking=no prod-vm@192.168.1.55 "docker run -d --name dvna-app --env-file ~/vars.env --link dvna-mysql:mysql-db -p 9090:9090 appsecco/dvna"'
                        }
                }   }
            }
        } 


- `pipeline` - Constitutes the entire definition of the pipeline.
- `agent` - Is used to choose the way the Jenkins instance(s) are used to run the pipeline. The `any` keyword defines that Jenkins should - allocate any available agent (an instance of Jenkins/a slave/the master instance) to execute the pipeline.
- `stages` - Consists of all the stages/jobs to be performed during the execution of the pipeline.
- `stage` - Specify the task to be performed.
- `steps` - This block defines actions to be performed within a particular stage.
- `sh` - Used to execute shell commands through Jenkins.

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

