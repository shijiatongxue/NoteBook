# MySQL安全管理

管理安全：用户、权限、备份还原

结构安全：外键、视图、事务

执行层：预处理

# 一、外键约束

## 1、外键

外键：Foreign Key，表中指向外部表主键的字段定义为外键

- 创建语法

```mysql
[constraint 外键名] foreign key(当前表字段名) references 外部表(主键字段);
```

- 外键字段必须与对应表的主键字段类型一致
- 外键字段本身要求是一个索引
- 一张表可以有多个外键，但是一个字段只能产生一个外键

## 2、外键约束

外键约束：当表建立外键关系后，外键就会对主表和子表里的数据产生约束效果

- 外键约束是写操作
  - 新增：子表插入的数据对应的外键必须在主表存在
  - 修改：主表的记录如果在子表存在，那么主表的主键不能修改
  - 删除：主表的记录如果在子表存在，那么主表的主键不能删除
- 外键约束控制：外键可以在定义时控制外键的约束作用
  - 控制类型
    - on update：父表更新时子表的表现
    - on delete：父表删除时子表的表现
  - 控制方式
    - cascade：级联操作，父表操作后子表跟随操作
    - set null：置空操作，父表操作后，子表关联的外键字段置空
    - restrict：严格模式，不允许父表操作（默认的）
    - no action：子表不管

> 示例

限制外键约束，一般使用**更新级联，删除置空**。

- on update cascade
- on delete set null

```mysql
create table t_50(
	id int primary key auto_increment,
    name varchar(50) not null unique
)charset utf8;

create table t_51(
	id int primary key auto_increment,
    name varchar(50) not null,
    c_id int,
    foreign key(c_id) references t_50(id) on update cascade on delete set null
)charset utf8;

insert into t_50 values(null, 'Chinese'),(null, 'Computer');
insert into t_51 values(null, 'Tony', 1),(null, 'Lilei', 2);

# 更新父表
update t_50 set id = 3 where id = 1;
# 删除父表
delete from t_50 where id = 3;
```

## 3、外键管理

外键管理：在表创建后维护外键

- 新增外键

```mysql
alter table 表名 add [constraint 外键名] foreign key(当前表字段名) references 外部表(主键字段);
```

- 删除外键

```mysql
alter table 表名 drop foreign key 外键名;
```

- 更新外键：先删除再后增

# 二、事务安全

## 1、事务

事务安全原理：先记录日志，然后同步数据表。

## 2、事务处理

事务处理：利用自动或手动的方式实现事务管理

- 自动事务处理：系统默认，操作结束直接同步到数据表（事务管理状态）
  - 事务控制：变量 autocommit（值为 on，自动提交）
- 手动事务处理
  - 开启事务：`start transaction`
  - 关闭事务：
    - 提交事务： `commit`（不同到数据表同时清空日志数据）
    - 回滚事务：`rollback` （清空日志数据）
- 事务回滚：在长事务执行中，可以在某个已经成功的节点处设置回滚点，后续回滚的话可以回到某个成功点
  - 设置回滚点：`savepoint 回滚点名字`
  - 回滚到回滚点：`rollback to 回滚点名字`

> 注意

存储引擎需要为 InnoDB 才支持事务，新版本默认支持

## 3、事务特点

事务特点：

- **A**tomicity：事务操作是一个整体，不可拆分，要么都成功，要么都失败
- **C**onsistency：事务执行前后必须处于一致性状态
- **I**solation：事务操作过程中，其他事务不可见
- **D**urability：事务一旦提交， 结果不可改变

> 扩展

- 事务锁：当一个事务开启时，另外一个事务是不能对当前事务锁占用的数据进行操作的。
  - 行锁
  - 表锁
- 脏读：一个事务在对某个数据进行操作但尚未提交，而另一个事务读到了这个历史数据。

# 三、预处理

## 1、预处理

预处理：prepare statement，一种预先编译 SQL 指令的方式

- 预处理不同于直接处理，是将执行的 SQL 指令先发送给服务器编译，然后通过指令执行
  - 发送预处理：`prepare 预处理名字 from '要执行的SQL指令';`
  - 执行预处理：`execute 预处理名字;`
