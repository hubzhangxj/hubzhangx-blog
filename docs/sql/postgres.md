
# ARM服务器PostgreSQL安装部署

# ARM服务器Postgis安装部署



## 环境配置
CentOS Linux release 7.5.1804  
[PostgreSQL 9.6.1](https://wwww.postgresql.org)  


## 安装部署
### 1.获取源码包
     
### 2.编译并安装PostgreSQL
在/home目录创建安装目录pgsql：
```shell-session
>mkdir pgsql  
```
配置yum源,并安装PostgreSQL依赖包  
```shell-session
yum -y install gcc gcc-c++ automake zlib zlib-devel bzip2 bzip2-devel bzip2-libs readline-devel bison  
```
解压PostgreSQL源代码包,进入解压目录,执行编译命令  
```shell-session
tar -xzvf postgresql-9.6.1.tar.gz  
cd postgresql-9.6.1  
./configure --prefix=/home/pgsql  
make -j 64  
make install  
```
### 3.配置PostgreSQL执行环境
在/home/pgsql目录安装的PostgreSQL主要用于安装文件备份以及后续sysbench oltp benchmark动态库引用.  
需要在tmpfs文件系统下创建PostgreDQL测试实例,避免磁盘IO对性能测试结果的影响.  
此步骤操作包括: 将安装文件包拷贝至/run目录,创建postgres用户,创建数据目录，目录权限修改(安全原因).  
如需在磁盘完整安装配置PostgreSQL,也可参考后续章节.
```shell-session
cd /home
cd -Rf pgsql/run/pgsql
adduser postgres
cd /run/pgsql
mkdir data
chown -R postgres ./
```
在/etc/profile中配置OS环境变量,利于后续数据库性能测试相关操作.  
export LD_LIBRARY_PATH=/home/pgsql/lib:$LD_LIBRARY_PATH
export PATH=/run/pgsql/bin:$PATH
source /etc/profile
```
### 4.初始化数据库
执行如下命令,启动PostgreSQL数据库.  
```shell-session
su - progres -c "/run/pgsql/bin/pg_ctl" -D /run/pgsql/data start
```
执行如下命令,停止PostgreSQL数据库.  
```shell-session
su - progres -c "/run/pgsql/bin/pg_ctl" -D /run/pgsql/data stop
```
执行如下命令,确认postgres数据库是否正常启动:  
```shell-session
ps -ef | grep postgres
```
查看postgresql版:  
```shell-session
pgsql --version

show server_version

SHOW server_version_num
或
SELECT current_setting('server_version_num')
```
**注意事项**  
SELECT current_setting('server_version_num');返回类型为text,如果需要可以转换为interger
```shell-session
SELECT current_setting('server_version_num')::integer;
```

如上图所示,PostgreSQL相关进程已经正常启动了.
配置PostgreSQL为系统服务:将PostgreSQL安装目录内,contrib/start-scripts/linux文件拷贝至/etc/init.d目录,并修改名称为postgresql,  
权限为chmode +x postgresql. 修改postgresql文件内容中,prefix变量值为/run/pgsql, PGDATA变量的值为/run/pgssql/data.
执行以下命令配置postgresql服务.
```shell-session
chkconfig --add postgresql
chkconfig postgresql off #如不需开机自启动,执行此命令
service postgresql start|stop|restart|status #按需执行
```
### 5.登录数据库
执行如下命令登录数据库.初次登录数据库,无需密码.  
```shell-session
psql -U postgres
```
登录后,执行以下命令设置postgres密码  
```shell-session
alter user postgres with password '123456';
```
### 6.配置数据库
编辑/run/pgsql/data/pg_hba.conf,增加以下内容,用以允许非本地访问,IP地址按需配置  
```shell-session
host all  all 192.168.11.0/22  md5
```
编辑/run/pgsql/data/pg_hba.conf,如以下文件.其中重要参数请参考如下表说明.  

|  参数                  |  说明                                                 |
| :------                | :------                                               |
| max_connections        | 2050. 最大连接数,sysbench oltp测试中最大连接数为1024. |
| shared_buffers         | 4GB. 共享缓存大小,sysbench oltp测试中,数据库大小为3G. |
| maintenance_work_mem   | 2GB. 维护操作使用内存.越大越快,过大无用.              |
| checkpoint_timeout     | 1d. 检查点周期,设置为1天,避免对性能测试的影响.        |
| max_wal_size           | 4GB. 最大WAL x_log文件大小,设置稍大以免频繁checkpoint |
| effective_cache_size   | 12GB. 可作为系统cache的系统内存度量.                  |




































## 参考链接














