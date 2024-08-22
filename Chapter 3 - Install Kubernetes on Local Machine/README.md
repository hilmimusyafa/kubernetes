## Chapter 3 : Install Kubernetes on Local Machine

In this chapter, we will show you how to install Kubernetes on your device. To install Kubernetes on your laptop or device is to use these 2 applications:

### 1.1 Docker Desktop

You can run Kubernetes on your Laptop or device by using the features provided by docker directly, Oh yes, the steps below are for the Windows Operating System, for other OS can be adjusted to other tutorials on the internet. Here are the steps : 

1. Download File Installer Docker Desktop

    Go to the official Docker page [(Docker Official Page)](https://www.docker.com/products/docker-desktop/) and download the Docker Desktop installer, make sure it's for Windows, as shown in the picture :
![image](https://hackmd.io/_uploads/BJ-T6AxoC.png)

2. Install Docker Dekstop

   Install according to the provisions and descriptions on the display using the downloaded `.exe` installer file.

    > *In this step, please adjust to the installation menu, there is no specialization for Kubernetes*

3. Open Docker Desktop Settings and Enable Kubernetes

    ![image](https://hackmd.io/_uploads/HJiuGuZo0.png)

    Go to the Kubernetes section and make sure it is checked : 
    ![image](https://hackmd.io/_uploads/SJVsMdWiC.png)

4. Wait for the Kubernetes Installation Process

5. Once done, return to the main menu and Kubernetes is installed.

    ![image](https://hackmd.io/_uploads/rJ92zOZiA.png)

### 1.2 Minikube (Requires VirtualBox and Hyper-V)

Minikube is highly recommended for Mac and Linux users, but is also available for Windows, and is similar to kubernetes on Docker Desktop but is accessed via Terminal or Command Prompt, you can follow these steps: 

1. Download the installer via GitHub or their Official Website.

2. Install using the downloaded installer file, and make sure to pay attention to the conditions and settings of the installation process and method.

3. Once installed, open minkube with Command Prompt: 

   ```
   $ minikube start
   ```

### 1.3 Linux Virtual Machine with Minikube

This method is actually for the case of Windows users who want to use Minkube powerfully or similar to the environment when deploying applications, and the benefit of using a virtual machine directly does not damage your Windows (if something happens). And this documentation will fully use this method in the future. To use VM in Minikube, make sure to do the following steps:

1. Make sure you have VirtualBox

   This is clear, because to set up and place the Virtual Machine, we use the lightweight and free VirtualBox. Download it on [the official site](https://www.virtualbox.org/wiki/Downloads) and make sure it is installed properly.
   
2. Also download ISO Image of Linux OS

   Here, for the Linux that you want to install, you can do it as you wish. But from the author, I will use Ubuntu Server 24.04 LTS as well as the tutorial in the future. Please be able to adjust.
   
3. Install Virtual Machine with the downloaded Linux Image, install it in VirtualBox with the configuration as below : 

    - CPU : 2
    - Memory : 3 GB
    - HardDrive : 25 GB
    - Network : 1x NAT and 1x Host Only
    
    The above configuration is based on the author, if there are additional or different configurations, you can adjust them.
    
4. If Linux is installed and ready, do an update and upgrade with the following command: 

    ```bash
    $ sudo apt update && sudo apt upgrade -y
    ```
    If you have done a reboot: 
    ```bash
    $ sudo reboot
    ```

5. Install Docker to help Kubernetes

    ```bash
    $ sudo apt install ca-certificates curl gnupg wget apt-transport-https -y
    $ sudo install -m 0755 -d /etc/apt/keyrings
    $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    $ sudo chmod a+r /etc/apt/keyrings/docker.gpg
    $ echo \
      "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
      "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
      sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    $ sudo apt update
    ```
    Install Docker with the latest version
    ```
    $ sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    ```
    Add your local user to docker group so that your local user run docker commands without sudo.

    ```
    $ sudo usermod -aG docker $USER
    $ newgrp docker
    ```
    Verify docker service is running.
    ```
    $ sudo systemctl status docker
    ```

6. Install Minikube

    To download and install minikube binary, run following commands.

    ```
    $ curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
    $ sudo install minikube-linux-amd64 /usr/local/bin/minikube
    ```

    Check minikube version.

    ```
    $ minikube version
    ```

7. Install kubectl tool

    Kubectl is a command line tool, used to interact with your Kubernetes cluster.

    ```
    $ curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
    ```

    Next, set the executable permission on it and move to /usr/local/bin

    ```
    $ chmod +x kubectl
    $ sudo mv kubectl /usr/local/bin/
    ```

    Verify the kubectl version.

    ```
    $ kubectl version -o 
    ```

8. Start Minikube cluster

    Now that Minikube is installed, start a Kubernetes cluster using the following command.

    ```
    $ minikube start --driver=docker
    ```

    After minikube has started, verify the status of your cluster.
    ```
    $ minikube status
    ```
