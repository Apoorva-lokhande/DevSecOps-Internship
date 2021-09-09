# Software Code Quality Analysis
## Objective

The aim of this section is to perform linting checks on the source code of DVNA and generate a code quality report to provide a solution to the 9th point of the [Problem Statement](https://devsecops-report.netlify.app/problem-statements/).
## Source Code Linting

Lint, or a linter, is a source code analysis tool used to flag programming errors, bugs, stylistic errors, and suspicious constructs.

Linter's are used to generate reports about the code quality by either invoking a built-in functionality or writing a reporting wrapper arround the tool to resolve the identified issues.


## Linting tools for DVNA

DVNA is a Nodejs application and hence, I used [JSHint](https://jshint.com/install/) and [ESLint](https://eslint.org/docs/2.13.1/user-guide/command-line-interface) as the linter.

JSHint scans your program's source code and reports about commonly made mistakes and potential bugs. The potential problem could be a syntax error, a bug due to implicit type conversion, a leaking variable, or any of the other problems that JSHint looks for and it is a command-line utility, I can easily integrate it into my CI pipeline with Jenkins. 

### Integrating JSHint with Jenkins pipeline

- I installed it globally with NPM using the following command:

        sudo npm install -g jshint


- Run the jshint command with the aplication/project directory to scan all the `.js` files as given below:
  
        jshint ~/app > ~/report/jshint-report

- The above command will scan all the files in the directory. To restrict the scan to only .js and .ejs files, use a find command. We will further exclude all files in the `node_modules` directory.

        jshint $(find ~/app -type f -name "*.js" -o -name "*.ejs" | grep -v node_modules) > ~/reports/jshint-report
    
#### JSHint Pipeline

    stage ('JSHint Analysis') {  
        steps {
            sh 'jshint $(find ~/app -type f \( -name "*.js" -o -name "*.ejs" \) | grep -v node_modules) > ~/reports/jshint-report || true'
        }
    }

### ESLint

ESLint is a static code analysis tool for identifying problematic patterns found in JavaScript code. ESLint covers both code quality and coding style issues.Unlike JSHint, ESLint uses Espree for JavaScript parsing and an AST to evaluate patterns in code. It's completely pluggable, every single rule is a plugin and you can add more at runtime.

I followed the [official documentation](https://eslint.org/docs/user-guide/getting-started) to install ESLint using NPM:

    npm install -g eslint 

Now, to run the scan eslint requires a .eslintrc configuration file which contains environment variables, rules and other configuration details. To create this file, run `eslint --init` in the project's root folder you will be prompted with a few questions to create the config file, and the responses are as shown below:

    ✔ How would you like to use ESLint? · problems
    ✔ What type of modules does your project use? · commonjs
    ✔ Which framework does your project use? · none
    ✔ Does your project use TypeScript? · No
    ✔ Where does your code run? · browser
    ✔ What format do you want your config file to be in? · JSON

***NOTE***: The `.eslintrc.json` config file is required everytime I run an eslint scan on a file or directory. It's not possible to create the config file by running eslint --init as the command line waits for responses to file configuration questions. To resolve this problem, copy the configuration details shown above into `<Jenkins-Home-Dir>/.eslintrc.json` file. You can now specify eslint to use this config file during its execution.

The contents of the `.eslintrc.json` file are as below:

    {
        "env": {
            "browser": true,
            "commonjs": true,
            "es2021": true
        },
        "extends": "eslint:recommended",
        "parserOptions": {
            "ecmaVersion": 12
        },
        "rules": {
        }
    }

To perform an eslint scan, run eslint command with the following flags:

- `-c`  to specify the config file.
- `-f` to format of output report.
- `--ext` to specify the extensions of files to be scannned.
- `-o` to specify file to write the report to a particular file. 

        
      eslint -c ~/.eslintrc.json -f html --ext .js,.ejs -o ~/reports/eslint-report.html ~/app

#### ESLint Pipeline

Finally, I added a stage in the pipeline to run the script after I made it executable. The stage that I added was as follows:

    stage ('ESLint Analysis') {
        steps {
            sh 'eslint -c ~/.eslintrc.json -f html --ext .js,.ejs -o ~/reports/eslint-report.html ~/app || true'
        }
    }


