# 后端学习计划

## 学习路线
1. Linux（基本操作）（黑马）[3.28-4.24]
2. MySQL [4.25]
3. Java基础
4. JavaWeb
5. SSM框架（Spring + SpringMVC + Mybatis）
6. SpringBoot
   - 后期可接触：RabbitMQ/Kafka（消息队列）、Elasticsearch（搜索引擎）
7. Redis（小林Coding）
8. JUC
9. JVM（深入理解JAVA虚拟机）

---

# MySQL 学习笔记

## 基础概念
- 主机（电脑上的MySQL服务）
  - 用户（登录账号，比如 root/heima）
    - 数据库（多个，比如 fresh、test）
      - 表（每个库下多张，比如 emp、employee）
        - 行/列（具体数据）

---

## SQL 分类

### 一、DDL (Data Definition Language)
数据定义语言，用来定义数据库对象（数据库，表，字段）

#### 1. 数据库操作

**查询**
```sql
-- 查询所有数据库
SHOW DATABASES;

-- 查询当前数据库
SELECT DATABASE();
```

**创建**
```sql
CREATE DATABASE [IF NOT EXISTS] 数据库名 [DEFAULT CHARSET 字符集] [COLLATE 排序规则];
```

**删除**
```sql
DROP DATABASE [IF EXISTS] 数据库名;
```

**使用**
```sql
USE 数据库名;
```

#### 2. 表操作
> 首先要进入 use 这个数据库

**查询**
```sql
-- 查询当前数据库所有表
SHOW TABLES;
-- 查询表结构
DESC 表名;
-- 查询指定表的建表语句
SHOW CREATE TABLE 表名;
```

**创建**
```sql
CREATE TABLE 表名 (
    字段1 字段1类型 [COMMENT 字段1注释],
    字段2 字段2类型 [COMMENT 字段2注释],
    字段3 字段3类型 [COMMENT 字段3注释],
    字段n 字段n类型 [COMMENT 字段n注释]
) [COMMENT 表注释];
```

**数据类型**
参考：mysql数据范围.xlsx

**修改**
```sql
-- 添加字段
ALTER TABLE 表名 ADD 字段名 类型(长度) [COMMENT 注释] [约束];
-- 修改数据类型
ALTER TABLE 表名 MODIFY 字段名 新数据类型(长度);
-- 修改字段名和字段类型
ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型(长度) [COMMENT 注释] [约束];
-- 删除字段
ALTER TABLE 表名 DROP 字段名;
-- 修改表名
ALTER TABLE 表名 RENAME TO 新表名;
```

**删除**
```sql
-- 删除表
DROP TABLE [IF EXISTS] 表名;
-- 删除指定表，并重新创建该表
TRUNCATE TABLE 表名;
```

---

### 二、DML (Data Manipulation Language)
数据操作语言，用来对数据库表中的数据进行增删改

#### 1. INSERT - 添加数据

```sql
-- 给指定字段添加数据
INSERT INTO 表名(字段名1, 字段名2, ...) VALUES(值1, 值2, ...);
-- 给全部字段添加数据
INSERT INTO 表名 VALUES(值1, 值2, ...);
-- 批量添加数据
INSERT INTO 表名(字段名1, 字段名2, ...) VALUES(值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...);
INSERT INTO 表名 VALUES(值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...);
```

**注意：**
- 插入数据时，指定的字段顺序需要与值的顺序是一一对应的
- 字符串和日期型数据应该包含在引号中
- 插入的数据大小，应该在字段的规定范围内

#### 2. UPDATE - 修改数据

```sql
UPDATE 表名 SET 字段名1 = 值1, 字段名2 = 值2, ... [WHERE 条件];
```

**注意：** 修改语句的条件可以有，也可以没有，如果没有条件，则会修改整张表的所有数据。

#### 3. DELETE - 删除数据

```sql
DELETE FROM 表名 [WHERE 条件];
```

**注意：**
- DELETE语句的条件可以有，也可以没有，如果没有条件，则会删除整张表的所有数据
- DELETE语句**不能删除某一个字段的值**（可以使用UPDATE）

---

### 三、DQL (Data Query Language)
数据查询语言，用来查询数据库中表的记录

#### 1. 基本查询

```sql
-- 查询多个字段
SELECT 字段1, 字段2, 字段3 ... FROM 表名;
SELECT * FROM 表名;
-- 设置别名（AS可以省略）
SELECT 字段1 [AS 别名1], 字段2 [AS 别名2] ... FROM 表名;
-- 去除重复记录
SELECT DISTINCT 字段列表 FROM 表名;
```

#### 2. 条件查询

```sql
SELECT 字段列表 FROM 表名 WHERE 条件列表;
```

**比较运算符**

