# AWX Installation
This is a short step-by-step guide how to install AWX on RHEL 7.6

## Hardware requirements
The system that runs the Ansible AWX service will need to satisfy the following requirements:
-  RAM: at leasts 4GB of memory.
-  CPU: at least 2 cpu cores. 
-  HDD: at least 20GB of space.
-  Running Docker, Openshift, or Kubernetes.

*If you choose to use an external PostgreSQL database, please note that the minimum version is 9.4.*

## Pre-requisites
Make sure that SELinux are disable
```
sed -i 's|SELINUX=enforcing|SELINUX=disabled|g' /etc/selinux/config
reboot
```
Make sure your system is up to date
```
yum -y update 
```
## Install the dependency package
Install EPEL Repository and all the dependecy packages required for AWX.
```
yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
yum makecache
yum install -y git gcc gcc-c++ lvm2 bzip2 gettext nodejs yum-utils device-mapper-persistent-data ansible python-pip
```
Add Docker-CE repository to he server and install docker-ce
```
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum makecache 
yum -y install docker-ce
systemctl start docker && systemctl enable docker && systemctl status docker
pip install -U docker-py
```
## Install Ansible AWX
Clone AWX from official repository and move to install directory
```
git clone --depth 50 https://github.com/ansible/awx.git
cd awx/installer/
```
Edit `inventory` file before installation
First, you need to know installation parameters
```
grep -v '^ *#' inventory | sed '/^$/d'
```
Change  admin password
```
sed -i 's|admin_password=password|admin_password=YOUR_PASSWORD|g' inventory
```
Get a new secret_key
```
openssl rand -base64 30
```
Change the secret_key
```
sed -i 's|secret_key=awxsecret|secret_key=YOUR_NEW_SECRET_KEY|g' inventory 
```
Check new parameters
```
grep -v '^ *#' inventory | sed '/^$/d'
```
Install with pip docker-compose
```
pip uninstall docker docker-py docker-compose
pip install docker-compose
```
Start installation
```
ansible-playbook -i inventory install.yml
```
After the installation is complete, run the following command to display the created containers. If everything is OK then 5 containers are created correspondingly as below.
```
docker ps
```
Open a web browser and access to AWX GUI
