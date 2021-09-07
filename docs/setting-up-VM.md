# What is Virtual Machine? 

For understanding Virtual machine (VM) we will have to understand what Virtualization is. 

- **Virtualization** is a software based or virtual version of something, that be compute, storage, networking, servers or applications. And Hypervisor makes Virtualization possible. 

- **Hypervisor** is a piece of software that runs above host or server. 

- There are 2 types of hypervisors: 

    1. Type 1: It is installed directly on top of physical server. 

    2. Type 2: Here there is a layer of Host operating system (OS) between the hardware and Hypervisor (as we are using here). 

A VM is an OS or application environment that is installed on software, which imitates dedicated physical server. A VM provides an isolated environment for running its own OS and applications independently from the underlying host system or from other VMs on that host. The VM's OS is commonly referred to as the guest OS, and it can be the same as or different from the host OS or the other VMs. 
   
## Setting up VM
Here I will be setting up two VMs:

1. For Jenkins Deployment.
2. For suiteCRM server.

In virtual Box Click `NEW` icon to create a new machine.

## Name and operating system 

![image](/pictures/os.png)

- Name : `Jenkins-deploy`
- Type : `Linux`
- Version : `Ubuntu (64-bit)`
## Memory size 

![image](/pictures/2.png) 

Select `2000 MB` and click on `create`.

## Hard disk 

![image](/pictures/3.png) 

Select a `virtual hard disk now` and `Create` and Hard disk file type will open in that select `VDI (VirtualBox Disk Image) `and `NEXT`. 

![image](/pictures/js.png) 

In storage on physical hard disk select `Dynamically allocated` File location and size will open hit `Create` and is done. 

![image](/pictures/2021-06-29_19-41.png) 

## Download server image 18.04 

Downloaded the server image Ubuntu 18.04 on VirtualBox as it is an LTS version which is a desirable feature for a CI pipeline.  

I followed the official [link](https://releases.ubuntu.com/18.04/) to download the 64-bit PC(AMD64) desktop image  

![image](/pictures/desktop.png) 

In VM box I selected `Jenkins-deploy` to install the server and then clicked on `start`.

![image](/pictures/mark.png) 

Then the Select start-up disk window opened and I clicked on the folder which gave a new screen Optical Disk Selector. I selected the server image and clicked on Choose after that click on `start`.

![image](/pictures/optical.png) 

![image](/pictures/8.png) 

Now Jenkin VM starts running! 

## Language selection 

Select the language English and click on done 

![image](/pictures/9.png) 

## Keyboard configuration 

By default, English is selected for Layout and variant, pressed `Done`. 

![image](/pictures/10.png) 

## Network connections 

I kept it as default and selected `Done`. 

![image](/pictures/11.png) 

## Configure proxy  

![image](/pictures/12.png) 

## Configure ubuntu archive mirror 

![image](/pictures/13.png) 

I kept it as default and pressed Done for both Configure proxy and configure ubuntu archive mirror  

## Storage configuration 

![image](/pictures/14.png) 

I did not make changes in the storage configuration I kept it as default and pressed `Done`. 

![image](/pictures/15.png) 

I kept this too as default and hit `Done`. 

## Profile setup 

![image](/pictures/17.png) 

I provided all the details and pressed `Done`. 

## SSH Setup 

![image](/pictures/18.png) 

In order to install the OpenSSH server I pressed Space button to select the `Install OpenSSH server` option. I selected the install openSSH server because by default ubuntu did not have openSSH installed, and then I kept Import SSH identity as default and pressed `Done`. 

## Featured Server Snaps 

![image](/pictures/19.png)  

Selected the snaps which are useful in a server environment, once selected pressed `Done`. 

![image](/pictures/20.png) 

## Installation complete 

Installation complete window appeared, I waited until the **Reboot** button appeared on this window. Later, pressed `reboot`. 

![image](/pictures/21.png) 

And the installation of Ubuntu 18.04 is completed! 

Repeat the same process in order to create another VM for suiteCRM deployment where I have specified the server name as `suitecrm-deploy`. 

 
 
 

 