# Setting up VMs

## Objective

This section aims to set up the required infrastructure to perform the task and solve the 1st point of the [Problem Statement](https://devsecops-report.netlify.app/problem-statements/).

## What is Virtual Machine?
For understanding Virtual machine (VM) we will have to understand what Virtualization is. 

- **Virtualization** is a software-based or virtual version of something, that computes, storage, networking, servers, or applications. And Hypervisor makes Virtualization possible. 

- **Hypervisor** is a piece of software that runs above the host or server. 

    - There are 2 types of hypervisors: 

        1. Type 1: It is installed directly on top of a physical server. 

        2. Type 2: Here there is a layer of Host operating system (OS) between the hardware and Hypervisor (as we are using here). 

A VM is an OS or application environment that is installed on software, which imitates a dedicated physical server. A VM provides an isolated environment for running its OS and applications independently from the underlying host system or from other VMs on that host. The VM's OS is commonly referred to as the guest OS, and it can be the same as or different from the host OS or the other VMs. 
   
## Setting up VM
Here I will be setting up two VMs:

1. For Jenkins Deployment.
2. For the DVNA server.

In virtual Box Click on the `NEW` icon to create a new machine. Later follow the steps as given below:

### Name and operating system 

![image](/pictures/os.png)

- Name : `Jenkins-deploy`
- Type : `Linux`
- Version : `Ubuntu (64-bit)`
### Memory size 

![image](/pictures/2.png) 

Select `2000 MB` and click on `create`.

### Hard disk 

![image](/pictures/3.png) 

Select a `virtual hard disk now` and `Create` and `Hard disk file type` will open in that select `VDI (VirtualBox Disk Image) `and `NEXT`. 

![image](/pictures/js.png) 

In storage on physical hard disk select `Dynamically allocated` file location and size will open hit `Create` and is done. 

![image](/pictures/2021-06-29_19-41.png) 

### Download server image 18.04 

Downloaded the server image Ubuntu 18.04 on VirtualBox as it is an LTS version which is a desirable feature for a CI pipeline.  

I followed the official [link](https://releases.ubuntu.com/18.04/) to download the 64-bit PC(AMD64) desktop image.  

![image](/pictures/desktop.png) 

In the VM box, I selected `Jenkins-deploy` to install the server and then click on `start`.

![image](/pictures/mark.png) 

Then the `Select start-up disk` window opened and I clicked on the folder which gave a new screen `Optical Disk Selector`. I selected the server image and clicked on `Choose`.

![image](/pictures/optical.png) 

Here, I clicked on `start`. Now Jenkins VM starts running! 

![image](/pictures/8.png) 

### Language selection 

After successfully installation the `language selection`  dialogue box will open and here select the language `English` and click on `done`.

![image](/pictures/9.png) 

### Keyboard configuration 

By default, English is selected for Layout and variant, clicked on  `Done`. 

![image](/pictures/10.png) 

### Network connections 

I kept it as default and selected `Done`. 

![image](/pictures/11.png) 

### Configure proxy  
The proxy configured on this screen is used for accessing the package repository and the snap store both in the installer environment and in the installed system. I did not provide any Proxy address, kept it default, and selected `Done`.
![image](/pictures/12.png) 

### Configure Ubuntu archive mirror 
The installer will attempt to use GeoIP to look up an appropriate default package mirror for your location. I kept this too as default and selected `Done`.

![image](/pictures/13.png) 

### Storage configuration 

I did not make changes in the storage configuration I kept it as default and clicked on `Done`. 

![image](/pictures/14.png) 


I kept this too as default and hit `Done`. 


![image](/pictures/15.png) 

### Profile setup 
I provided all the details and clicked on `Done`. 
![image](/pictures/17.png) 

### SSH Setup 
To install the OpenSSH server I selected the `Install OpenSSH server` option because by default ubuntu won't have openSSH installed, and then I kept `Import SSH identity` as default and clicked on `Done`. 
![image](/pictures/18.png) 

### Featured Server Snaps 
Selected the snaps which are useful in a server environment, once selected click on `Done`.
![image](/pictures/19.png)   

### Installation complete 

Installation complete window appeared, wait until the **Reboot** button appears on this window. Later, select `reboot`. 

![image](/pictures/21.png) 

And the installation of Ubuntu 18.04 is completed! 

Repeat the same process to create another VM for DVNA deployment where I have specified the server name as `DVNA-deploy`. 

 
 
 

 