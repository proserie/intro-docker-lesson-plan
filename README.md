 Introduction to Docker
------

**What is it**

Docker is a tool designed to make it easier to create, deploy, and run applications by using containers.

**Getting Started**

###### Windows
1. [Download Docker Here](https://download.docker.com/win/beta/InstallDocker.msi)
2. Double Click the installer
3. Follow the install prompts, accept the licence, etc
4. Click Finish to launch Docker.
5. Docker starts automatically.

P.S. In windows you will most likely have to share your C:\ drive with the docker daemon so you can appropriately run containers.
To do this we click on the whale in our taskbar, go to settings, shared drives and click the checkbox next to your drive.

###### Mac OSX
1. [Download Docker Here](https://download.docker.com/mac/stable/Docker.dmg)
2. Double click the DMG 
3. Drag it into your apps?

###### Linux
1. `sudo yum install docker` (centos) or `sudo apt install docker` (ubuntu)

**Good to Know**

[Important Commands and Docker Nomenclature](docs/nomenclature.md)

**Lesson 1**

[Running Containers](docs/Running%20Containers.md)