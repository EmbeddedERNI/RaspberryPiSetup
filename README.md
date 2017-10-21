# Raspberry Pi Setup
This repository contains all the binaries that you may need along with all the instructions to setup the raspberry pi.

## Download this repository
In the overview tab there is a button just bellow the repository name that allows you to download the full repository as a package.

## Install git client
You are free to use any git client of your choice if you are familiar with it. we will be using GitKraken.

### Windows SSH
- Run the installer in the Binary folder
- Open preferences > Authentication  and click in "Generate" button to generate a key.
- Copy the public key and add it to your profile in GitLab.

### Linux SSH
- Instal the .deb package.
- Use ssh-keygen command to generate a key.
- Add the key to GitKraken in preferences > Authentication.
- Add the public key to your GitLab profile.

## Connect SSH 
If you are familiar with ssh, you can use any client of your choice, we will use MobaXTerm for windows.

- Launch MobaXTerm portable from binaries.
- Get your RPI IP Address, we will tell to each group the right one.
- Create a new session (Top left) using the IP and one of the following users with password "frambuesa"
    - pi
    - pi2
    - pi3

At this point you should be able to get full control of your RPI.

## Connect VNC
This allows you to have an screen in the RPI.

- Install RealVNC from binaries.
- For user pi, the virtual desktop already exist, for other users you have to launch it with the following command

```
vncserver -geometry 800x600
```
*you can specify a different resolution if you want. keep in mind that you will be up to 3 people in the same device.*

- keep note of the screen, in the case is :1

```
New desktop is raspberrypi:1 (192.168.0.159:1)
```
- Create the connection with RealVNC using the ip from the previous command.
- Introduce your user and password *pi, pi2 or p3 and "frambuesa"*

At this point you should be able to use the rasspberry pi as a normal desktop computer.






