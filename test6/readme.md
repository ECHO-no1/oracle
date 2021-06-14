## 一、引言

从资源回收产业链改造的角度去说，互联网取缔了“回收站点”，将零散的回收从业者个人统一起来，使旧物直接从用户家中到达回收基地（大型回收分拣处理企业），缩短了产业链流程以节约成本，包括单人、单点覆盖面的时间成本，运输成本、层层转手产生的二次成本。如果从规模上来说，在完全规模化以后，还能实现城市回收基地的取代，直接与再生产企业建立业务往来，这中间成本几乎为零。另一方面，如果旧物回收工具能够成功进入家庭或社区，成为一道入口，在这个方向上将更加具有想象力。

## 二、概念模型设计（E-R图）

![](img\E-R.png)

## 三、数据库表

3.1订单表（orders）：

![](img\orders.png)

3.2普通用户表(user)：

![](img\user.png)

3.3回收员表(collector)：

![](img\collector.png)

3.4转账信息表(pay)：

![](img\pay.png)

3.5管理员admin表:

![](img\admin.png)

3.6废物rubbish表:

![](img\rubbish.png)

## 四、创建表空间

#### space_echo_no1

```sql
Create Tablespace space_echo_no1
 datafile
 '/home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_echo_no1_1.dbf'
  SIZE 100M AUTOEXTEND ON NEXT 256M MAXSIZE UNLIMITED,
 '/home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_echo_no1_2.dbf'
  SIZE 100M AUTOEXTEND ON NEXT 256M MAXSIZE UNLIMITED
 EXTENT MANAGEMENT LOCAL SEGMENT SPACE MANAGEMENT AUTO;
```

#### space_echo_no2

```
Create Tablespace space_echo_no2
 datafile
 '/home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_echo_no2_1.dbf'
  SIZE 100M AUTOEXTEND ON NEXT 256M MAXSIZE UNLIMITED,
 '/home/oracle/app/oracle/oradata/orcl/pdborcl/pdbtest_echo_no2_2.dbf'
  SIZE 100M AUTOEXTEND ON NEXT 256M MAXSIZE UNLIMITED
 EXTENT MANAGEMENT LOCAL SEGMENT SPACE MANAGEMENT AUTO; 
```

## 五、 创建角色及用户

用户默认使用表空间 space_echo_no1

#### 创建第一个角色和用户

```
创建角色 ql1 将 connect,resource,create view 授权给 ql1
创建用户 ql1_1
分配 60M 空间给 ql1_1 并将角色 ql1 授权给用户 ql1_1

CREATE ROLE ql1;
 GRANT connect,resource,CREATE VIEW TO ql1;
 CREATE USER ql1_1 IDENTIFIED BY 123 DEFAULT TABLESPACE space_echo_no1 TEMPORARY TABLESPACE temp;
 ALTER USER ql1_1 QUOTA 60M ON space_echo_no1;
 GRANT ql1 TO ql1_1;
```

#### 创建第二个角色和用户

```
创建角色 ql2，将 connect,resource 权限给 ql2
创建用户 ql1_2
分配 60M 空间给 ql1_2 并将角色 qhl2 授权给用户 ql1_2
CREATE ROLE qhl2;
GRANT connect,resource TO qhl2;
CREATE USER ql1_2 IDENTIFIED BY 123 DEFAULT TABLESPACE space_echo_no1 TEMPORARY TABLESPACE temp;
ALTER USER ql1_2 QUOTA 60M ON space_echo_no1;
GRANT qhl2 TO ql1_2;

```

## 六、表和数据添加

如下是本次数据库的建表sql，使用的是pl/sql语言写的一个文件。

在建表之前应该判断数据库中是否有该表的存在，如果有删除，如果没有，则执行建表语句。

这里使用的是查找该表中的数据条数，来判断是是否有表，然后执行drop table 来删除表。Declare表示申明，begin表示执行开始，需要在结尾加上end；/ 表示执行以上所有代码。

