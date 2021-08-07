# MySQL 基本操作

## 1 SQL 语法规则

SQL语法规则：SQL是一种结构化编程语言。

- 基础 SQL 指令通常是以行为单位
- SQL 指令需要语句结束符，默认是英文分号：`;`、\g、\G
  - \G：主要用于查询数据，立体展示结果
- SQL 指令类似自然语言
- 编程中如果使用关键字和保留字，需要使用反引号``来包裹，让系统忽略

> 示例

1. 创建结构

```mysql
	create 结构类型 结构名 结构描述;
```

2. 显示结构

```mysql
# 显示结构
show 结构类型（复数）;

# 显示结构创建详情
show create 结构类型 结构名;
```

3. 数据操作

```mysql
insert into 表名 values;

select from 表名;

update 表名 set;

delete from 表名;
```

## 2 SQL 库操作

### 2.1 创建数据库

- 数据库选项包括：
  - 字符集：charset 或 character set
  - 校对集：collate
- 创建数据库会在磁盘指定位置产生一个文件夹

```mysql
create database 数据库名字 [数据库选项];
```

> 示例

1. 创建一个指定名字的数据库

```mysql
create database db_1;
```

2. 创建一个指定字符集的数据库

```mysql
create database db_2 charset utf8MB4;
```

3. 创建一个指定校对集的数据库；

```mysql
create database db_3 charset utf8MB4 collate utf8mb4_general_ci;
```

> 小结

- 数据库名字：自定义名字
  - 数字、字母和下划线组成
  - **不区分大小写**
  - 数字不能开头
  - 使用下划线法创建复杂数据库名字
- 数据库选项：非必须选项
  - 字符集：默认继承 DBMS
  - 校对集：依赖字符集

### 2.2 显示数据库

- 数据库的查看时根据用户权限限定的
- 数据库的查看分为两者查看方式
  - 查看全部数据库
  - 查看数据库创建指令

> 示例

1. 查看所有数据库

```mysql
show databases;
```

2. 显示数据库创建指令；

```mysql
show create database db_2;
```

> 小结

查看数据库的目的和应用

- 开发人员确认数据库是否存在
- DBA 维护数据库

### 2.3 使用数据库

**使用数据库：**指在进行具体 SQL 指令之前，让系统指导操作针对的是哪个数据库。

> 示例

```mysql
use db_1;
```

> 小结

使用数据库的目标

- 让系统知道后续 SQL 指令都是针对当前选择的数据库
- 简化后续 SQL 指令的复杂度（如果不指定数据库，那么所有 SQL 操作都必须强制指定数据库名字）

### 2.4 修改数据库

**修改数据库：**修改数据库的相关库选项

- 数据库名字不可修改（老版本可以）
  - 先新增
  - 后迁移
  - 最后删除
- 数据库修改分为两个部分
  - 字符集
  - 校对集
- 数据库修改指令

```mysql
alter database 数据库名字 库选项;
```

> 示例

1. 修改数据库字符集

```mysql
alter database db_1 charset gbk; 
```

2. 修改数据集校对集（如果字符集修改必须同时改变字符集）

```mysql
alter database db_1 charset gbk collate gbk_chinese_ci;
```

### 2.5 删除数据库

**删除数据集：**将当前已有数据库删除

- 删除数据库会删除数据库内所有表和数据
- 删除数据库操作要备份
- 删除数据库后，对应的存储文件夹就会消失
- 删除语法

```mysql
drop database 数据库名字;
```

> 示例

```mysql
drop database db_1;
```

> 小结

删除数据库不可逆

## 3 SQL 表（字段）操作

### 3.1 创建数据表

- 表与字段不分家，相辅相成

- 表的创建需要指定存储的数据库
  - 明确指定数据库：`数据库.表名`
  - 先使用数据库：`use 数据库名字`
- 字段至少需要指定名字、类型
- 数据库表不限定字段数量
  - 每个字段间使用 `,` 分割
  - 最后一个字段不需要逗号
- 表可以指定表选项（都有默认值）
  - 存储引擎：engine [=] 具体存储引擎
  - 字符集：[default] charset 具体字符集（继承数据库）
  - 校对集：collate （继承数据库）
- 表创建语法

```mysql 
create table [数据库名.]表名 (
	字段名 字段类型,
    ...
    字段名 字段类型
) 表选项;
```

> 示例

1. 创建简单数据表

```mysql
create table t_1(
	name varchar(50)
);
```

2. 创建数据表——多字段

```mysql
create table t_2(
	name varchar(50),
  age int,
  gender varchar(10)
);
```

3. 创建数据表——表选项

```mysql
create table t_3(
	name varchar(50)
) engine Innodb charset utf8MB4;
```

> 拓展

1. 存储引擎是指数据存储和管理的方式，MySQL 中提供了多种存储引擎，一般使用默认存储引擎。

- InnoDB
  - 默认存储引擎
  - 支持事务和外键
  - 数据统一管理
- Mylsam
  - 不支持事务和外键
  - 数据、表结构、索引独立管理
  - MySQL5.6 以后不再维护

2. 如果想创建一个与已有表一样的数据表，MySQL 提供了一种便捷的方式

```mysql
create table 表名 like 数据库表明.表名;
```

### 3.2 显示数据表

显示数据表：客户端通过指令显示已有的数据表。

- 数据表的显示跟用户权限有关
- 显示数据表有两种方式
  - 显示所有数据表
  - 显示具体数据表的创建指令

> 示例

1. 显示所有数据表——当前数据库下

```mysql
show tables;
```

2. 显示所有数据库——指定数据库

```mysql
show tables from db_3;
```

3. 显示部分关联数据表——匹配

