# graduate_project
CAU personal graduate project

## 1. 部署服务器
- 选择Ubuntu系统(22.04.3 LTS) 

查看版本：`lsb_release -a`

- 安装docker 

`sudo apt-get update` 

`sudo apt-get install \ apt-transport-https \ ca-certificates \ curl \ gnupg \ lsb-release`

`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg`

`echo \ "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \ $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null`

`sudo apt-get update`

`sudo apt-get install docker-ce docker-ce-cli containerd.io`
- 测试docker是否安装

`docker -v`
- 安装nvidia-docker

- `distribution=$(. /etc/os-release;echo $ID$VERSION_ID)`

`curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -`

`curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list`

`curl -s -L https://nvidia.github.io/libnvidia-container/experimental/$distribution/libnvidia-container-experimental.list | sudo tee /etc/apt/sources.list.d/libnvidia-container-experimental.list`

(上面几行一起运行)

`sudo apt-get update`

`sudo apt-get install -y nvidia-docker2`
- 重启docker

`sudo service docker stop`

`sudo service docker start`
## 2. 