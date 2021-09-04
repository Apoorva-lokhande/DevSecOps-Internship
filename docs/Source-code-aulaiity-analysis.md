# Source Code Quality Analysis

## Source Code Linting

Lint, or a linter, is a source code analysis tool used to flag programming errors, bugs, stylistic errors, and suspicious constructs.

Linter's are used to generate reports about the code quality by either invoking a built-in functionality or writing a reporting wrapper arround the tool to resolve the identified issues.


## Linting tools for DVNA

DVNA is a Nodejs application and hence, I used [JSHint](https://jshint.com/install/) and [ESLint](https://eslint.org/docs/2.13.1/user-guide/command-line-interface) as the linter.

JSHint scans your program's source code and reports about commonly made mistakes and potential bugs. The potential problem could be a syntax error, a bug due to implicit type conversion, a leaking variable, or any of the other problems that JSHint looks for and it is a command-line utility, I can easily integrate it into my CI pipeline with Jenkins. 

## Integrating JSHint with Jenkins pipeline

- I installed it globally with NPM using the following command:

    sudo npm install -g jshint


- Run the jshint command with the aplication/project directory to scan all the .js files as given below:
  
    jshint ~/dvna > ~/report/jshint-report

- To restrict the scan to only .js and .ejs files, use a find command. We will further exclude all files in the `node_modules` directory.

        jshint $(find ~/app -type f -name "*.js" -o -name "*.ejs" | grep -v node_modules) > ~/reports/jshint-report
    
## JSHint Pipeline

## ESLint

ESLint is a static code analysis tool for identifying problematic patterns found in JavaScript code. ESLint covers both code quality and coding style issues.