```mysql
show tables like '%like'; # _ 表示匹配一个字符，% 表示匹配 n 个字符
```

3. 显示数据表的创建指令；

```mysql
show create table t_1; # 看到的结果未必一定是真实创建的指令（系统会加工）
```

### 3.3 查看数据表

查看数据表：指查看数据表中的具体结构

- 通常是查看字段信息
- 详细的显示字段的各项信息
- 查看语法有三种

```mysql
desc 表名：
describe 表名;
show columns from 表名;
```

> 示例

```mysql
desc t_1;
```

### 3.4 更改数据表

> 目标：了解数据表的修改内容以及修改语法、

> 概念

更改数据表：修改表名或表选项。

- 修改表名：rename 表名 to 新表名
- 修改表选项：alter table 表名

> 示例

1. 修改表名

```mysql
rename table t_1 to t1;
```

2. 修改表选项

```mysql
alter table t1 charset gbk;
```

> 提示

- 在生产环境很少使用更改数据表

### 3.5 更改字段

> 目标：了解字段更改的类型和基本语法使用。

> 概念

更改字段：指针对表创建好后，里面字段的增删改。

- 字段操作包括字段名字、类型和属性的操作
- 字段操作分为四类
  - 新增字段：add[column]
  - 更改字段名：change
  - 修改类型：modify
  - 删除字段：drop
- 字段操作还有位置处理
- 字段操作通常是在表已经存在数据进行

#### 3.5.1 新增字段

> 概念

新增字段：在表创建好后往里面增加其他字段

- 字段的新增必须同时存在字段类型
- 新增语法

```mysql
alter table 表名 add [column] 字段名 字段类型 [字段属性]  [字段位置]
```

> 示例

1. 给已经存在的t_3表增加益个字段age

```mysql
alter table t_3 add age int;
```

2. 给已经存在的t_3表增加一个字段nickname

```mysql
alter table t_3 add column nickname varchar(10);
```

#### 3.5.2 字段位置

> 目标：了解字段的位置的概念，掌握字段位置的使用。

- 放置字段位置分为两种：
  - 放在第一个字段：first
  - 放在某个字段后：after 已经存在的字段名

- 字段位置适用于追加字段、修改字段、更改字段名
- 字段位置语法

```mysql
alter tablt 表名 字段操作 字段位置;
```

> 示例

1. 为t_3表增加一个 id 字段，放在最前面

```mysql
alter table t_3 add id int first;
```

2. 在t_3表 name 字段后增加一个身份证字段 card

```mysql
alter table t_3 add card varchar(18) after name;
```

#### 3.5.3 更改字段名

> 目标：了解更改字段名的基本语法

> 概念

更改字段名：指对已经存在的字段名进行修改

- 字段名修改必须跟上字段类型
- 字段名修改语法

```mysql
alter table 表名 change 原字段名 新字段名 字段类型 [字段属性] [字段位置]
```

> 示例

修改字段名 card 为 sfz

```mysql
alter table t_3 change card sfz varchar(18);
```

#### 3.5.4 修改字段

> 目标：了解字段修改概念和基本语法，掌握字段修改的实现

> 概念

修改字段：指修改字段相关信息

- 修改字段类型、字段属性和位置
- 修改字段语法

```mysql
alter table 表名 modify 字段名 字段类型 [字段属性] [位置];
```

> 示例

修改身份证的类型 char(18) 并且位置放到 id 后面

```mysql
alter table t_3 modify sfz char(18) after id;
```

### 3.6 删除字段

> 目标：了解字段的删除的概念和语法

> 概念

删除字段：将某个不要的字段从表中删除

- 删除字段会将数据也删除（不可逆）
- 删除字段语法

```mysql
alter table 表名 drop 字段名;
```

> 示例

删除年龄字段

```mysql
alter table t_3 drop age;
```

## 4 SQL 数据操作

> 目标：掌握 MySQL 中数据的增删改查的基本操作

- 增
- 删
- 改
- 查

### 4.1 新增数据

数据插入分两种方式

- 全字段插入：insert into 表名 values(字段列表顺序对应的所有制);
- 部分字段插入：insert into 表名 (字段列表) values(字段列表对应的值顺序排列);

> 示例

1. 给 t_3 插入一条完整的数据

```mysql
insert into t_3 values(1, '410523190001011234', 'Zhang San', 'Tim');
```

2. 指定字段插入数据

```mysql
insert into t_3 (id,sfz,name) values(2, '410523190001011234', 'Tom');
```

### 4.2 删除数据

- 删除数据是一种不可逆的操作
- 删除数据通常是有条件的删除
- 删除数据语法

```mysql
delete from 表名 [where条件];
```

> 示例

删除 t_3 表中 id 为 2 的数据

```mysql
delete from t_3 where id=2;
```

### 4.3 更改数据

更新数据语法

```mysql
update 表名 set 字段=新值[,字段=新值] [where条件查询];
```

> 示例

1. 更新所有记录的身份证信息

```mysql
update t_3 set sfz='410523202001011234';
```

2. 更新某个记录的多个字段数据

```mysql
update t_3 set name='Lily',sfz='410523202001021234' where id=1;
```

### 4.4 查询数据

- 数据查看分两种方式
  - 查看全部数据：使用`*`代替所有字段
  - 查看部分数据：明确字段名，使用逗号分隔

- 查看语法

```mysql
select *|字段列表 from 表名;
```

> 示例

1. 查看 t_3 表中的所有数据

```mysql
select * from t_3;
```

2. 查看 t_3 表中 name 和身份证

```mysql
select name,sfz from t_3;
```

3. 查看 t_3 表中 id 值为 1 的信息

```mysql
select * from t_3 where id=1;
```