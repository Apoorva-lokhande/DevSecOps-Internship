## Requirements

I've referred the official [documentation](https://github.com/appsecco/dvna) for installation of DVNA.

- Setup without Docker:
  - NodeJS (Developed using NodeJS v6.11.4)
  - MySQL Server (Developed using MySQL 5.7)
  
## Setup

-  For setup you need [NodeJS](https://nodejs.org/en/download/package-manager/) setup on your system and access to a [MySQL](https://dev.mysql.com/doc/mysql-getting-started/en/) database.
-  Once done with installing NodeJS and MySql, I cloned the repository into VM and configured the environment variables with database information as shown below:
  
       git clone https://github.com/appsecco/dvna; cd dvna

       export MYSQL_USER=dvna
       export MYSQL_DATABASE=dvna
       export MYSQL_PASSWORD=passw0rd
       export MYSQL_HOST=127.0.0.1
       export MYSQL_PORT=3306

- Now, I installed the dependencies and started the application:
      
      npm install

      npm start

- But, while installing NPM I got an error because I had installed latest version of MySQL and NodeJS, ignoring the requirements mentioned. 
- I uninstalled the latest version's of each and installed MySQL 5.7:
    
      sudo apt update

      sudo apt install wget -y

- Once downloaded, install the repository by running the below command:
            
      wget https://dev.mysql.com/get/mysql-apt-config_0.8.12-1_all.deb        

- In the prompt choose `ubuntu bionic` and in the next prompt choose `MySQL server and cluster` and select the version `MySQL 5.7` hit `tab` and `enter`.
- Now, run the below command to update your system packages:
        
      sudo apt-get update
      sudo apt-cache policy mysql-server

- Having found MySQL 5.7 in our system, we are going to install MySQL 5.7 client, MySQL 5.7 server with the below command:

      sudo apt install -f mysql-client=5.7* mysql-community-server=5.7* mysql-server=5.7*

- And, yet again I checked running NPM. It gave me an error saying  `MySQL ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)`, beacuse I had not created user and database in MySQL.
- Firstly, connected yo MySQL with the set root password and run the below command:

      mysql -u root -p 

- While still connected to MySQL, run the following commands to create a user and database:

      CREATE USER 'user'@'%' IDENTIFIED BY 'MyStrongPass.';
      GRANT ALL PRIVILEGES ON * . * TO 'user'@'%';  
      CREATE DATABASE;
      FLUSH PRIVILEGES;

- Now, I tried acesssing the application from http://localhost:9090 and it worked !