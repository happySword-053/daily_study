在 MySQL 中，DML（Data Manipulation Language，数据操纵语言）主要用于对数据库中的数据进行操作，包括插入、查询、更新和删除数据。以下是详细介绍：

### 1. 插入数据（INSERT）

#### 插入单条记录

  

收起

sql

```
INSERT INTO table_name (column1, column2, ...)
VALUES (value1, value2, ...);
```

  

- **作用**：向指定表中插入一条新记录。
- **参数说明**：
    - `table_name`：要插入数据的表名。
    - `column1, column2, ...`：要插入数据的列名。
    - `value1, value2, ...`：对应列的值。
- **示例**：

  

收起

sql

```
INSERT INTO employees (name, age, department)
VALUES ('John Doe', 30, 'HR');
```

#### 插入多条记录

  

收起

sql

```
INSERT INTO table_name (column1, column2, ...)
VALUES (value1_1, value1_2, ...),
       (value2_1, value2_2, ...),
       ...;
```

  

- **作用**：向指定表中插入多条新记录。
- **示例**：

  

收起

sql

```
INSERT INTO employees (name, age, department)
VALUES ('Jane Smith', 25, 'IT'),
       ('Bob Johnson', 35, 'Finance');
```

#### 插入查询结果

  

收起

sql

```
INSERT INTO table_name (column1, column2, ...)
SELECT column1, column2, ...
FROM another_table
WHERE condition;
```

  

- **作用**：将一个查询的结果插入到指定表中。
- **示例**：

  

收起

sql

```
INSERT INTO new_employees (name, age)
SELECT name, age
FROM old_employees
WHERE department = 'Sales';
```

### 2. 查询数据（SELECT）

#### 基本查询

  

收起

sql

```
SELECT column1, column2, ...
FROM table_name;
```

  

- **作用**：从指定表中查询指定列的数据。
- **示例**：

  

收起

sql

```
SELECT name, age
FROM employees;
```

#### 查询所有列

  

收起

sql

```
SELECT *
FROM table_name;
```

  

- **作用**：从指定表中查询所有列的数据。
- **示例**：

  

收起

sql

```
SELECT *
FROM employees;
```

#### 条件查询

  

收起

sql

```
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

  

- **作用**：从指定表中查询满足指定条件的数据。
- **示例**：

  

收起

sql

```
SELECT name, age
FROM employees
WHERE age > 25;
```

#### 排序查询

  

收起

sql

```
SELECT column1, column2, ...
FROM table_name
ORDER BY column1 [ASC|DESC], column2 [ASC|DESC], ...;
```

  

- **作用**：从指定表中查询数据，并按照指定列进行排序。`ASC` 表示升序，`DESC` 表示降序，默认是升序。
- **示例**：

  

收起

sql

```
SELECT name, age
FROM employees
ORDER BY age DESC;
```

#### 分页查询

  

收起

sql

```
SELECT column1, column2, ...
FROM table_name
LIMIT offset, count;
```

  

- **作用**：从指定表中查询指定范围的数据。`offset` 表示偏移量，即从第几条记录开始查询；`count` 表示查询的记录数量。
- **示例**：

  

收起

sql

```
SELECT name, age
FROM employees
LIMIT 0, 10; -- 查询前 10 条记录
```

### 3. 更新数据（UPDATE）

  

收起

sql

```
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```

  

- **作用**：更新指定表中满足指定条件的记录。
- **示例**：

  

收起

sql

```
UPDATE employees
SET age = age + 1
WHERE department = 'HR';
```

### 4. 删除数据（DELETE）

  

收起

sql

```
DELETE FROM table_name
WHERE condition;
```

  

- **作用**：从指定表中删除满足指定条件的记录。
- **示例**：

  

收起

sql

```
DELETE FROM employees
WHERE age > 60;
```

### 注意事项

  

- 在使用 `UPDATE` 和 `DELETE` 语句时，务必小心 `WHERE` 子句，因为如果省略 `WHERE` 子句，将会更新或删除表中的所有记录。
- 在进行复杂的查询时，可以使用 `JOIN` 语句来连接多个表，以获取更全面的数据。例如：

  

收起

sql

```
SELECT employees.name, departments.department_name
FROM employees
JOIN departments ON employees.department_id = departments.id;
```

  

以上就是 MySQL 中常见的 DML 语句的详细介绍，通过这些语句可以方便地对数据库中的数据进行操作。

但是看你的回答中DDL和DML都有删除操作

确实，DDL（数据定义语言）和 DML（数据操纵语言）里都有和 “删除” 相关的操作，但它们在操作对象和操作本质上有明显区别，下面为你详细解释：

### DDL 中的删除操作

  

DDL 主要用于定义和管理数据库对象的结构，它的删除操作针对的是数据库对象本身，例如数据库、表、索引、视图等。一旦执行删除操作，相关对象及其包含的所有数据都会被永久性地移除。

#### 1. 删除数据库

  

收起

sql

```
DROP DATABASE database_name;
```

  

这会删除指定名称的数据库，该数据库下的所有表、视图、存储过程等对象都会被一并删除，且无法恢复（除非有备份）。比如：

  

收起

sql

```
DROP DATABASE test_db;
```

#### 2. 删除表

  

收起

sql

```
DROP TABLE table_name;
```

  

此操作会删除指定的表，表中的所有数据以及与该表相关的索引、约束等都会被删除。示例如下：

  

收起

sql

```
DROP TABLE employees;
```

#### 3. 删除索引

  

收起

sql

```
DROP INDEX index_name ON table_name;
```

  

该语句用于删除指定表上的索引，索引本身被移除，但表和表中的数据不受影响。例如：

  

收起

sql

```
DROP INDEX idx_employees_name ON employees;
```

#### 4. 删除视图

  

收起

sql

```
DROP VIEW view_name;
```

  

执行此语句会删除指定的视图，视图只是一个虚拟表，它本身不存储数据，所以删除视图不会影响底层表的数据。示例：

  

收起

sql

```
DROP VIEW employee_summary;
```

### DML 中的删除操作

  

DML 侧重于对数据库中的数据进行操作，它的删除操作是针对表中的数据记录，而不会删除表或其他数据库对象的结构。

  

收起

sql

```
DELETE FROM table_name WHERE condition;
```

  

该语句用于从指定表中删除满足特定条件的记录。如果省略 `WHERE` 子句，将删除表中的所有记录，但表结构依然存在。例如：

  

收起

sql

```
-- 删除 employees 表中 age 大于 60 的记录
DELETE FROM employees WHERE age > 60;

-- 删除 employees 表中的所有记录
DELETE FROM employees;
```

### 总结

  

- **DDL 删除**：是对数据库对象结构的删除，一旦执行，相关对象及其关联的数据和定义会被彻底清除。
- **DML 删除**：仅针对表中的数据记录进行删除，表的结构、索引、约束等保持不变。

  

在实际使用中，要根据具体需求谨慎选择使用 DDL 还是 DML 的删除操作，避免误删重要的数据或对象。