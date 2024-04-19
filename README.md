# graduate_project
CAU personal graduate project

## 准备工作
### 1. 部署服务器
- **选择Ubuntu系统(22.04.3 LTS)**   
查看版本：`lsb_release -a`  
- **安装docker**   
`sudo apt-get update`  
`sudo apt-get install \ apt-transport-https \ ca-certificates \ curl \ gnupg \ lsb-release`  
`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg`  
`echo \ "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \ $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null`  
`sudo apt-get update`  
`sudo apt-get install docker-ce docker-ce-cli containerd.io`  
- **测试docker是否安装**  
`docker -v`  
- **安装nvidia-docker**  
`distribution=$(. /etc/os-release;echo $ID$VERSION_ID)`  
`curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -`  
`curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list`  
`curl -s -L https://nvidia.github.io/libnvidia-container/experimental/$distribution/libnvidia-container-experimental.list | sudo tee /etc/apt/sources.list.d/libnvidia-container-experimental.list`  
(上面四条一起运行)  
`sudo apt-get update`  
`sudo apt-get install -y nvidia-docker2`  
- **重启docker**  
`sudo service docker stop`  
`sudo service docker start`  
### 2. 构建docker映像
#### **调试主机**  
 - **安装docker desktop**  
 [docker desktop官网](https://www.docker.com/products/docker-desktop/)  
 - **
#### **服务器**  
 - **构建docker映像**  
 `cd /your_path_to/DeepVisionVRServer/Docker/`  
 `bash build.sh`  
 (可能会遇到的问题：)    
 **`ERROR: permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Head "http://%2Fvar%2Frun%2Fdocker.sock/_ping": dial unix /var/run/docker.sock: connect: permission denied`**  
 表明当前用户权限不够，两种解决办法：  
 **切换为root用户**  
 `su root`  
 **给当前用户权限**  
 `sudo groupadd docker`  
 `sudo gpasswd -a your_username docker`  
 `newgrp docker`  
 **`ERROR: failed to solve: process "/bin/sh -c apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y     python3.8     python3-pip     python3-tk     xvfb" did not complete successfully: exit code: 100`**  
 表明网络问题下载不了，尝试换源或者换个vpn  
 - **
### 3. 启动服务端
- **启动服务端**  
`cd /your_path_to/DeepVisionVRServer/DeepLearningServer/Projects/Demo/`  
**修改`/DeepVisionVRServer/DeepLearningServer/Projects/Demo/start_falcon_server.sh`中的路径，更改为 DeepLearningServer目录的相对路径即可，即-v /your_path_to/DeepLearningServer:/DeepLearningServer,url中的ip地址更改为当前ip地址,端口号可选择更改**
`bash start_falcon_server.sh`  
无报错即开启成功，可以通过**Postman**测试：    
GET http://your_ip:your_port  
**正常状态下会看到状态字为200且"message": "Server is running"**  
### 4. 启动客户端
- **安装unity hub**  
[unity hub](https://unity.com/unity-hub)  
- **安装unity 2021.1.14f1**  
[unity 2021.1.14f1](https://unity.com/releases/editor/whats-new/2021.1.14)  
- **使用unity hub打开客户端文件**  
- **场景都集成在SubSceneNonVR上，运行即可**
## 编辑开发
### 1. 