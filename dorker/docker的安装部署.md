# docker引擎
docker 引擎是C\\S架构的

![[Pasted image 20241011192941.png]]

操作docker有两种方式，一种是调用API接口来获取docker的数据，一种是用linux的命令行来
![[Pasted image 20241011194731.png]] 客户端步骤：
- 基于dockerfile构建镜像
- 下载别人的镜像 docker pull
- 启动docker容器


# docker最核心的组件
- image镜像，构建容器（应用程序运行所需的环境，打包为镜像文件）
- container容器
- 镜像仓库（dockerhub）（保存镜像文件，提供上传，下载镜像）作用好比github
- dockerfile，将你部署项目的操作，写成一个部署脚本且该脚本还能构建出镜像文件