```
declare

   num  number;

begin
   select count(1) into num from user_tables where TABLE_NAME = 'RUBBISH';
   if  num=1  then
      execute immediate 'drop table RUBBISH cascade constraints PURGE';
   end  if;   
   select count(1) into num from user_tables where TABLE_NAME = 'RUBBISH';
   if  num=1  then
      execute immediate 'drop table RUBBISH cascade constraints PURGE';
   end  if;

  
   select count(1) into num from user_tables where TABLE_NAME = 'MESSAGE';
   if  num=1  then
      execute immediate 'drop table MESSAGE cascade constraints PURGE';
   end  if;
  
   select count(1) into num from user_tables where TABLE_NAME = 'MESSAGE_USER';
   if  num=1  then
      execute immediate 'drop table MESSAGE_USER cascade constraints PURGE';
   end  if;

   select count(1) into num from user_tables where TABLE_NAME = 'ORDER';
   if  num=1  then
      execute immediate 'drop table ORDER cascade constraints PURGE';
   end  if;

   select count(1) into num from user_tables where TABLE_NAME = 'USER';
   if  num=1  then
     execute immediate 'drop table USER cascade constraints PURGE';
   end  if;  
     select count(1) into num from user_tables where TABLE_NAME = 'ORDER_INFOR';
   if  num=1  then
      execute immediate 'drop table ORDER_INFOR cascade constraints PURGE';
   end  if;  
   select count(1) into num from user_tables where TABLE_NAME = 'USERS';
   if  num=1  then
      execute immediate 'drop table USERS cascade constraints PURGE';
   end  if;
end;
/
```

 

以下是建表语句，根据数据字段的设计，在数据库中设计数据库表，这里不赘述。

--创建RUBBISH 表

```
create table rubbish 

(

  RUBBISHID      INTEGER       not null,

  RUBBISHCLASS1    VARCHAR2(50)     not null,

  RUBBISHCLASS2    VARCHAR2(50)     not null,

  RUBBISHPHOTOURL   VARCHAR2(255)    not null,

  RUBBISHPRICE     INTEGER        not null,

  RUBBISHINFO     VARCHAR2(255)    not null

);

alter table rubbish

  add constraint PK_RUBBISH primary key (RUBBISHID);
```

-- 创建user表

```
create table "user" 

(

  CUSTOMERID      INTEGER       not null,

  CUSTOMERNAME     VARCHAR2(20)     not null,

  ADDRESS       VARCHAR2(50)     not null,

  EMAIL        VARCHAR2(20)     not null,

  PHONENUMBER     VARCHAR2(20)     not null,

  USERNAME       VARCHAR2(20)     not null,

  PASSWORD       VARCHAR2(20)     not null

);

 

alter table "user"

  add constraint PK_USER primary key (CUSTOMERID);

 

alter table orders

  add constraint FK_ORDERS_REFERENCE_USER foreign key (CUSTOMERID)

   references "user" (CUSTOMERID);

 

alter table orders

  add constraint FK_ORDERS_REFERENCE_COLLECTO foreign key (COLLECTORID)

   references collector (COLLECTORID);

 

alter table orders

  add constraint FK_ORDERS_REFERENCE_RUBBISH foreign key (RUBBISHID)

   references rubbish (RUBBISHID);

 

alter table pay

  add constraint FK_PAY_REFERENCE_COLLECTO foreign key (COLLECTORID)

   references collector (COLLECTORID);
```

--创建collector表

```
create table collector 

(

  COLLECTORID     INTEGER       not null,

  COLLECTORNAME    VARCHAR2(20)     not null,

  PASSWORD       VARCHAR2(20)     not null,

  REALNAME       VARCHAR2(20)     not null,

  COMMISSION      INTEGER       not null,

  ACCOUNT       VARCHAR2(20)     not null,

  PHONENUMBER     VARCHAR2(20)     not null

);

 

alter table collector

  add constraint PK_COLLECTOR primary key (COLLECTORID);
```

