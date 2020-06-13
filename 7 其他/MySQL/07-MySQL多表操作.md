# 一、联合查询

## 1、联合查询

> 概念

联合查询：union，是指将多个查询结果合并成一个结果显示

- 联合查询时针对查询结果的合并
- 联合查询语法

```mysql
select 查询
	union 查询选项
select 查询
...
```

- 联合查询要求：联合查询时结果联合显示
  - **多个联合查询的字段结果数量一致**
  - 联合查询的字段来源于第一个查询语句的字段
- **查询选项：**与 select 选项类似
  - all：保留所有记录
  - distinct：保留去重记录（默认）、

> 步骤

1. 确保多个表结构一致（一般是分表的结果）
2. 确定数据要求：全部保留 or 去重
3. 使用联合查询

> 示例

1. 使用联合查询合并两个表

```mysql
select * from t_40
union all
select * from t_41;
```

2. 如果使用 where 对数据进行筛选，where 针对 select 指令，而不是针对 union 结果

```mysql
select * from t_40
union all
select * from t_41
where gender = '女';
```

## 2、联合查询排序

- order by 本身是对内存结果进行排序， union 的优先级高于 order by，所以 order by 默认是对 union 结果进行排序
- 如果想对单独 select 的结果进行排序，需要两个步骤
  - 将需要排序的 select 指令进行括号包裹（括号里使用 order by）
  - order by 必须配合 limit 才能生效

> 示例

1. 对整个结果进行排序

```mysql
select * from t_40
union all
select * from t_41;
order by age desc;
```

2. t_40 表按照年龄降序排序，t_42 表按照年龄升序排序

```mysql
(select * from t_40 order by age desc limit 9999)
union all
(select * from t_41 order by age limit 9999);
```

# 二、连接查询

## 1、交叉连接

> 概念

交叉连接：cross join，不需要任何条件的连接

- 交叉连接产生的结果就是笛卡尔积
  - 左表的每一条记录都会与右表的所有连接并保留
- 交叉连接没有实际数据价值，只是丰富了连接查询的完整性

> 示例

交叉连接 t_41 和 t_42

```mysql
select * from t_41 cross join t_42;

# 或
select * from t_41,t_42;
```

> 小结

笛卡尔积无意义，避免使用。

## 2、内连接

> 概念

内连接：[inner] join，将两张表根据指定的条件连接起来，严格连接

- 内连接是将一张表的每一条记录去另一张表根据条件匹配
  - 匹配成功：保留连接数据
  - 匹配失败：都不保留
- 内连接语法：`左表 join 右表 on 连接条件;`

## 3、外连接

> 概念

外连接：outer join，是一种不严格的连接方式

- 外连接分为两种
  - left join
  - right join
- 外连接有主表和从表之分
  - 左连接：左表是主表
  - 右连接：右表是主表
- 外连接是将主表的记录去匹配从表的记录
  - 匹配成功保留
  - 匹配失败：保留主表，从表字段置空

> 小结

- 外连接不论是左连接还是右连接，字段的顺序不影响，都是先显示左表数据，后显示右表数据
- **外连接必须使用 on 作为连接条件**（不能没有或者使用 where 替代）

## 4、自然连接

> 概念

自然连接：natural join，是一种自动寻找连接条件的连接查询。

- 自然连接包括自然内连接和自然外连接
  - 自然内连接：natural join
  - 自然外连接：natural left join/natural right join
- 自然连接条件匹配模式：自动寻找相同字段作为连接条件（字段名相同）

> 小结

对表结构要求较高，因为一般情况下表的设计很难做到完全标准或者不会出现无关同名字段。

## 5、using 关键字

> 概念

using 关键字：连接查询时如果是同名字段作为连接条件，using 可以代替 on 出现

- using 是针对同名字段 （using(id) === A.id=B.id）
- using 关键字使用后会自动合并对应字段为一个
- using 可以同时使用多个字段作为条件

# 三、子查询

> 概念

子查询：sub query，通过 select 查询结果当作另外一条 select 查询的条件或者数据源

```mysql
select * from t_45 where c_id = (select c_id from t_46 where c_name='专业名字')
```

## 1、子查询分类

> 概念

子查询分类：根据子查询出现的位置或者产生的数据效果分类。

- 位置分类
  - **from 子查询**
  - **where 子查询**
- 按子查询得到的结果分类
  - 标量子查询
  - 列子查询
  - 行子查询
  - 表子查询
  - exits 子查询
- 子查询都需要使用括号`()`进行包裹，必要时需要对子查询结果进行别名处理（from 子查询）

## 2、标量子查询

标量子查询：子查询的结果是一行一列，一个值。

```mysql
# 获取 Computer 专业的所有学生
select * from t_45 where c_id = (select c_id from t_46 where c_name='Computer');
```

## 3、列子查询

列子查询：子查询返回的结果是一列多行

```mysql
# 获取所有学生的班级信息
select * from t_46 where c_id in (select c_id from t_45);
```

列子查询通常是作为外部主查询的条件，而且是使用 in 来进行判定。

## 4、行子查询

行子查询：行子查询的结果是一行多列。

- 行子查询需要条件种构造行元素
  - (元素,元素2,...,元素N)
- 行子查询通常也是用来作为主查询的结果条件

```mysql
# 获取学生表种性别和年龄都与 Tim 相同的学生信息

# 方法1：2次标量子查询
select * from t_40 where gender = (select gender from t_42 where name = 'Tim') and age = (select age from t_42 where name = 'Tim');

# 方法2：构建条件行元素
select * from t_40 where (gender,age) = (select gender,age from t_42 where name = 'Tim');
```

> 小结

1. 行子查询可以使用多个标量子查询替代解决问题，但是行子查询的效率要比多个标量要高。
2. 如果数据来源不在一张表可以考虑使用多个标量子查询来实现。

## 5、表子查询

表子查询：子查询返回结果是多行多列。

- 表子查询多出现在 from 之后当作数据源
- 表子查询通常是为了对数据进行一次加工处理，然后再交给外部进行二次加工处理

```mysql
# 获取学生表中每个班级里年龄最大的学生信息，然后按照年龄降序排序显示

select any_value(name),max(age),classname from
(select name,age,class_name from t_42 order by age desc limit 9999) as t
group by class_name;
```

> 注意

MySQL7 以后，子查询使用 order by 必须使用 limit 才能生效

## 6、exists 子查询

exits 子查询：代入查询，将主表的每一行代入到子表进行校验

- 子查询返回的结果是布尔结果
- exits 子查询通常是作为 where 条件使用

```mysql
# 获取所有学生的班级信息

select * from t_46 c where exits(select c_id from t_45 where c.c_id = c_id);
```

## 7、比较方式

比较方式：在子查询中可以使用一些特点的比较方式

- 特定的比较方式都是基本比较符号一起使用
  - all
  - any
  - some（与 any 完全一样）
- 结果集：可以是直接的数据也可以是子查询数据

```mysql
select * from t_40 where age = some(select age from t_42);
```

## 8、总结

1. 子查询通常使用较多的是标量主查询、列子查询和 exits 子查询
2. 子查询的效率是比连接查询的效率要低的
   - where 子查询在主表每一次记录匹配时都会执行一次
     - 主表数据大，子表数据小：影响较小
     - 主表数据小，子表数据大：影响较大
   - from 子查询只执行一次，影响不大
3. 理论上讲，不限制子查询嵌套，但是考虑到效率问题，不建议使用子查询嵌套