1. mysql 登录

> mysql 登录密码特殊字符前面加\转义

2. 导出数据库

```mysql
mysqldump -h127.0.0.1 -ukeji -pkeji ems > /opt/ems/ems.sql
```

3. 导入数据库

```bash
mysql -u keji -P 23306 -p -h 192.168.1.1
mysql > use ems;
mysql > source /opt/ems/ems.sql
```