| 比较运算符 | 功能 |
|-----------|------|
| > | 大于 |
| >= | 大于等于 |
| < | 小于 |
| <= | 小于等于 |
| = | 等于 |
| <> 或 != | 不等于 |
| BETWEEN ... AND ... | 在某个范围之内（含最小、最大值）|
| IN(...) | 在 IN 之后的列表中的值，多选一 |
| LIKE 占位符 | 模糊匹配（_ 匹配单个字符，% 匹配任意个字符）|
| IS NULL | 是 NULL |

**逻辑运算符**

| 逻辑运算符 | 功能 |
|-----------|------|
| AND 或 && | 并且（多个条件同时成立）|
| OR 或 \|\| | 或者（多个条件任意一个成立）|
| NOT 或 ! | 非，不是 |

#### 3. 聚合函数
> null 不参与运算！

- COUNT - 统计数量
- MAX - 最大值
- MIN - 最小值
- AVG - 平均值
- SUM - 求和

```sql
SELECT 聚合函数(字段列表) FROM 表名;
```

#### 4. 分组查询

```sql
SELECT 字段列表 FROM 表名 [WHERE 条件] GROUP BY 分组字段名 [HAVING 分组后过滤条件];
```

**WHERE 与 HAVING 区别：**
- **执行时机不同**：WHERE 是分组之前进行过滤，不满足 WHERE 条件，不参与分组；而 HAVING 是分组之后对结果进行过滤
- **判断条件不同**：WHERE 不能对聚合函数进行判断，而 HAVING 可以

**示例：**
```sql
-- 1. 根据性别分组，统计男性员工和女性员工的数量
SELECT gender, COUNT(*) FROM emp GROUP BY gender;
-- 2. 根据性别分组，统计男性员工和女性员工的平均年龄
SELECT gender, AVG(age) FROM emp GROUP BY gender;
-- 3. 查询年龄小于45的员工，并根据工作地址分组，获取员工数量大于等于3的工作地址
SELECT workaddress, COUNT(*) FROM emp WHERE age < 45 GROUP BY workaddress HAVING COUNT(*) >= 3;
```

**注意：**
- 执行顺序：WHERE > 聚合函数 > HAVING
- 分组之后，查询的字段一般为聚合函数和分组字段，查询其他字段无任何意义

#### 5. 排序查询

```sql
SELECT 字段列表 FROM 表名 ORDER BY 字段1 排序方式1, 字段2 排序方式2;
```

**排序方式：**
- ASC：升序（默认值）
- DESC：降序

**注意：** 如果是多字段排序，当第一个字段值相同时，才会根据第二个字段进行排序。

#### 6. 分页查询

```sql
SELECT 字段列表 FROM 表名 LIMIT 起始索引, 查询记录数;
```

**注意：**
- 起始索引从0开始，**起始索引 = (查询页码 - 1) × 每页显示记录数**
- 分页查询是数据库的方言，不同的数据库有不同的实现，MySQL中是 LIMIT
- 如果查询的是第一页数据，起始索引可以省略，直接简写为 `LIMIT 10`

#### 编写顺序与执行顺序
![SQL执行顺序](media/image1.png)

---

### 四、DCL (Data Control Language)
数据控制语言，用来创建数据库用户、控制数据库的访问权限

#### 1. 管理用户

```sql
-- 查询用户
USE mysql;
SELECT * FROM user;

-- 创建用户（主机名：%表示任意主机可以访问，localhost表示只能在当前主机访问）
CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';
-- 修改用户密码
ALTER USER '用户名'@'主机名' IDENTIFIED WITH mysql_native_password BY '新密码';
-- 删除用户
DROP USER '用户名'@'主机名';
```

**注意：**
- 主机名可以使用 % 通配
- 这类SQL开发人员操作的比较少，主要是DBA（数据库管理员）使用

#### 2. 权限控制

```sql
-- 查询权限
SHOW GRANTS FOR '用户名'@'主机名';
-- 授予权限
GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名';
-- 撤销权限
REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';
```

**注意：**
- 多个权限之间，使用逗号分隔
- 授权时，数据库名和表名可以使用 * 进行通配，代表所有

---

## 函数

### 1. 字符串函数

| 函数 | 功能 |
|------|------|
| CONCAT(S1, S2, ... Sn) | 字符串拼接，将 S1，S2，... Sn 拼接成一个字符串 |
| LOWER(str) | 将字符串 str 全部转为小写 |
| UPPER(str) | 将字符串 str 全部转为大写 |
| LPAD(str, n, pad) | 左填充，用字符串 pad 对 str 的左边进行填充，达到 n 个字符串长度 |
| RPAD(str, n, pad) | 右填充，用字符串 pad 对 str 的右边进行填充，达到 n 个字符串长度 |
| TRIM(str) | 去掉字符串头部和尾部的空格 |
| SUBSTRING(str, start, len) | 返回从字符串 str 从 start 位置起的 len 个长度的字符串 |