--创建orders表

```
create table orders 

(

  ORDERID       INTEGER       not null,

  CUSTOMERID      INTEGER,

  COLLECTORID     INTEGER,

  RUBBISHID      INTEGER,

  ORDERDATE      DATE         not null,

  CLASS        VARCHAR2(10)     not null,

  WEIGHT        INTEGER       not null,

  PRICE        INTEGER       not null,

  ADDRESS       VARCHAR2(50)     not null,

  RECOVERTIME     DATE         not null,

  STATE        VARCHAR2(1)     not null

);

 

alter table orders

  add constraint PK_ORDERS primary key (ORDERID);--创建表order表

CREATE TABLE ORDER(

  ID NUMBER(20) NOT NULL ,

  USER_ID NUMBER(20) DEFAULT NULL ,

  ORDER_ID NUMBER(20) DEFAULT NULL ,

  RUBBISH_ID NUMBER(20) DEFAULT NULL 

);
```

 

--创建表user

```
CREATE TABLE USER(

  ID NUMBER(20) NOT NULL,

  USER_ID NUMBER(20) DEFAULT NULL,

  NICKNAME VARCHAR2(255) NOT NULL,

  PASSWORD VARCHAR2(32) DEFAULT NULL ,

  SALT VARCHAR2(10) DEFAULT NULL,

  HEAD VARCHAR2(128) DEFAULT NULL ,

  REGISTER_DATE DATE DEFAULT NULL ,

  LAST_LOGIN_DATE DATE DEFAULT NULL ,

  LOGIN_COUNT NUMBER(11) DEFAULT '0'

);
```

-- 创建表pay

```
create table pay 

(

  PAYID        INTEGER       not null,

  COLLECTORID     INTEGER,

  AMOUNT        INTEGER       not null,

  STATUS        VARCHAR2(1)     not null

);

 

alter table pay

  add constraint PK_PAY primary key (PAYID);
```

-- 创建admin表

```
create table admin 

(

  ADMINID       INTEGER       not null,

  ADMINNAME      VARCHAR2(20)     not null,

  PASSWORD       VARCHAR2(20)     not null

);

 

alter table admin

  add constraint PK_ADMIN primary key (ADMINID);
```

## 七、数据库表导入相应数据

使用pl/sql语句来添加数据。

#### 1、向RUBBISH表中添加数据

这里定义了6个数组，数据库表中的每个字段随机从每个数组中选取数据，构成一个记录，插入到数据库中相应表中，数据条数为10000条。

```
set SERVEROUTPUT ON;

create or replace function RANDOM

  return number 

  is 

​    a number ; 

  begin

​    select round(dbms_random.value(1,5)) rnum

​    into a 

​    from dual;

​    return a ;

  end;

  /

 

DECLARE

  type RUBBISH_name is varray(5) of varchar2(20);

  type RUBBISH_details is varray(5) of VARCHAR2(100);

  type RUBBISH_img is varray(5) of VARCHAR2(20);

  type RUBBISH_info is varray(5) of VARCHAR2(50);

  type RUBBISH_price is varray(5) of VARCHAR2(20);

  type RUBBISH_have is varray(5) of VARCHAR2(20);

 

  indexRandom NUMBER;

  RUBBISH_name_list RUBBISH_name:=RUBBISH_name('金属','塑料',铝制品','瓶子','纸板'); 

  RUBBISH_details_list RUBBISH_details:=RUBBISH_details('4kg 银色 纸板','4kg 银色 纸板','4kg 银色 金属','4kg 银色 瓶子','4kg 银色 金属'); 

  RUBBISH_img_list RUBBISH_img:=RUBBISH_img('/img/iphonex.png','/img/meta10.png','/img/iphone8.png','/img/mi6.png','/img/mi9.png'); 

  RUBBISH_info_list RUBBISH_info:=RUBBISH_info('金属','4kg','月光银','玫瑰金','); 

  RUBBISH_price_list RUBBISH_price:=RUBBISH_price('8765.00','3212.00','5589.00','3212.00','7212.00'); 

  RUBBISH_have_list RUBBISH_have:=RUBBISH_have('8765','-1','558','3212','7212'); 

 BEGIN

  dbms_output.put_line(indexRandom);

  DBMS_OUTPUT.PUT_LINE(RUBBISH_name_list(5));

  for i in 1..10000

  loop

​    indexRandom:=RANDOM();

​    INSERT INTO RUBBISH VALUES (i, RUBBISH_name_list(indexRandom), RUBBISH_details_list(indexRandom), RUBBISH_img_list(indexRandom), RUBBISH_info_list(indexRandom), RUBBISH_price_list(indexRandom), RUBBISH_have_list(indexRandom));

  end loop;

 

 END;

 /
```

 

