# Setting up VM
- Here I will be setting up two VMs:
    1. For Jenkins Deployment.
    2. For application(suiteCRM) server.

- In virtual Box Click "NEW" icon to ceate a new machine
- Create Virtual machine window opened
    The Name Jenkins-infra.
    The Type to Linux.
    Version to Ubuntu (64-bit).
    Allocated memory size(RAM).
- Clicked on Create
    Allocated the storage.
    Set the default disk type to VDI(VirtualBox Disk Image)
    Selected storage on physical hard disk Dynamically allocated.
    Clicked on Create.

## Download server image 18.04
- To download the server image 18.04 on VirtualBox as it is an LTS version which is a desirable feature for a CI pipeline.
     64-bit PC (AMD64) server install image
- In VM box I selected Jenkins-infra to install the server and then clicked on start
- Then the Select start-up disk window opened and I clicked on the folder which gave a new screen Optical Disk Selector. I selected the server image and clicked on Choose after that click on start

- later select the language English and click on done

- The proxy configured on this screen is used for accessing the package repository and the snap store both in the installer environment and in the installed system. I did not provide any Proxy address, kept it default and selected Done


- The installer will attempt to use GeoIP to lookup an appropriate default package mirror for your location. I kept this too as default and selected Done

- I do not have to make any changes to the storage configuration. So I selected Done and pressed the Enter button.

- Selected continue and pressed Enter to begin the installation.
- In Profile setup provided the required credentials and pressed Done
- In SSH window I selected the install SSh server becuase by default ubuntu didnot have SSH installed and then  hit Done
- If a network connection is enabled, a selection of snaps that are useful in a server environment is presented and can be selected for installation.After this, selected Done and pressed Enter.
- Once done hit done and you will get a window where you have to Click on reboot and the installation of ubuntu 18.04 is done

- Repeat the same process in order to create another VM for suiteCRM deployment where I have specified the server name as "suitecrm-deploy".