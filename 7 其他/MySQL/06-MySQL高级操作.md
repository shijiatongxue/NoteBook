# MySQL 高级 SQL 操作

> 概念

高级 SQL 操作：利用 SQL 指令实现一些复杂的数据操作

## 一、新增

- 批量插入
- 蠕虫复制
- 主键冲突
  - 冲突更新
  - 冲突替换

### 1、批量插入

> 概念

批量插入：是一种优化数据逐条插入的方式

- 批量插入数据的语法与简单数据插入的语法差不多

- 批量插入分为两种

  - 全字段批量插入

  ```mysql
  insert into 表名 values(值列表1),(值列表2),...,(值列表N);
  ```

  - 部分字段批量插入

  ```mysql
  insert into 表名(字段列表) values(值列表1),(值列表2),...,(值列表N);
  ```

> 示例

1. 批量插入学生成绩

```mysql
insert into t_30 values(null, 'Tom', 'Computer', 90),(null,'Lily','Computer',92);
```

2. 批量插入部分字段

```mysql
insert into t_30(name, course) values('Tom', 'Computer'),('Lily','Computer');
```

> 注意

MySQL8 以后默认有事务安全，即批量插入要么成功要么失败，不会出现部分问题

### 2、蠕虫复制

> 概念

蠕虫复制：从已有表中复制数据直接插入到另外一张表

- 蠕虫复制的目标是快速增加表中的数据
  - 实现表中数据复制（用于数据备份或者迁移）
  - 实现数据的指数级递增（多用于测试）
- 蠕虫复制语法

```mysql
insert into 表名[(字段列表)] select 字段列表 from 表名;
```

- 注意事项
  - 字段列表必须对应上
  - 字段类型必须对应上
  - 数据冲突需要实现考虑

> 步骤

1. 确定一张需要插入数据的表（一般与要蠕虫复制数据的表结构一致）
2. 确定数据来源表
   - 字段数量匹配
   - 字段类型匹配
   - 所选字段不存在冲突
3. 使用蠕虫复制

> 示例

1. 创建一张新表，将 t_30 表中的数据迁移到新表中

```mysql
create table t_35(
	id int primary key auto_increment,
    stu_name varchar(25) not null,
    course varchar(20) not null,
    socore decimal(5,2)
)charset utf8;

insert into t_35 select * from t_30;
```

### 3、主键冲突

> 概念

主键冲突：在数据进行插入时包含主键指定，而主键在数据表已经存在

- 主键冲突的业务通常是发生在业务主键上

- 主键冲突的解决方案

  - 忽略冲突：保留原始记录

  ```mysql
  insert ignore into 表名[(字段列表)] values(值列表);
  ```

  - 冲突更新：冲突后部分字段变成更新

  ```mysql
  insert into 表名[(字段列表)] values(值列表) on duplicate key update 字段=值[,字段=值,...];
  # 1. 尝试新增
  # 2. 失败更新
  ```

  - 冲突替换：先删除原有记录，后新增记录

  ```mysql
  replace into 表名[(字段列表)] values(值列表); # 效率没有insert高（需要检查是否冲突）
  ```

> 示例

用户名作为主键的用户注册（冲突不能覆盖）：username，password，regtime

```mysql
create table t_36(
	username varchar(50) primary key,
    password char(32) not null,
    regtime int unsigned not null
)charset utf8;

insert into t_36 values('username', 'password', 123456);
# 冲突忽略
insert ignore into t_36 values('username', 'password', 123456);
```

## 二、查询

### 1、查询选项

> 概念

查询选项：用于对查询结果进行简单数据筛选

- 查询选项在 select 关键字之后，有两个互斥值	
  - all：默认，表示保留所有记录
  - distinct：去重，重复的记录（所有字段都重复）

### 2、字段选择 & 别名

> 概念

**字段选择：**根据实际需求选择要获取数据的字段信息

- 字段数据可以不一定来自数据源（select 只要有结果即可）
  - 数据常量：`select 1;`
  - 函数或变量：`select unix_timestamp(), @@version;`，（@@是系统变量的前缀，后面跟变量名）

**字段别名：**给字段取的临时名字

- 字段别名使用 as 语法实现
  - 字段名 as 别名
  - 字段名 别名
- 字段别名的目的通常是为了保护数据
  - 字段冲突：多张表同时操作有同名字段（系统默认覆盖），想保留全部
  - 数据安全：对外提供数据不适用真实字段名字

> 示例

查看系统当前时间和数据库版本

```mysql
select unix_timestamp() as now,@@version as version;
```

### 3、数据源

> 概念

数据源：from 关键字之后，提供数据。只要最终结果是一个二维表，都可以当作数据源。

- 单表数据源：`select * from 表名;`
- 多表数据源：笛卡尔积，`select * from 表名1,表名2,...,表名N;`
- 子查询数据源：`select * from (select 字段列表 from 表名) as 别名;`
  - 数据源要求是一个表
  - 如果是查询结果必须给定一个表别名

- 数据表也可以指定别名
  - 表名 as 别名
  - 表名 别名

> 小结

1. 数据源是为查询和检索提供数据支持的，使用时需要明确指定
2. 通常情况下数据源不会使用简单的多表数据源（笛卡尔积）
3. 数据表别名在负责 SQL 查询操作时非常有用，而且有些地方是必须使用（如子查询数据源）

### 4、where 子句

> 概念

where 子句：跟在 from 数据源之后，对数据进行条件匹配

