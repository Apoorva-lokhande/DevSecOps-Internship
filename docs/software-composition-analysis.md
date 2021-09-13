# Software composition analysis

## Objective

This section aims to explain the software composition of DVNA using SCA tools in a Jenkins pipeline and solve the 7th point of the [Problem Statement](https://devsecops-report.netlify.app/problem-statements/).

## What is SCA
Software composition analysis (SCA) identifies all the open sources in a codebase and maps that inventory to a list of current known vulnerabilities. It helps identify vulnerabilities in open source code (dependencies) used in code.

SCA is the process of automating the visibility into open source software (OSS) use for risk management, security, and license compliance. With the rise of open-source (OS) use in software across all industries, the need to track components increases exponentially to protect companies from issues and open source vulnerabilities.
### OWASP Depedency-Check
Dependency-Check is a Software Composition Analysis (SCA) tool that attempts to detect publicly disclosed vulnerabilities contained within a project’s dependencies. It does this by determining if there is a Common Platform Enumeration (CPE) identifier for a given dependency. If found, it will generate a report linking to the associated CVE entries.
### Performing SCA in DVNA
I followed the [official documentation](https://github.com/jeremylong/DependencyCheck) to download the Dependency-Check CLI and the associated GPG signature file which is given below:

    wget -P ~/ https://github.com/jeremylong/DependencyCheck/releases/download/v6.2.2/dependency-check-6.2.2-release.zip

    wget -P ~/ https://github.com/jeremylong/DependencyCheck/releases/download/v6.2.2/dependency-check-6.2.2-release.zip.asc

Now, extract the files from the `dependency-check` tool zip file:

    unzip ~/dependency-check-6.2.2-release.zip

Perform the scan by specifying the path to the project, output report format, and its location:

    ~/dependency-check/bin/dependency-check.sh --scan ~/app --out ~/report/dependency-check-report --format JSON --prettyPrint

### SCA Pipeline
    
Finally, I added the script to the Jenkins file to perform SCA of DVNA:

    stage ('OWASP Dependency-Check') {
        steps {
        sh '~/dependency-check/bin/dependency-check.sh --scan ~/app --out ~/reports/dependency-check-report --format JSON --prettyPrint || true'
        }
    }

***NOTE***: While adding the script to the pipeline I got an error:
![image](pictures/error2.png)

It was a DNS error, I went ahead and resolved it by initializing the following command in the `<jenkins-home-dir>`:

    sudo dhclient enp0s3

The complete report generated can be found [here](https://github.com/Apoorva-lokhande/DevSecOps-internship/blob/master/reports/depedency-check-report.json).