- 预处理管理
  - 预处理属于会话级别：即当前用户当次连接有效
  - 删除预处理：`deallocate | drop prepare 预处理名字;`

## 2、预处理传参

- 可变参数处理

```mysql
prepare 预处理名字 from `预处理指令 变化部分使用?代替;`
```

- 数据存储在变量

```mysql
set @变量名 = 值;
```

- 使用 using 关键字传参

```mysql
execute 预处理名字 using @变量名;
```

# 四、视图

## 1、视图

视图：view，一种由 select 指令组成的虚拟表

- 视图是虚拟表，只提供结构不存储数据

```mysql
create view 视图名字 as select 指令;

select */字段名 from 视图名字;
```

## 2、视图管理

视图管理：对视图结构的管理

- 视图查看

```mysql
show tables;
show create table/view;
desc 视图名字;
```

- 视图修改

```mysql
# 更改视图
alter view 视图名 as 新的查询指令;
create or replace view 视图名 as 新的查询指令;
```

- 视图删除

```mysql
drop view 视图名;
```

## 3、视图数据操作

- 视图所有的数据操作都是最终对基表的数据操作
- 视图操作条件
  - 多基表视图：不允许操作
  - 单基表视图：允许增删改
    - 新增条件：视图的字段必须包含基表中所有不允许为空的字段
  - with check option：操作检查规则
    - 默认不需要这个规则
    - 增加此规则：视图的数据操作后，必须要保证该视图还能把通过视图操作的数据查出来（否则失败）

## 4、视图算法

视图算法：在执行过程中对于内部的 select 指令的处理方式

- 视图算法在创建时指定

```mysql
create ALGORITHM = 算法 view 视图名字 as select 指令;
```

- 视图算法一共三种
  - undefined：默认的，未定义算法，即系统自动选择算法
  - merge：合并算法，就是将视图外部查询和视图内部 select 语句合并后执行，效率高（系统有限选择）
  - template：临时表算法，即系统将视图的 select 语句查出来先得到一张临时表，然后外部再查询

# 五、数据备份与还原

## 1、表数据备份

```mysql
select * into outfile 'D:/t_40.csv' from t_40;
```

## 2、表数据还原

```mysql
load data infile 'D:/t_40.csv' into table t_40 charset utf8;
```

## 3、文件备份

InnoDB：表结构文件在 ibd 文件中，数据和索引存储在外部统一的 ibddata 文件中。

**InnoDB 不适合文件备份**，因为不管是备份一张表还是全部数据表，都需要备份整个数据存储文件 idbdata（适合整库迁移）

## 4、文件还原

InnoDB 适合整个数据库的迁移。

## 5、SQL 备份

SQL 备份：将数据库的数据以 SQL 指令的形式保存到文件中，属于逻辑备份

- SQL 备份利用 mysqldump.exe 客户端实现
- SQL 备份是将备份目标（数据表）以 SQL 指令形式，从表的结构、数据和其他信息保存到文件

```mysql
mysqldump.exe -h -P -u -p [备份选项] 数据库名字 [数据库列表] > SQL文件路径;
```

- 备份选项很多，常见的主要是数据库的备份多少
  - 全库备份：`--all-databases`所有数据库的所有表，也不需要指定数据库名字
  - 单库备份：`[--databases] 数据库` 指定数据库里的所有表
  - 部分表：`数据库名字 表1[表2 ... 表N]`

> 示例

1. 全库备份

```mysql
mysqldump.exe -uroot -p --all-databases > D:/mysql.sql
```

2. 单库备份

```mysql
mysqldump.exe -uroot -p --databases db_3 > D:/mysql3.sql
```

3. 单表或多表

```mysql
mysqldump.exe -uroot -p db_3 t_40 t_42 > D:/t_40_42.sql
```

> 小结

1. SQL 备份时一般更新频次不高的数据库的常用备份方式
2. SQL 备份是将数据表以 SQL 指令形式进行备份
3. SQL 备份能够完成的备份结构和数据，而结构和数据又是独立的，所以比较方便用作备份和还原
   - SQL 备份比较耗时，建议在闲时备份
   - SQL 备份可以根据数据表的重要性进行频次区分备份

## 6、SQL 还原

