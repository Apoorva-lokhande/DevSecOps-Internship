# Docker

- Docker is a software platform that allows you to build, test, and deploy applications quickly. Docker packages software into standardized units called containers that have everything the software needs to run including libraries, system tools, code, and runtime. Using Docker, you can quickly deploy and scale applications into any environment and know your code will run.I refered the [official documentation](https://docs.docker.com/get-started/overview/) to know more about the docker.

- Docker provides the ability to package and run an application in a loosely isolated environment called a container. Containers are lightweight and contain everything needed to run the application, so you do not need to rely on what is currently installed on the host. You can easily share containers while you work, and be sure that everyone you share with gets the same container that works in the same way.
  
## Install Docke Engine

- Update the`apt` package index, and install the latest version od\f Docker Engine and containerd:
        
        sudo apt-get update

        sudo apt-get install docker-ce docker-ce-cli         containerd.io

- ansd then on a production server, run:

        docker run --name dvna -p 9090:9090 -d appsecco/dvna:sqlite