- where 是在磁盘读取后，进入内存之前进行筛选
  - 不符合条件的数据不会进入内存
- where 筛选的内容因为还没进入内存，所以数据是没有被加工过的
  - 字段别名不能在 where 使用

> 示例

因为 where 是在磁盘取数据时进行条件筛选，此时数据没有进入内存，所以字段别名是无效的

```mysql
# 错误
select stu_name name,score from t_35 where name = 'Lily';
```

### 5、运算符

> 概念

运算符：用于进行运算的符号

- 比较运算符
  - \>、\<、=、>=、<=、<>
  - between A and B：包括 A 和 B 本身
  - in(数据1,数据2,...,数据N)
  - like 'pattern'：像上面样的，用于字符串比较
    - _：匹配一个任意字符
    - %：匹配任意数量字符
- 逻辑运算符
  - and、or、not
- null 运算符
  - is null、is not null

### 6、group by 子句

> 概念

group by 子句：分组统计，根据某个字段将所有的结果分类，并进行数据统计分析

- 分组的目的不是为了显示数据，一定是为了统计数据
- group by 子句一定是出现在 where 子句之后（如果同时存在）
- 分组统计可以进行统计细分：先分大组，然后大组分小组
- 分组统计需要使用统计函数
  - group_concat()：将分组的名字拼接在一块
  - any_value()：从分组里挑出任意一个值
  - count()
    - count(字段名)：null 不统计
    - count(*)：统计整个记录的数量
  - sum()：指定某个字段的和
  - max()/min()：指定某个字段的最大/最小值
  - avg()：某个字段的平均值

> 示例

多分组

```mysql
select count(*),class_name,gender from t_40 group by class_name,gender;
```

### 7、回溯统计

> 概念

回溯统计：在进行分组时（通常是多分组），每一次结果的回溯都进行一次汇总统计。

- 回溯统计语法：在统计之后使用 `with rollup`

> 示例

统计每个班级的男女同学数量，同时需要知道班级人数总数

```mysql
# 汇总统计：回溯
select count(*),class_name,gender,group_concat(name) from t_40 group by class_name,gender with rollup;
```

### 8、分组排序

> 概念

分组排序：在分组后统计结果可以根据分组进行升序或降序显示数据

- 默认系统使用升序排序
- 可以设定分组结果的排序方式
  - group by 字段名 [asc]：升序排序（默认）
  - group by 字段名 [desc]：降序排序 

### 9、having 子句

> 概念

having 子句：类似于 where 子句，是用来进行条件筛选数据的

- having 子句本身是针对分组统计结果进行条件筛选
- having 子句必须出现在 group by 之后
- having 针对的数据是在内存里已经加载的数据
- having 几乎能做 where 能做的所有事，但是 where 却不一定
  - 字段别名（where 针对磁盘数据，那时还没有）
  - 统计结果（where 在 group by 之前）
  - 分组统计函数（having 通常是针对 group by 存在的）

> 示例

获取班级人数小于 3 的班级

```mysql
select count(*) as `count`,class_name from t_40 group by class_name having `count` < 3;

# having 可以使用统计函数
select class_name from t_40 group by class_name having count(*) < 3;
```

### 10、order by 子句

> 概念

order by 子句：排序，根据某个指定字段进行升序或降序排序

- 排序的参照物是校对集
- order by 子句在 having 子句后（如果同时出现）
- 排序分为：
  - order by 字段 [asc]：默认升序
  - order by 字段 [desc]：降序
- 多字段排序：在根据某个字段排序好后，可以再细分排序

> 示例

1. 单字段排序

```mysql
select * from t_40 order by age;
```

2. 多字段排序

```mysql
select * from t_40 order by gender desc, age;
```

### 11、limit 子句

> 概念

limit 子句：限制数据的获取数量

- limit 子句必须在 order by 子句后（如果同时存在）
- limit 限制数量的方式有两种
  - limit 数量：显示获取数量（不保证一定能获取到指定数量）
  - limit 起始位置,数量：限制数据获取的位置以及数量（**分页**）

> 示例

1. 获取 t_40 表前 3 条数据

```mysql
select * from t_30 limit 3;
```

2. 获取 t_40 表 第 3 条以后的 3 条数据（数据库计数从 0 开始）

```mysql
select * from t_40 limit 3,3;
```

## 三、更新

> 概念

限制更新：即更新时对更新的记录数进行限制

- 限制更新通过 limit 来实现
- 限制更新其实是局部更新的一种手段，一般情况下是依据条件精确更新

> 示例

限制 3 个会员发放 10 元红包

```mysql
update t_41 set account = account + 10 limit 3;
```

## 四、删除

- 限制删除
- 清空数据

### 1、限制删除

> 概念

限制删除：限制要删除的记录数

- 使用 limit 实现
- 一般很少使用限制删除，通常是 where 条件精准删除

> 示例

```mysql
delete from t_41 where account = 0 limit 1;
```

### 2、清空数据

> 概念

清空数据：将表中的所有数据清除，并且将所有状态回到原始状态

- 清空数据的本质是先删除表，后创建表
- 清空数据能够让表的一些状态回到原始状态
  - 自增长重新回到初始值
- 清空语法： `truncate 表名;`

> 小结

1. 清空数据表是一种比 delete 更彻底的数据删除方式，所以使用之前必须慎重
2. 一般只会在开发阶段才会使用这种数据删除操作，如表数据发生错乱，或者业务发生变化