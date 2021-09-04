# Configuring Production VM

The aim of this section is to setup the production server for deploying DVNA.

Damn Vulnerable NodeJS Application (DVNA) is a simple NodeJS application to demonstrate [OWASP Top 10 Vulnerabilities](https://owasp.org/www-project-top-ten/2017/) and guide on fixing and avoiding these vulnerabilities

## Requirements

To serve DVNA, there are some prerequisites:
- VM running Ubuntu 18.04 LTS.
- Docker, NodeJS and NPM
  
## Install Docker, NodeJS and NPM

- For Docker installation I referred the [official documentation](https://docs.docker.com/engine/install/), update the `apt` package index:

      sudo apt-get update

- Add Docker’s official GPG key:

  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
  
- Use the following command to set up the stable repository:

  echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

- Update the apt package index, and install the latest version of Docker Engine and containerd, or go to the next step to install a specific version:

  sudo apt-get update
  sudo apt-get install docker-ce docker-ce-cli containerd.io
  sudo docker run hello-world # Test if docker installation is successful 

- Install NodeJS and NPM:

      sudo curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash - &&
      sudo apt install -y nodejs 

## Setup DVNA

- I followed the [official documentation](https://github.com/appsecco/dvna/blob/master/docs/setup.md) for setup.
- Create a file named vars.env with the following configuration:

        MYSQL_USER=dvna
        MYSQL_DATABASE=dvna
        MYSQL_PASSWORD=passw0rd
        MYSQL_RANDOM_ROOT_PASSWORD=yes
        MYSQL_HOST=mysql-db
        MYSQL_PORT=3306

- Start a MySQL container, unless you want to use your own, in which case configure in the env file above.

        docker run --name dvna-mysql --env-file vars.env -d mysql:5.7

- Start the application using the official image:

        docker run --name dvna-app --env-file vars.env --link dvna-mysql:mysql-db -p 9090:9090 appsecco/dvna

- To test if the containers are running, run `docker ps -a` you will see two containers running `dvna-app` and `dvna-mysql` and `docker stop` for stopping the container.
- Access the application at http://your-ip-address:8080.
  