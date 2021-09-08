# What is SBoM?

*** Objective***

The aim of this section is to perform dynamic analysis using DAST tools on DVNA and solve the 6th point of the [Problem Statement](https://devsecops-report.netlify.app/problem-statements/).

A software bill of materials is a list of all the open source and third-party components present in a codebase. A Bill of Materials is a list of components used to assemble/create a product. It gives out a specification about how each component was used in the making of the end product.


## CycloneDX

To generate the SBoM for DVNA, we are using a tool called CycloneDX. According to the [documentation](https://github.com/CycloneDX/cyclonedx-node-module#cyclonedx-nodejs-module) CycloneDX is a module for Node.js creates a valid CycloneDX SBoM containing an aggregate of all project dependencies.

SCA tools can discover all related components, their supporting libraries, and their direct and indirect dependencies. SCA tools can also detect software licenses, deprecated dependencies, as well as vulnerabilities and potential exploits. The scanning process generates a component inventory or  [software bill of materials (SBoM)](https://www.synopsys.com/blogs/software-security/software-bill-of-materials-bom/), providing a complete inventory of a project’s software assets which is then compared against a variety of databases, these databases hold information regarding known and common vulnerabilities.

## Generating SBoM for DVNA

Here, I installed CycloneDX's Node module with NPM by using the command:

    sudo su
    npm install -g @cyclonedx/bom

Later, I generated SBoM report in an `.xml` format(you can either generate using `.xml` or `.json`) also use `-o` to specify filename and format(i.e., .xml)

    cyclonedx-bom -o sbom.xml

## SBoM pipeline


Lastly, I added a stage in the pipeline to run CycloneDX and store the SBoM (sbom.xml) in the local reports folder:

    stage ('Generating Software Bill of Materials') {
        steps {
            sh 'cd ~/app && cyclonedx-bom -o ~/reports/sbom.xml'
        }
    }