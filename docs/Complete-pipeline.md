# Final Pipeline Structure

## Objective

The aim of this section is to define complete structure of CI/CD pipeline and solve all the points [Problem Statement](https://devsecops-report.netlify.app/problem-statements/).


## Complete Pipeline

Various stages of DVNA (SAST, DAST, Code Quality Analysis, SBoM, SCA) were tested individually, here we have combined all all the segments together. The following is the complete pipeline to perform multiple scans and then deploy it in production:

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
                    MYSQL_RANDOM_ROOT_PASSWORD="yes"
                    MYSQL_HOST="mysql-db"
                    MYSQL_PORT=3306
                }
                steps {
                    sh 'echo "MYSQL_USER=$MYSQL_USER\nMYSQL_DATABASE=$MYSQL_DATABASE\nMYSQL_PASSWORD=$MYSQL_PASSWORD\nMYSQL_RANDOM_ROOT_PASSWORD=$MYSQL_RANDOM_ROOT_PASSWORD\nMYSQL_HOST=$MYSQL_HOST\nMYSQL_PORT=$MYSQL_PORT" > ~/vars.env'
                    sh 'docker run --rm -d --name dvna-mysql --env-file ~/vars.env mysql:5.7 tail -f /dev/null'
                    sh 'docker run --rm -d --name dvna-app --env-file ~/vars.env --link dvna-mysql:mysql-db -p 8080:8080 appsecco/dvna'
                    sh 'docker cp dvna-app:/app/ ~/'              
                }
            } 
            stage('NodeJsScan Analysis') {
                steps {
                    sh 'njsscan --json -o ~/reports/nodejsscan-report ~/app || true'
                }
            }

            stage('Auditjs Analysis') {
                steps {
                    sh 'cd ~/app; auditjs ossi > ~/reports/auditjs-report || true'
                }
            }

            stage ('OWASP Dependency-Check Analysis') {
                steps {
                    sh '~/dependency-check/bin/dependency-check.sh --scan ~/app --out ~/reports/dependency-check-report --format JSON --prettyPrint || true'
                }
            }
            stage('OWASP ZAP Analysis') {
                steps {
                    sh 'docker run --rm -i -u zap --name owasp-zap -v ~/reports/:/zap/wrk/ owasp/zap2docker-stable zap-baseline.py -t http://192.168.1.55:8080 -r zap-report.html -l PASS || true'
                }
            }

            stage ('Generating Software Bill of Materials') {
                steps {
                    sh 'cd ~/app && cyclonedx-bom -o ~/reports/sbom.xml'
                }
            }

            stage ('JSHint Analysis') {
                steps {
                    sh 'jshint $(find ~/app -type f -name "*.js" -o -name "*.ejs" | grep -v node_modules) > ~/reports/jshint-report || true'
                }
            }

            stage ('ESLint Analysis') {
                steps {
                    sh 'eslint -c ~/.eslintrc.json -f html --ext .js,.ejs -o ~/reports/eslint-report.html ~/app || true'
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
                    sh 'ssh -tt -o StrictHostKeyChecking=no prod-vm@192.168.1.55 "docker run -d --name dvna-app --env-file ~/vars.env --link dvna-mysql:mysql-db -p 8080:8080 appsecco/dvna"'
                }
            }

        }
    }