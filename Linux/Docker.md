# Docker
1.  必须内核3. 10以上   uname -r 查看
2.  yum install docker
3.  启动  systemctl start docker     停止 systemctl stop docker
4. 设置开机启动 systemctl enable docker
5. 搜索镜像  docker search 关键字
6. 下载镜像  docker pull 下载名字
7. 查看所有镜像 docker images
8. 删除镜像 docker rmi 镜像ID
9. 运行镜像 docker run (可选: --name 自定义名字) -d(后台) -p(地址映射) 8080(外部访问端口):8080(容器中的端口) 启动的镜像  
    * 可以一个镜像启动多个容器
    * 每个请查看实例,有的需要设置其他必选项
10. 查看运行的镜像 docker ps
11. 停止 docker stop 自定义名字|容器ID
12. 查看所有容器 docker ps -a
13. 启动容器 docker start 容器ID
14. 删除容器 docker rm 容器ID
15. 查看日志 docker logs 容器ID|自定义名字
16. 进入容器 docker exec -it containerID /bin/bash
17. 本地文件传入容器 docker cp 本地文件路径 ID全称:容器路径
18. 容器传入本地 docker cp ID全称:容器文件路径 本地路径
19. 重启容器 docker restart 容器ID|自定义名字

#fastdfs
docker pull mypjb/fastdfs
mkdir /home/fastdfs
docker run --add-host fastdfs.net:192.168.1.40 --name fastdfs --net=host -e TRACKER_ENABLE=1 -e NGINX_PORT=81 -v /home/fastdfs:/storage/fastdfs -it mypjb/fastdfs
docker restart fastdfs
firewall-cmd --zone=public --add-port=81/tcp --permanent;firewall-cmd --reload;
