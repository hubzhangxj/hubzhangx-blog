
# PostGIS
[PostGIS官网](https://postgis.net)

## 环境配置
CentOS Linux release 7.5.1804
[PostgreSQL 9.6.1](https://wwww.postgresql.org)


## 安装部署

### 1.yum安装

### 1.源码包安装
[PostGIS源码包](https://download.osgeo.org/postgis/source)
```shell-session
wget https://download.osgeo.org/postgis/source/postgis-2.5.2.tar.gz 
```    
### 2.编译
[PostGIS编译](https://postgis.net/source)
```shell-session
tar xvzf postgis-2.5.2.tar.gz
cd postgis-2.5.2
./configure
make
make install
```












