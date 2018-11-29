## LOAD DATA INFILE

导出表数据

```sql
select * from demo into outfile '/var/lib/mysql-files/demo.txt'
```

先手工创建用于导入数据的表

```sql
create table demo (id int, name varchar(50))
```

将数据导入该表

```sql
load data infile '/var/lib/mysql-files/demo.txt' into table demo
```

### 其他

自定义导出表数据的分隔符。

```sql
select * from demo into outfile '/var/lib/mysql-files/demo.txt' 
fields terminated by ',' enclosed by '"';
```

导入时也需要指定一样的分隔符。

```sql
load data infile '/var/lib/mysql-files/demo.txt' into table demo 
fields terminated by ',' enclosed by '"';
```

## mysqlimport

导出表数据

```sql
mysqldump tutorial -T /var/lib/mysql-files
```

导出后的文件

```txt
shell> ls /var/lib/mysql-files
demo.sql  demo.txt  test.sql  test.txt
```

先手工创建用于导入数据的表

```sql
create table demo (id int, name varchar(50))
```

导入数据到该表，demo.txt 的文件名 `demo` 即默认为表名。

```sh
 mysqlimport -h localhost -u root -p tutorial /var/lib/mysql-files/demo.txt 
```