# Hive学习笔记

## 创建数据库

1)创建一个数据库，数据库在 HDFS 上的默认存储路径是/user/hive/warehouse/*.db

```sql
create database db_hive;
```

2）避免要创建的数据库已经存在错误，增加 if not exists 判断。（标准写法）、

```sql
create database if not exists db_hive;
```

3）创建一个数据库，指定数据库在 HDFS 上存放的位置

```sql
create database db_hive2 location '/db_hive2.db';
```



## 查询数据库

### 显示数据库

**1）显示数据库**

```sql
show databases;
```

**2）过滤显示查询的数据库**

```sql
hive> show databases like 'db_hive*'; 
db_hive 
db_hive_1
```

### 查看数据库详情

**1）显示数据库信息**

```sql
desc database db_hive;
```

**2）显示数据库详细信息**

```sql
desc database extended db_hive; 
```

### 切换当前数据库

```sql
use db_hive;
```

## 修改数据库

1）用户可以使用ALTER DATABASE 命令为某个数据库的DBPROPERTIES 设置键-值对属性值， 来描述这个数据库的属性信息。

```sql
alter database db_hive set dbproperties('createtime'='20170830');
```

2）在 hive 中查看修改结果

```sql
desc database extended db_hive;
```

## 删除数据库

1）删除数据库

```sql
drop database db_hive2;
```

2）如果删除的数据库不存在，最好采用if exists判断数据库是否存在

```sql
drop database if exists db_hive2;
```

3）如果数据库不为空，可以采用cascade命令，强制进行删除

```sql
drop database db_hive cascade;
```

## 创建表

1）建表语法

```sql
CREATE [EXTERNAL] TABLE [IF NOT EXISTS] table_nam
```

### 管理表

当我们删除一个管理表时，Hive 也会删除这个表中数据

![image-20221106110457444](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/image-20221106110457444.png)

### 外部表

 Hive 并非认为其完全拥有这份数据。删除该表并不会删除掉这份数据，不过描述表的元数据信息会被删除掉。

![image-20221106110538415](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/image-20221106110538415.png)

![image-20221106110701277](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/image-20221106110701277.png)

![image-20221106140330057](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/image-20221106140330057.png)

### 管理表与外部表的相互转换

1）查询表的类型

```sql
desc formatted student2; 
```

2）修改内部表为外部表

```sql
alter table student2 set tblproperties('EXTERNAL'='TRUE');
```

3）查询表的类型

```sql
desc formatted student2; 
```

4）修改外部表

```sql
alter table student2 set tblproperties('EXTERNAL'='FALSE');
```

5）查询表的类型

```sql
desc formatted student2; 
```



## 数据导出

### Insert导出

**将查询的结果格式化导出到本地**

```sql
insert overwrite local directory '/opt/module/hive/data/export/student1'
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
select * from student;
```

### Haoop命令导出到本地

```shell
hadoop fs -get /user/hive/warehouse/student/student.txt
/opt/module/data/export/student3.txt
```

### 清除表中的数据

注意：Truncate 只能删除管理表，不能删除外部表中数据

```sql
truncate table dept_partition;
```



## 基本查询

**(1)运算符的使用**

| 运算符 | 描述              |
| ------ | ----------------- |
| A+B    | A 和 B 相加       |
| A-B    | A 减去 B          |
| A*B    | A 和 B 相乘       |
| A/B    | A 除以 B          |
| A%B    | A 对 B 取余       |
| A&B    | A 和 B 按位取与   |
| A\|B   | A 和 B 按位取或   |
| A^B    | A 和 B 按位取异或 |
| ~A     | A 按位取反        |

**（2）常用函数**

​		**1）求总行数（count）**
​		select count(*) cnt from emp;	

​		**2）求工资的最大值（max）**
​		select max(sal) max_sal from emp;	

​		**3）求工资的最小值（min）**
​		select min(sal) min_sal from emp;	

​		**4）求工资的总和（sum）**
​		 select sum(sal) sum_sal from emp;	

​		**5）求工资的平均值（avg）**
​		 select avg(sal) avg_sal from emp;

**（3）Limit语句**

​		典型的查询会返回多行数据。LIMIT 子句用于限制返回的行数。

 **（4）比较运算符**

| 操作符                  | 支持的数据类型 | 描述                                                         |
| ----------------------- | -------------- | ------------------------------------------------------------ |
| A=B                     | 基本数据类型   | 如果A 等于 B 则返回TRUE，反之返回 FALSE                      |
| A<=>B                   | 基本数据类型   | 如果A 和B 都为 NULL，则返回 TRUE，如果一边为NULL，返回 False |
| A<>B, A!=B              | 基本数据类型   | A 或者 B 为 NULL 则返回 NULL；如果 A 不等于 B，则返回TRUE，反之返回 FALSE |
| A<B                     | 基本数据类型   | A 或者 B 为 NULL，则返回 NULL；如果 A 小于 B，则返回TRUE，反之返回 FALSE |
| A<=B                    | 基本数据类型   | A 或者 B 为 NULL，则返回 NULL；如果 A 小于等于 B，则返回 TRUE，反之返回 FALSE |
| A>B                     | 基本数据类型   | A 或者 B 为 NULL，则返回 NULL；如果 A 大于 B，则返回TRUE，反之返回 FALSE |
| A>=B                    | 基本数据类型   | A 或者 B 为 NULL，则返回 NULL；如果 A 大于等于 B，则返回 TRUE，反之返回 FALSE |
| A [NOT] BETWEEN B AND C | 基本数据类型   | 如果 A，B 或者 C 任一为NULL，则结果为 NULL。如果 A 的值大于等于 B 而且小于或等于C，则结果为 TRUE，反之为 FALSE。如果使用NOT 关键字则可达到相反的效果。 |
| A IS NULL               | 所有数据类型   | 如果A 等于 NULL，则返回TRUE，反之返回 FALSE                  |
| A IS NOT NULL           | 所有数据类型   | 如果A 不等于NULL，则返回 TRUE，反之返回 FALSE                |
| IN(数值 1, 数值 2)      | 所有数据类型   | 使用 IN 运算显示列表中的值                                   |
| A [NOT] LIKE B          | STRING 类型    | B 是一个 SQL 下的简单正则表达式，也叫通配符模式，如果 A 与其匹配的话，则返回TRUE；反之返回 FALSE。B 的表达式说明如下：‘x%’表示 A 必须以字母‘x’开头，‘%x’表示 A 必须以字母’x’结尾，而‘%x%’表示 A 包含有字母’x’,可以位于开头，结尾或者字符串中间。如果使用 NOT 关键字则可达到相反的效果。 |
| A RLIKE B, A REGEXP B   | STRING 类型    | B 是基于 java 的正则表达式，如果 A 与其匹配，则返回TRUE；反之返回 FALSE。匹配使用的是 JDK 中的正则表达式接口实现的，因为正则也依据其中的规则。例如，正则表达式必须和整个字符串 A 相匹配，而不是只需与其字符串匹配。 |

**（5）Like与RLike**

​	**1）** **使用** **LIKE** **运算选择类似的值**

​	**2）** **选择条件可以包含字符或数字**

​		% 代表零个或多个字符(任意个字符)。

​		 _ 代表一个字符。

​	**3）** **RLIKE** **子句**

​	RLIKE 子句是 Hive 中这个功能的一个扩展，其可以通过 Java 的正则表达式这个更强大的语言来指定匹配条件。

​	（1）查找名字以A 开头的员工信息

```sql
select * from emp where ename LIKE 'A%';
```

​	（2）查找名字中第二个字母为A 的员工信息

```sql
select * from emp where ename LIKE '_A%';
```

​	（3）查找名字中带有A 的员工信息

```sql
select * from emp where ename RLIKE '[A]';
```

**（6）逻辑运算符**

![1658665519809](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1658665519809.png)

## 分组

**（1）Group By语句**

​		GROUP BY 语句通常会和聚合函数一起使用，按照一个或者多个列队结果进行分组，然
后对每个组执行聚合操作。

**（2）Having 语句**

​	**having 与 where 不同点**
​	（1）where 后面不能写分组函数，而 having 后面可以使用分组函数。
​	（2）having 只用于 group by 分组统计语句。

## Join语句

**内连接：**只有进行连接的两个表中都存在与连接条件相匹配的数据才会被保留下来。

**左外连接：**JOIN 操作符左边表中符合 WHERE 子句的所有记录将会被返回。 

**右外连接：**JOIN 操作符右边表中符合 WHERE 子句的所有记录将会被返回。 

**满外连接：**将会返回所有表中符合 WHERE 语句条件的所有记录。如果任一表的指定字 段没有符合条件的值的话，那么就使用 NULL 值替代。 

**多表连接：**连接 n 个表，至少需要 n-1 个连接条件。例如：连接三个表，至少需要两个连接 条件

## 排序

**全局排序（Order By）**

Order By：全局排序，只有一个 Reducer 

​		ASC（ascend）: 升序（默认） 

​		DESC（descend）: 降序



## 分区表

​		把一个大的数据集根据业务需要分割成小的数据集。在查询时通过 WHERE 子句中的表达式选择查询所需要的指定的分区，这样的查询效率会提高很多

### 分区表基本操作

(1)建表：指明分区字段

```
hive (default)> create table 
dept_partition( deptno int, dname string,)
partitioned by (day string)
row format delimited fields terminated by '\t';
```