#### 2、向RUBBISH中添加数据

使用for loop语句构造2到2000 的数据，然后根据i值的不同，其后的数据相同，数据条数为2000条，插入到数据库中。

```
declare

  result number;

begin

  for i in 2..2000

  loop

​    result:=i mod 3;

​    if result =0 then

​      INSERT INTO RUBBISH VALUES (i, i, '0.01', '9', ('04-12月-17'), ('04-12月-17'));

​      DBMS_OUTPUT.PUT_LINE(i);

​    elsif result = 1 then

​      INSERT INTO RUBBISH VALUES (i, i, '0.25', '12', ('08-11月-18'), ('08-11月-18'));

​      DBMS_OUTPUT.PUT_LINE(i);

​    else

​      INSERT INTO RUBBISH VALUES (i, i, '0.05', '8', ('12-11月-17'), ('12-11月-19'));

​      DBMS_OUTPUT.PUT_LINE(i);

​    end if;

​    exit when i=2000;

  end loop;

end;

/
```

 

#### 3、向orders中添加数据

如上面的导入数据方式，使用i值的不同，构造不同记录。

```
declare

begin

  for i in 2..5000

  loop

​    INSERT INTO MESSAGE VALUES (i,'533324506110885888', '尊敬的用户你好，你已经成功注册！', null, '0', null, null, '0', null, null);

  end loop;

end;

/

 

-- user

declare

  id number;

  user_id number;

  nickname number;

begin

  id:=18912341247;

  nickname:=18612766444;

  user_id:=1;

  loop

​    id:=id+1;

​    user_id:=user_id+1;

​    nickname:=nickname+1;

​    INSERT INTO USER VALUES (id, user_id, nickname, 'b7797cce01b4b131b433b6acf4add449', '1a2b3c4d', null, '11-1月-19', null, '0');

​    exit when id=18912344246;

  end loop;

end;

/
```



#### 4、向orders中添加数据

导入orders使用的是循环loop….end，创建变量order_id,user_id，RUBBISH_id，并赋以初值，然后在执行完一条insert之后，是这些变量的值进行相应的改变，达到数据导入的目的。

```
DECLARE

--  INSERT INTO `order_info` VALUES ('1564', '18912341234', '3', null, 'iphone8', '1', '0.01', '1', '0', '2017-12-16 16:35:20', null);

  ORDER_ID NUMBER;

  USER_ID NUMBER;

  RUBBISH_ID NUMBER;

  ID NUMBER:=1;

BEGIN

--20000

  ORDER_ID:=1564;

  USER_ID:=1;

  RUBBISH_ID:=1;

  LOOP

​    INSERT INTO ORDER_INFOR VALUES(ID, ORDER_ID, USER_ID, RUBBISH_ID,null, 'iphone8', '1', '0.01', '1', '0', '16-12月-17', null);

​    ORDER_ID:=ORDER_ID+1;

​    USER_ID:=USER_ID+1;

​    RUBBISH_ID:=RUBBISH_ID+1;

​    EXIT ???

  END LOOP;

END;

/
```



## 八、ORACLE中相关配置

