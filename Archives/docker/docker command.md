```shell
docker image ls
  -a, --all             Show all images (default hides intermediate images)
      --digests         Show digests
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print images using a Go template
      --no-trunc        Don't truncate output
  -q, --quiet           Only show numeric IDs
```

* 常用命令

```shell
# 进入容器
docker exec -it servicename bash
# 查看容器修改的部分
docker diff servicename
# docker commit
 docker commit \
 --author "dhxz" \
 --message "modified index page" \
 webserver \
 nginx:v2
# docker 启动命令
docker run --name web -d -p 80:80 nginx:v2
```

* DockerFile

```shell
FROM nignx
RUN echo '<h2>HELLO,DOCKER!</h2>' > /usr/share/nginx/html/index.html
```

