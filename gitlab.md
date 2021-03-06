## 安装

使用 docker 镜像进行安装

```sh
shell> sudo docker run --detach \
    --hostname gitlab.example.com \
    --publish 443:443 --publish 80:80 --publish 22:22 \
    --name gitlab \
    --restart always \
    --volume /srv/gitlab/config:/etc/gitlab \
    --volume /srv/gitlab/logs:/var/log/gitlab \
    --volume /srv/gitlab/data:/var/opt/gitlab \
    gitlab/gitlab-ce:8.17.8-ce.0
```

为了避免冲突，物理机器 ssh 端口修改为 25680

```sh
shell> vim /etc/ssh/sshd_config
# What ports, IPs and protocols we listen for
Port 25680
```

重启 ssh 服务

```sh
shell> service ssh restart
```


## 全量备份

```sh
shell> docker stop gitlab
shell> docker commit gitlab local/gitlab-container-20181010
shell> docker save local/gitlab-container-20181020 > /root/gitlab-container-20181010.tar
shell> docker run --rm --volumes-from gitlab -v $(pwd):/backup ubuntu tar cvf /backup/gitlab-volume-etc-20181010.tar /etc/gitlab
shell> docker run --rm --volumes-from gitlab -v $(pwd):/backup ubuntu tar cvf /backup/gitlab-volume-log-20181010.tar /var/log/gitlab
shell> docker run --rm --volumes-from gitlab -v $(pwd):/backup ubuntu tar cvf /backup/gitlab-volume-opt-20181010.tar /var/opt/gitlab
```

## 全量恢复

用于恢复的文件列表

```sh
shelll> tree
.
├── gitlab-container-20181010.tar
├── gitlab-volume-etc-20181010.tar
├── gitlab-volume-log-20181010.tar
└── gitlab-volume-opt-20181010.tar
```

加载镜像

```sh
shell> docker load -i gitlab-container-20181010.tar 
```

创建容器

```sh
shell> sudo docker create \
    --hostname gitlab.example.com \
    --publish 443:443 --publish 80:80 --publish 22:22 \
    --name gitlab \
    --restart always \
    --volume /srv/gitlab/config:/etc/gitlab \
    --volume /srv/gitlab/logs:/var/log/gitlab \
    --volume /srv/gitlab/data:/var/opt/gitlab \
    local/gitlab-container-20181010
```

恢复数据

```sh
shell> mkdir -p /srv/gitlab/config
shell> mkdir -p /srv/gitlab/logs
shell> mkdir -p /srv/gitlab/data
shell> docker run --rm --volumes-from gitlab -v $(pwd):/backup ubuntu bash -c "cd /etc && tar xvf /backup/gitlab-volume-etc-20181010.tar --strip 1"
shell> docker run --rm --volumes-from gitlab -v $(pwd):/backup ubuntu bash -c "cd /var/log && tar xvf /backup/gitlab-volume-log-20181010.tar --strip 2"
shell> docker run --rm --volumes-from gitlab -v $(pwd):/backup ubuntu bash -c "cd /var/opt && tar xvf /backup/gitlab-volume-opt-20181010.tar --strip 2"
```

启动容器

```sh
shell> docker start gitlab
```

## 参考文献

- [Gitlab docker container – backup and restore](http://khmel.org/?p=1213)
- [GitLab Docker images](https://docs.gitlab.com/omnibus/docker/)
