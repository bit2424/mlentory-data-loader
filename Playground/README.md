# Playground structure

In this folder you will find the code needed to experiment with the tool we later used in production 

## Run the project

We use a docker based tools to run the project.

If you are in machine with a Unix based operating system you just need to install the Docker and Docker Compose services.

If you are in Windows I recommend installing the Windows subsystem for Linux (WSL 2) and install Ubuntu 20.04. The idea is to have a Linux machine inside Windows so that everything can run smoothly. Particularly when working with machine learning libraries using the Windows service for Docker can become troublesome.

After that you'll have to install the CUDA toolkit and the Nvidia drivers to run ML software in your GPUs.

### Setting up Docker on Linux

For Linux distribution like Ubuntu, Debian, CentOS, etc, we do the following:

1. Update your existing list of packages:
``` console
sudo apt update
```

2. Install a few prerequisite packages which let apt use packages over HTTPS:
``` console
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```


3. Add the GPG key for the official Docker repository:
``` console
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```


4. Add the Docker repository to APT sources:
``` console
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
```


5. Update the package database with the Docker packages:
```
sudo apt update
```


7. Install Docker:
```
sudo apt install docker-ce
```


8. Verify the installation:
```
sudo docker run hello-world
```


### Manage Docker as Non-root User

If you don't want to write sudo before every command, do the following: 

1. Create the docker group if it does not exist:
```
sudo groupadd docker
```
2. Add your user to the docker group:
```
sudo usermod -aG docker ${USER}
```
3. Log out and log back in for changes to take effect.

4. Verify you can run Docker commands without sudo:
```
docker run hello-world
```

### Install Docker compose

1. Run this command to download the latest version of Docker Compose:
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
2. Apply executable permissions to the binary:
```
sudo chmod +x /usr/local/bin/docker-compose
```
3. Verify the installation:
```
docker-compose --version
```

### Setup NVIDIA GPUs

You can follow the guide at https://docs.nvidia.com/cuda/wsl-user-guide/index.html if you want to setup the NVDIA GPUs in your WSL.

But in general you have to guarantee that you have the GPU drivers, the NVIDIA container toolkit, and you have CUDA install.

If you are using Windows with WSL you have to install the GPU drivers in Windows.
You can check the NVIDIA GPU drivers at: https://www.nvidia.com/Download/index.aspx

The NVIDIA container toolkit has to be installed in the Linux distribution. The links for installation can be found here: https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html#installing-with-apt

If you don't have CUDA drivers install to use your GPU for ML development you can follow the instructions here: 
https://developer.nvidia.com/cuda-downloads

### Run the project

1. You need to build the images for the containers in project

```
docker-compose build
```

2. Create the containers and 

```
docker-compose run
```

3. If you want to access any of the running containers:

```
docker exec -it <container_name> /bin/bash
```

4. To enable the Jupyter notebooks in the container run:

```
jupyter notebook --ip 0.0.0.0 --port 8888 --no-browser --allow-root
```

### Update the default Docker DNS server
If you are using the WSL or a Linux distribution as your OS you need to configure the following in order for the private container network to resolve outside hostnames and interact correctly with the internet.

1. Install dnsmasq and resolvconf.
```
sudo apt update
sudo apt install dnsmasq resolvconf
```

2. Find your docker IP (in this case, 172.17.0.1):
```
root@host:~# ifconfig | grep -A2 docker0
docker0   Link encap:Ethernet  HWaddr 02:42:bb:b4:4a:50  
          inet addr:172.17.0.1  Bcast:0.0.0.0  Mask:255.255.0.0
```

3. Edit /etc/dnsmasq.conf and add these lines:
```
interface=docker0
bind-interfaces
listen-address=172.17.0.1
```
4. Create/edit /etc/resolvconf/resolv.conf.d/tail and add this line:
```
nameserver 172.17.0.1
```
5. Re-read the configuration files and regenerate /etc/resolv.conf.
```
sudo resolvconf -u
```
6. Restart your OS. If you are using WSL run the following in your windows terminal:
```
wsl.exe --shutdown
```