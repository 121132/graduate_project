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
### 服务端拆解
#### 网络模型部分
- **某网络模型.py&某网络模型.pt**  
 - **模型.py**  
 参考CovidResNet.py文件的格式构建网络模型  
 **重点**注意如何**封装的函数**以及模型函数的**参数**，一定要保证**一致性**否则后面会出各种各样的问题
 **次重点**在想要在unity中展示的网络层，考虑**tracker层**的编写，建议写完后提前测试pos和per数组是否正确，可以使用**postman**方便的测试而不必重复启动客户端。
 GET http://your_ip:your_port/network/your_network_id  
 - **模型.pt**  
 .pt文件是pytorch网络训练后保存的权重文件，通过提前训练获得，加载入模型才会有效果。可以通过[**kaggle**](https://www.kaggle.com/)网站云端训练。  
训练模型：  
`import torch`  
`import torch.nn as nn`  
`import torch.optim as optim`  
`from torchvision import datasets, transforms`  
`from torch.utils.data import DataLoader`  

`# 定义数据转换`  
`data_transforms = transforms.Compose([`  
`    transforms.Resize(256),`  
`        transforms.RandomHorizontalFlip(),`  
`        transforms.ColorJitter(brightness=0.2, contrast=0.2, saturation=0.2),`  
`        transforms.CenterCrop(224),`  
`        transforms.ToTensor(),`  
`        transforms.Normalize(mean=[0.485, 0.456, 0.406], std=[0.229, 0.224, 0.225])`   
`])`  

`# 加载数据集`  
`train_dataset = datasets.ImageFolder('/kaggle/input/corn-orginal/corn_orginal',   transform=data_transforms)`  

`# 定义数据加载器`  
`train_loader = DataLoader(train_dataset, batch_size=32, shuffle=True)`  


`# 定义模型`  
`model = CovidGoogleNet(`  
`    num_classes=4,`  
`    pretrained=False,`  
`)`  

`# 定义损失函数和优化器`  
`criterion = nn.CrossEntropyLoss()`  
`optimizer = optim.SGD(model.parameters(), lr=0.001, momentum=0.9)`  

`# 训练模型`  
`num_epochs = 50  # 定义训练周期数`  
`for epoch in range(num_epochs):`  
`    model.train()  # 设置模型为训练模式`  
`    running_loss = 0.0`  
`    for inputs, labels in train_loader:`    
`        optimizer.zero_grad()`  
`        outputs = model(inputs)`  
`        loss = criterion(outputs, labels)`  
`        loss.backward()`  
`        optimizer.step()`  
`        running_loss += loss.item()`  
`    print(f"Epoch {epoch+1}/{num_epochs}, Loss: {running_loss/len(train_loader)}")`  
保存模型：`torch.save(model.state_dict(), '/kaggle/working/inception-new.pt')`  


#### 通信部分
#### 其他
### 客户端拆解