首先就是新建pdb 的操作，oracle没有办法对cdb进行操作，只能操作pdb，所以在oracle中的开始，我就需要新建一个pdb数据库，以上的相关操作，都是建立在这次之后的操作，这里新建一个salespdb的pdb数据库。

大致解释以下语句的含义：

Create pluggable database 就是新建一个pdb的语句，其中salespdb是数据库的名称，然后就是用户名和密码，使用的tablespace的大小，默认的存储文件地址。

```
CREATE PLUGGABLE DATABASE salespdb ADMIN USER sale5deng IDENTIFIED BY sale5deng STORAGE (MAXSIZE 2G) DEFAULT TABLESPACE sales DATAFILE '/database/oracle/oracle/oradata/orcl/salespdb/sales01.dbf' SIZE 250M AUTOEXTEND ON PATH_PREFIX = '/database/oracle/oracle/oradata/orcl/salespdb/' FILE_NAME_CONVERT = ('/database/oracle/oracle/oradata/orcl/pdbseed/', '/database/oracle/oracle/oradata/orcl/salespdb/');
```



#### 一、表设计

创建表空间的过程，创建了三个表空间，分别叫做sales，sales02，sales03，大小最大为50M，数据文件存放在/database/oracle/oracle/oradata/orcl/orclpdb/目录下面。

```
CREATE TABLESPACE SALES 

DATAFILE '/database/oracle/oracle/oradata/orcl/salespdb/sales.dbf' 

SIZE 100M AUTOEXTEND ON NEXT 50M MAXSIZE UNLIMITED 

EXTENT MANAGEMENT LOCAL SEGMENT SPACE MANAGEMENT AUTO;

 

CREATE TABLESPACE USERS01 

DATAFILE '/database/oracle/oracle/oradata/orcl/ salespdb / sales02.dbf' 

SIZE 100M AUTOEXTEND ON NEXT 50M MAXSIZE UNLIMITED 

EXTENT MANAGEMENT LOCAL SEGMENT SPACE MANAGEMENT AUTO;

 

CREATE TABLESPACE USERS02 

DATAFILE '/database/oracle/oracle/oradata/orcl/ salespdb / sales03.dbf' 

SIZE 100M AUTOEXTEND ON NEXT 50M MAXSIZE UNLIMITED 

EXTENT MANAGEMENT LOCAL SEGMENT SPACE MANAGEMENT AUTO;
```

 

#### 二、用户管理

##### 创建用户

这里创建了两个用户，分别叫做sale5deng和buyer5deng

```
SYSTEM@192.168.44.183:1521/salespdb>create role sales5deng identified sales5deng;

角色已创建。

 

SYSTEM@192.168.44.183:1521/salespdb>create role buyer5deng identified buyer5deng;

角色已创建。
```

##### 权限配置

给刚创建的两个用户添加connect，resource，create view的权限

```
SYSTEM@192.168.44.183:1521/salespdb>grant connect, resource, CREATE VIEW TO sales5deng;

授权成功。

 

SYSTEM@192.168.44.183:1521/salespdb>grant connect, resource, CREATE VIEW TO buyer5deng;

授权成功。
```

##### 表空间分配

数据库中有三个刚才创建的表空间，分别为sales，sales02，sales03.

```
SYSTEM@192.168.44.229:1521/salespdb>select tablespace_name from user_tablespaces;

 

TABLESPACE_NAME

\------------------------------

SYSTEM

SYSAUX

UNDOTBS1

TEMP

SALES

SALES02

SALES03
```

已选择 7 行。

## 九、PL/SQL设计

查找order 表中的数据，使用user的user_id，使用存储过程queryUser传入user_id，从miasha_order表中查出相应的数据记录，然后取出RUBBISH，使用RUBBISH_id，在RUBBISH中进行查询，查询出相应的记录

