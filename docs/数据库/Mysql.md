

# Mysql

## Linux Centos 安装Mysql

### 下载

MySQL官网：[https://www.mysql.com/](https://www.mysql.com/)

![image.png](../images/1692156163642-b623db72-b7e5-40e6-83f1-2116eeb0faf0.png)

#### 点击download

![image.png](../images/1692156255007-23336516-f11a-4fa7-8e3a-6481867385a4.png)

点击 "[MySQL Community (GPL) Downloads »](https://dev.mysql.com/downloads/)"

#### 在 "MySQL Communiy Downloads"

![image.png](../images/1692156527188-ebedbde6-faaa-4994-a83c-a01a2c2b0f0b.png)
点击 " MySQL Community Server "

#### 进入"MySQL Community Server" 下载页面

![image.png](../images/1692159486627-156b1b7f-4236-4b3f-ac15-1f4ee2813248.png)
点击 “Archives”按钮

#### 进入“MySQL Product Archives”页面

![image.png](../images/1692159741978-655081ee-45c9-4c7a-bc5e-192459cdc4be.png)
选择版本号和操作系统
![image.png](../images/1692160101772-7a66edad-a6a5-4b87-9a0c-88d169490ece.png)
选择指定版本：mysql-<相应版本编号>.rpm-bundle.tar 下载

### 安装

#### 创建soft文件夹

命令：mkdir soft

![image.png](../images/1692162894070-31cd6062-6f37-4c95-bd9a-bf163c6840fe.png)

#### 将MySQL安装包上传到soft文件夹

可使用rz命令
也可使用xftp工具连接上传

![image.png](../images/1692163478239-d55d846a-3880-4455-a3c8-db94f1ed4900.png)

#### 查看mariadb安装包

命令：rpm -qa | grep mariadb

![image.png](../images/1692163599667-a4aae397-ef00-4f89-8ab7-e81ccf799a17.png)

#### 卸载mariadb

命令：rpm -e mariadb-libs-5.5.68-1.el7.x86_64 --nodeps

![image.png](../images/1692163748246-844176ea-6c08-4e02-9f5d-55d3f2878eb2.png)
再次查看mariadb安装包

#### 在/usr/local目录下创建mysql目录

命令：cd /usr/local
命令：mkdir mysql-8.0.32

![image.png](../images/1692163879218-9077c4b3-c49a-475a-aa41-0cf0c45c8f89.png)

#### 把mysql安装包移动到mysql目录下

在soft目录下
命令：mv mysql-8.0.32-1.el7.x86_64.rpm-bundle.tar /usr/local/mysql-8.0.32/

![image.png](../images/1692164103607-56c255d6-be3c-4654-b945-c11eac9d43a7.png)

#### 打开mysql目录

![image.png](../images/1692164230134-43d40d56-2e9d-4f6e-90bc-fd19245016b3.png)

#### 解压MySQL安装包到/usr/local/mysql-8.0.32目录下

命令：tar -xvf mysql-8.0.32-1.el7.x86_64.rpm-bundle.tar

![image.png](../images/1692164337128-ffb42bcf-2b0b-4eed-8981-8b4a9b1be744.png)

#### 安装MySQL其它安装包

命令：
rpm -ivh mysql-community-common-8.0.32-1.el7.x86_64.rpm --nodeps --force
rpm -ivh mysql-community-libs-8.0.32-1.el7.x86_64.rpm --nodeps --force
rpm -ivh mysql-community-client-8.0.32-1.el7.x86_64.rpm --nodeps --force
rpm -ivh mysql-community-server-8.0.32-1.el7.x86_64.rpm --nodeps --force

![image.png](../images/1692164578792-8f5eb144-a9ba-4268-b723-8c2c1e0117cf.png)

#### 查看MySQL其它安装包

命令：rpm -qa | grep mysql 

![image.png](../images/1692164672522-9a3ff82e-5588-4342-8d65-abb4fcae24dc.png)

#### 初始化数据库

在mysql-8.0.32目录下，命令：mysqld --initialize

![image.png](../images/1692164770537-8e7a6c67-b97e-43d7-8dcb-120ce9a4f948.png)

#### 修改mysql权限

在mysql-8.0.32目录下，命令：chown mysql:mysql /var/lib/mysql -R

![image.png](../images/1692164921535-9e1fa0ac-8fea-426a-b388-2a2a1022733f.png)

#### 启动MySQL数据库服务

在mysql-8.0.32目录下，命令：systemctl start mysqld.service

![image.png](../images/1692165339509-059fcd3d-95f5-4164-b109-7279d7c365c6.png)

#### 设置开机自启动MySQL服务

在mysql-8.0.32目录下，命令：systemctl enable mysqld

#### 查看安装MySQL初始化密码

命令：cat /var/log/mysqld.log | grep password

![image.png](../images/1692165519816-33187b62-8be0-45e9-8695-c4ff433db6b7.png)

#### 进入MySQL数据库登录页面

在mysql-8.0.32目录下，命令：mysql -uroot -p
**注意：MySQL登录密码是不显示的 **

![image.png](../images/1692165773746-e7e69acc-eef4-4ba0-96c3-aca09abf3707.png)

#### 执行SQL语句：show databases;

第一次使用将会提示修改mysql的root用户密码

![image.png](../images/1692165848756-039f194d-531f-4924-bd02-8297d46f2e2b.png)

#### 修改MySQL密码

命令：alter user 'root'@'localhost' identified by '123456';

![image.png](../images/1692165929779-5694ba4b-8831-449b-b14f-d383ffcdb3a5.png)

#### 授权远程访问MySQL服务

在 MySQL 8.0.32 版本中，如果您想要为 'root' 用户设置密码并使用 mysql_native_password 身份验证插件，可以使用以下语句：
create user 'root'@'%' identified with mysql_native_password by '**root**';
请注意，这里的密码是** 'root'**，您可以根据需要更改密码。

**注意：修改远程访问密码**
**alter user 'root'@'%' identified with mysql_native_password by '123456';**

---

使用以下语句授予给 'root' 用户在 MySQL 8.0.32 中的所有权限，并使用 WITH GRANT OPTION 选项允许用户对其他用户进行授权：
grant all privileges on *.* to 'root'@'%' with grant option;
这将授予 'root' 用户在所有数据库和所有表上的所有权限，并具有对其他用户进行授权的能力。

![image.png](../images/1692166320526-0ee72677-fb5c-4d7e-bb4e-e10ef9b7c4e4.png)

#### 修改加密方式

原因：MySQL8.0 版本 和 5.0 的加密规则不一样，而现在的可视化工具只支持旧的加密方式。
命令：ALTER USER 'root'@'localhost' IDENTIFIED BY '123456' PASSWORD EXPIRE NEVER; 
![image.png](../images/1692166693882-7ff06e8b-4052-4f4d-854c-be38fb8880df.png)

#### 刷新权限

命令：flush privileges;

![image.png](../images/1692166486336-b69fd9ac-7341-4402-af44-ea2034cbded3.png)

#### 退出MySQL

命令：exit;

![image.png](../images/1692166988103-705df8cd-d71d-4016-af8d-65cb2741a23e.png)

#### 关闭防火墙firewall

systemctl stop firewalld.service;
systemctl disable firewalld.service;
systemctl mask firewalld.service;

![image.png](../images/1692167034910-4ab228c4-f5db-4262-b8a2-896c0b5f934a.png)

## SQL相关语句注意事项

### 创建表

**column1**： 字段名

**datatype**： 字段类型

**constraint**： 约束

```mysql
CREATE TABLE table_name (
    column1 datatype [constraint], "备注"
    column2 datatype [constraint], "备注"
    ...
);
```

|   约束   |                           描述                           |   关键字    |
| :------: | :------------------------------------------------------: | :---------: |
| 主键约束 |         主键是一行数据的唯一标识，要求非空且唯一         | PRIMARY KEY |
| 非空约束 |                限制该字段的数据不能为null                |  NOT NULL   |
| 唯一约束 |     保证该字段的所有数据都是唯一、不重复的、可以为空     |   UNIQUE    |
| 默认约束 |      保存数据时，如果未指定该字段的值，则采用默认值      |   DEFAULT   |
| 检查约束 |   插入数值时根据指定条件校验合法性(8.0.16版本之后开始)   |    CHECK    |
| 外键约束 | 用来让两张表的数据之间建立连接，保证数据的一致性和完整性 | FOREIGN KEY |

### 多表查询

**例：**

```mysql
select k.id ak_id,
               k.name attr_key_name,
               v.id,
               v.name,
               v.attr_key_id
        from attr_key k
                 left join attr_value v
                           on k.id = v.attr_key_id
                               and v.is_deleted = 0
        where k.is_deleted = 0
```

注：左连接需带上右表的 is_deleted字段 确保返回左表所有字段



### 事务

```mysql
SET @@autocommit = 0; # 设置为手动提交事务
SET @@autocommit = 1; # 设置为自动提交事务
```



```mysql
#开启事务
START TRANSACTION; 或 BEGIN;
#sql语句
#提交事务
commit;
```

