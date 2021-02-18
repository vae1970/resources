docker 导出镜像

```bash
sudo docker save -o /dev/shm/mysql.tar mysql/mysql-server:5.7

```

docker 导入镜像

```bash
sudo docker load -i /opt/ems/images/mysql.tar
```



```bash
sudo docker run -p 23306:3306 --mount type=bind,src=/opt/ems/docker/etc/mysql/my.cnf,dst=/etc/my.cnf --mount type=bind,src=/opt/ems/docker/data/mysql,dst=/var/lib/mysql -d --name mysql -e MYSQL_ROOT_PASSWORD='keji2!@#$%' mysql/mysql-server:5.7 
```

docker批量删除

```bash
docker rmi $(docker images | awk '/^<none>/ { print $3 }')
```

docker自启动服务

```
    docker container update --restart=always mysql
```