```
set serveroutput on;

 
create or replace procedure queryUser

(

  u_user_id in USER.user_id%type,

  u_RUBBISH_id out RUBBISH.RUBBISH_id%type

)

as

begin
 

  select RUBBISH_id into u_RUBBISH_id from order_infor where order_id = (select order_id from (select order_id from order where rownum=1 and order.user_id=u_user_id));

  dbms_output.put_line(u_RUBBISH_id);

--  select * from order_infor where order_infor.order_id=u_order_id;

  

exception

  when no_data_found then

​    dbms_output.put_line('error');

​    when others then

​    dbms_output.put_line('one error');

end queryUser;

/

 
--调用

declare

  v1 RUBBISH.RUBBISH_id%TYPE;

 
BEGIN

  queryUser('178', v1);

  dbms_output.put_line('name');

end;
输出结果如下。

Procedure QUERYUSER 已编译

423
name
 
```

PL/SQL 过程已成功完成。

## 十、备份设计

**1.编写rman  增量备份脚本**

```
\#rman_level1.sh 

\#!/bin/sh

export NLS_LANG='SIMPLIFIED CHINESE_CHINA.AL32UTF8'

export ORACLE_HOME=/home/oracle/app/oracle/product/12.1.0/dbhome_1 

export ORACLE_SID=orcl 

export PATH=$ORACLE_HOME/bin:$PATH 

rman target / nocatalog msglog=/home/oracle/rman_backup/lv1_`date +%Y%m%d-%H%M%S`_L0.log << EOF

run{

configure retention policy to redundancy 1;

configure controlfile autobackup on; 

configure controlfile autobackup format for device type disk to '/home/oracle/rman_backup/%F';

configure default device type to disk;

crosscheck backup;

crosscheck archivelog all;

allocate channel c1 device type disk;

backup as compressed backupset incremental level 1 database format '/home/oracle/rman_backup/dblv1_%d_%T_%U.bak'

  plus archivelog format '/home/oracle/rman_backup/arclv1_%d_%T_%U.bak'; 

report obsolete;

delete noprompt obsolete;

delete noprompt expired backup; 

delete noprompt expired archivelog all;
 
release channel c1;
 
}

EOF
 
Exit
```

2. 开启全备份

```
[oracle@oracle-pc ~]$ cat rman_level0.sh

[oracle@oracle-pc ~]$ ./rman_level0.sh

```

![](img\backup_1.png)

3.每天定时开始增量备份

```
[oracle@oracle-pc ~]$ cat rman_level1.sh

[oracle@oracle-pc ~]$ ./rman_level1.sh
```

4. 备份后修改数据

```
oracle@oracle-pc ~]$ sqlplus study/123@pdborcl 

SQL> create table t1 (id number,name varchar2(50)); 

SQL> insert into t1 values(1,'zhang');1 row created. 

SQL> commit;
 
SQL> select * from t1;

SQL> exit
```

​	5.删除数据库，模仿数据库被损坏

```
[oracle@oracle-pc~]$rm /home/oracle/app/oracle/oradata/orcl/pdborcl/SAMPLE_SCHEMA_users01.dbf
```

6. 修改

```
oracle@oracle-pc ~]$ sqlplus study/123@pdborcl

SQL> insert into t1 values(2,'wang');

SQL> commit;

SQL> select * from t1;

SQL> declare

n number;
 begin

  for n in 1..10000 loop
  insert into t1 values(n,'name'||n);
  end loop;
  end;
 
SQL> select * from t1;

SQL> exit
```

7. 数据库启动到mount状态

```
oracle@oracle-pc ~]$ sqlplus / as sysdba

SQL> shutdown immediate

SQL> shutdown abort

SQL> startup mount

SQL> exit
```

![](img\backup_2.png)

8. 开始恢复数据库

```
oracle@oracle-pc ~]$ rman target /

RMAN> restore database ;

RMAN> recover database;

RMAN> alter database open;

Statement processed

RMAN> exit
```

![](img\backup_3.png)

9. 查看数据库是否恢复

```
oracle@oracle-pc ~]$ sqlplus study/123@pdborcl

SQL> select * from t9;
```

![](img\backup_4.png)