```sql
SELECT 函数();
-- 示例：企业员工工号统一为5位数，不足5位前面补0
UPDATE emp SET workno = LPAD(workno, 5, '0');
```

> 注意：字符串要用引号括起来

### 2. 数值函数

| 函数 | 功能 |
|------|------|
| CEIL(x) | 向上取整 |
| FLOOR(x) | 向下取整 |
| MOD(x, y) | 返回 x/y 的模（取余）|
| RAND() | 返回 0~1 内的随机数 |
| ROUND(x, y) | 对 x 四舍五入，保留 y 位小数 |

```sql
-- 生成一个六位数的随机验证码
SELECT LPAD(ROUND(RAND() * 1000000, 0), 6, '0');
```

### 3. 日期函数

| 函数 | 功能 |
|------|------|
| CURDATE() | 返回当前日期 |
| CURTIME() | 返回当前时间 |
| NOW() | 返回当前日期和时间 |
| YEAR(date) | 获取指定 date 的年份 |
| MONTH(date) | 获取指定 date 的月份 |
| DAY(date) | 获取指定 date 的日期（日）|
| DATE_ADD(date, INTERVAL expr type) | 返回一个日期/时间值加上一个时间间隔 expr 后的时间值 |
| DATEDIFF(date1, date2) | 返回起始时间 date1 和结束时间 date2 之间的天数 |

```sql
-- 查询所有员工的入职天数，并根据其降序排序
SELECT emp.name, DATEDIFF(CURDATE(), emp.entrydate) AS 'entrydays'
FROM emp
ORDER BY entrydays DESC;
```

### 4. 流程函数

| 函数 | 功能 |
|------|------|
| IF(value, t, f) | 如果 value 为 true，则返回 t，否则返回 f |
| IFNULL(value1, value2) | 如果 value1 不为空（空是null），返回 value1，否则返回 value2 |
| CASE WHEN [val1] THEN [res1] ... ELSE [default] END | 如果 val1 为 true，返回 res1，... 否则返回 default 默认值 |
| CASE [expr] WHEN [val1] THEN [res1] ... ELSE [default] END | 如果 expr 的值等于 val1，返回 res1，... 否则返回 default 默认值 |

```sql
-- 案例：统计班级各个学员的成绩，展示的规则如下：
-- >=85，展示优秀
-- >=60，展示及格
-- 否则，展示不及格
SELECT
    id,
    name,
    CASE
        WHEN math >= 85 THEN 'great'
        WHEN math >= 60 THEN 'ok'
        ELSE 'no'
    END AS '数学'
FROM student_score;
```

---

## 约束

### 1. 约束示例

| 字段名 | 字段含义 | 字段类型 | 约束条件 | 约束关键字 |
|--------|---------|---------|---------|-----------|
| id | ID唯一标识 | int | 主键，并且自动增长 | PRIMARY KEY, AUTO_INCREMENT |
| name | 姓名 | varchar(10) | 不为空，并且唯一 | NOT NULL, UNIQUE |
| age | 年龄 | int | 大于0，并且小于等于120 | CHECK |
| status | 状态 | char(1) | 如果没有指定该值，默认为1 | DEFAULT |
| gender | 性别 | char(1) | 无 | - |

```sql
CREATE TABLE 表名 (
    字段名 数据类型,
    -- 字段级约束
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(10) NOT NULL UNIQUE,
    age INT CHECK (age > 0 AND age <= 120),
    status CHAR(1) DEFAULT '1',
    gender CHAR(1)
);
```

### 2. 外键约束
用来让两张表的数据之间建立联系，从而保证数据的一致性

**添加外键**

方法一：创建表时添加
```sql
CREATE TABLE 表名 (
    字段名 数据类型,
    [CONSTRAINT] [外键名称] FOREIGN KEY (外键字段名) REFERENCES 主表(主表列名)
);
```

方法二：表已创建，添加外键
```sql
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名) REFERENCES 主表(主表列名);
```

**删除外键**
```sql
ALTER TABLE 表名 DROP FOREIGN KEY 外键名称;
```

**删除/更新行为**

| 行为 | 说明 |
|------|------|
| NO ACTION | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新（与 RESTRICT 一致）|
| RESTRICT | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新（与 NO ACTION 一致）|
| CASCADE | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有，则也删除/更新外键在子表中的记录 |
| SET NULL | 当在父表中删除对应记录时，首先检查该记录是否有对应外键，如果有则设置子表中该外键值为 null（这就要求该外键允许取 null）|
| SET DEFAULT | 父表有变更时，子表将外键列设置成一个默认的值（InnoDB 不支持）|

```sql
ALTER TABLE 表名 ADD CONSTRAINT 外键名称
FOREIGN KEY (外键字段) REFERENCES 主表名(主表字段名)
ON UPDATE CASCADE ON DELETE CASCADE;
```