- SQL 还原可以使用 mysql.exe 进行操作

```mysql
mysql.exe -h -P -u -p [数据库名字] < SQL文件路径;
```

- SQL 还原可以在进入数据库之后利用 SQL 指令还原

```mysql
source SQL文件路径;
```

# 六、用户管理

## 1、账号管理

> 概念

**账号管理**：根据项目的需求设置和管理账号

* 账号是权限依赖的对象，先有账号才有权限
* MySQL中账号的组成分为两个部分：用户名 @ 主机地址（root@localhost）
  * 用户名为用户登录时的名字
  * 主机地址：是允许账号所在客户端的访问的客户端IP（如上述root只能在服务器本机通过客户端访问）
* 账号管理
  * 创建账号：`create user 用户名@主机地址 identified by '明文密码';`
  * 删除账号：`drop user 用户名@主机地址`

## 2、权限管理

- 账号创建之初除了登录没有其他操作权限
- 账号的管理通常需要配合权限的使用
  - 赋权：`grant 权限列表 on 数据库|*.数据表 from 用户名@主机地址;`
  - 回收：`revoke 权限列表 on 数据库|*.数据表 from 用户名@主机地址;`
  - 刷新权限：`flush priviledges;`
  - 查看权限：`show grant for 用户名@主机地址;`
- MySQL 权限列表 [下表来自木易博客](https://yangxx.net/)

| **权 限**                   | **作用范围**         | **作 用**                     |
| :-------------------------- | :------------------- | :---------------------------- |
| **all**                     | 服务器               | 所有权限                      |
| **select**                  | 表、列               | 选择行                        |
| **insert**                  | 表、列               | 插入行                        |
| **update**                  | 表、列               | 更新行                        |
| **delete**                  | 表                   | 删除行                        |
| **create**                  | 数据库、表、索引     | 创建                          |
| **drop**                    | 数据库、表、视图     | 删除                          |
| **reload**                  | 服务器               | 允许使用flush语句             |
| **shutdown**                | 服务器               | 关闭服务                      |
| **process**                 | 服务器               | 查看线程信息                  |
| **file**                    | 服务器               | 文件操作                      |
| **grant option**            | 数据库、表、存储过程 | 授权                          |
| **references**              | 数据库、表           | 外键约束的父表                |
| **index**                   | 表                   | 创建/删除索引                 |
| **alter**                   | 表                   | 修改表结构                    |
| **show databases**          | 服务器               | 查看数据库名称                |
| **super**                   | 服务器               | 超级权限                      |
| **create temporary tables** | 表                   | 创建临时表                    |
| **lock tables**             | 数据库               | 锁表                          |
| **execute**                 | 存储过程             | 执行                          |
| **replication client**      | 服务器               | 允许查看主/从/二进制日志状态  |
| **replication slave**       | 服务器               | 主从复制                      |
| **create view**             | 视图                 | 创建视图                      |
| **show view**               | 视图                 | 查看视图                      |
| **create routine**          | 存储过程             | 创建存储过程                  |
| **alter routine**           | 存储过程             | 修改/删除存储过程             |
| **create user**             | 服务器               | 创建用户                      |
| **event**                   | 数据库               | 创建/更改/删除/查看事件       |
| **trigger**                 | 表                   | 触发器                        |
| **create tablespace**       | 服务器               | 创建/更改/删除表空间/日志文件 |
| **proxy**                   | 服务器               | 代理成为其它用户              |
| **usage**                   | 服务器               | 没有权限                      |

## 3、角色管理

角色管理：role，根据角色来分配权限，用户只需要关联角色即可（MySQL8 后才有）

- 角色相关操作和语法
  - 创建角色：`create role 角色名字1[,角色名字2,...,角色名字N];`
  - 分配角色：`grant 权限列表 on 数据库|*.数据表|* to 角色名字;`
  - 绑定角色：`grant 角色名字 to 用户名@主机地址;`
  - 撤销角色：`revoke 角色名字 from 用户名@主机地址;`
  - 回收角色权限：`revoke 权限列表 on 数据库|*.数据表|* from 角色名字;`
  - 删除角色：`drop role 角色名字1[,角色名字2,...,角色名字N];`

