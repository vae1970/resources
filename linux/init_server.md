1. mysql 

   修改mysql可以远程登录

   sudo vim /etc/mysql/mariadb.conf.d/50-server.cnf

   sudo service mysql restart

   添加账户并且授权

   ```
   use mysql;
   CREATE USER 'keji'@'%' IDENTIFIED BY 'keji2!@#$%';
   GRANT all privileges ON ems.* TO 'keji'@'%' identified by 'keji2!@#$%';
   ```


2. rabbmit mq
   1. sudo rabbitmqctl add_user keji 'keji2!@#$%'
   2. sudo rabbitmqctl set_user_tags keji administrator
   3. sudo rabbitmqctl  set_permissions  -p  '/'  keji '.' '.' '.'
   4. sudo rabbitmq-plugins enable rabbitmq_management
   5. sudo service rabbitmq-server restart