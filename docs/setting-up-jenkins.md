# Installation of jenkins
- What is jenkins?
    Jenkins is a self-contained, open source automation server which can be used to automate all sorts of tasks related to building, testing, and delivering or deploying software.

# Prerequisite
- I have setup ubuntu 18.04 VM for installing Jenkins: https://www.jenkins.io/doc/book/installing/

- I have installed java 8 OpenJDK and JRE(Java Developement Kit and Java Runtime Environment) which is used to develop and run the software: https://www.digitalocean.com/community/tutorials/how-to-install-java-with-apt-on-ubuntu-18-04#installing-specific-versions-of-openjdk

# Installation steps for Jenkins

- Add the repository key to the system: wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
![image](/pictures/jenk.png)
- the system will return OK

- Next, add the debian package repository address: sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'

- sudo apt update
![image](/pictures/last.png)

- Now install Jenkins and it's dependencies: sudo apt install jenkins
![image](/pictures/installed.png)

# Starting Jenkins
![image](/pictures/startandstatus.png)
- The systemctl command is used to manage "systemd" services and service manager: sudo systemctl start jenkins
- Check the status of jenkins service using the below command: sudo systemctl status jenkins
    If the jenkins has installed succesfully, then the output will show as Active: active(excited).To reach it from a web browser I will adjust the firewall rules to complete the initial setup.

# Set-up a Firewall with UFW
![image](/pictures/activee.png)
- Firewall is a software controlling incoming and outgoing network traffic. Firewall is able to manage traffic by monitoring network ports.
- By default, Jenkins runs on port 8080. Opening using ufw(uncomplicated firewall): sudo ufw allow 8080
- To check the status of the ufw: sudo ufw status
- If the status shows "Inactive". Then enable using following command
    To configure your server to allow incoming SSH connections, you can use this command:sudo ufw allow ssh
    - To enable UFW, use this command: sudo ufw enable

# Setting up Jenkins
- To complete setup in the browser I entered http://your_server_name_or_domain:8080
![image](/pictures/unlock.png)
- The Unlock Jenkins screen opens, which will display where the Admin Password is stored.
![image](/pictures/cat.png)
- In the terminal I will use the cat command to display the passsword: sudo cat /var/lib/jenkins/secrets/initialAdminPassword
- The 32-character alphanumeric password is displayed in the terminal paste it onto Administrator password field, and then click continue.
![image](/pictures/costumize.png)
- In the Customize jenkins select "install suggested plugins" which will start installtion process directly and press continue.
![image](/pictures/info.png)
- Add the required credentials, CLick on save and continue as admin.
- The "Instance configuration" page will be displayed which will ask to confirm the prefered URL for jenkins instance, Click on save and finish.
![image](/pictures/started.png)
- Once the process is over click on "Reboot" which will restart the jenkins.

