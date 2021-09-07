***Objective***

The aim of this section is to explain the software composition of DVNA using SCA tools in a Jenkins pipeline and solve the 5th point of the [Problem Statement](https://devsecops-report.netlify.app/problem-statements/).

# What is SCA?

Software composition analysis (SCA) is an automated process that identifies the open source software in a codebase. This analysis is performed to evaluate security, license compliance, and code quality.

In a modern DevSecOps environment, SCA has galvanized the [shift left](https://devexperts.com/shift-left-paradigm/) paradigm. Earlier and continuous SCA testing has enabled developers and security teams to drive productivity without compromising security and quality.

## How does SCA work?

SCA tools can discover all related components, their supporting libraries, and their direct and indirect dependencies. SCA tools can also detect software licenses, deprecated dependencies, as well as vulnerabilities and potential exploits. The scanning process generates a component inventory or  [software bill of materials (SBoM)](https://www.synopsys.com/blogs/software-security/software-bill-of-materials-bom/), providing a complete inventory of a project’s software assets which is then compared against a variety of databases, these databases hold information regarding known and common vulnerabilities.

## Software bill of materials

There are four levels of details for SBoMs:

- Licenses
- Modules
- Patch levels
- Backports

Most of the discussion about SBoMs is roughly at the license level





## CycloneDX

To generate the SBoM for DVNA, we are using a tool called CycloneDX. According to the [documentation](https://github.com/CycloneDX/cyclonedx-node-module#cyclonedx-nodejs-module) CycloneDX is a module for Node.js creates a valid CycloneDX SBoM containing an aggregate of all project dependencies.


## Installation

Here, I installed CycloneDX's Node module with NPM by using the command:

    npm install -g @cyclonedx/bom

Later, I ran CycloneD
