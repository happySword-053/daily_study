# MySQL DDL 语句详细介绍

  

在 MySQL 中，DDL（Data Definition Language，数据定义语言）用于定义数据库、表、视图、索引等数据库对象的结构。以下是常见的 MySQL DDL 语句的详细介绍：

## 1. 数据库操作

### 创建数据库

  

收起



```mysql
CREATE DATABASE database_name;
```

  

- **作用**：创建一个新的数据库。
- **示例**：

  

收起



```mysql
CREATE DATABASE test_db;
```

### 删除数据库

  

收起

sql

```mysql
DROP DATABASE database_name;
```

  

- **作用**：删除指定的数据库及其所有对象。
- **示例**：

  

收起

sql

```mysql
DROP DATABASE test_db;
```

### 使用数据库

  

收起

sql

```mysql
USE database_name;
```

  

- **作用**：指定要使用的数据库，后续的操作将在该数据库中执行。
- **示例**：

  

收起

sql

```mysql
USE test_db;
```

## 2. 表操作

### 创建表

  

收起

sql

```mysql
CREATE TABLE table_name (
    column1 datatype [constraint],
    column2 datatype [constraint],
    ...
    [table_constraint]
);
```

  

- **作用**：创建一个新的表。
- **参数说明**：
    - `column1, column2, ...`：列名。
    - `datatype`：列的数据类型，如 `INT`、`VARCHAR`、`DATE` 等。
    - `constraint`：列约束，如 `NOT NULL`、`PRIMARY KEY`、`UNIQUE` 等。
    - `table_constraint`：表约束，如 `FOREIGN KEY` 等。
- **示例**：


```mysql
CREATE TABLE employees (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50) NOT NULL,
    age INT,
    department VARCHAR(50)
);
```

### 删除表


```mysql
DROP TABLE table_name;
```

  

- **作用**：删除指定的表及其所有数据。
- **示例**：


```mysql
DROP TABLE employees;
```

### 修改表结构

#### 添加列



```mysql
ALTER TABLE table_name ADD COLUMN column_name datatype [constraint];
```

  

- **作用**：在表中添加一个新的列。
- **示例**：

  

收起

sql

```mysql
ALTER TABLE employees ADD COLUMN salary DECIMAL(10, 2);
```

#### 删除列

  

收起

sql

```mysql
ALTER TABLE table_name DROP COLUMN column_name;
```

  

- **作用**：从表中删除指定的列。
- **示例**：

  

收起

sql

```mysql
ALTER TABLE employees DROP COLUMN age;
```

#### 修改列的数据类型

  

收起

sql

```mysql
ALTER TABLE table_name MODIFY COLUMN column_name new_datatype [constraint];
```

  

- **作用**：修改表中指定列的数据类型。
- **示例**：

  

收起

sql

```mysql
ALTER TABLE employees MODIFY COLUMN salary DECIMAL(12, 2);
```

#### 重命名列

  

收起

sql

```mysql
ALTER TABLE table_name CHANGE COLUMN old_column_name new_column_name datatype [constraint];
```

  

- **作用**：重命名表中的列。
- **示例**：

  

收起

sql

```mysql
ALTER TABLE employees CHANGE COLUMN name full_name VARCHAR(100);
```

#### 重命名表

  

收起

sql

```mysql
RENAME TABLE old_table_name TO new_table_name;
```

  

- **作用**：重命名指定的表。
- **示例**：

  

收起

sql

```mysql
RENAME TABLE employees TO staff;
```

## 3. 索引操作

### 创建索引

  

收起

sql

```mysql
CREATE [UNIQUE] INDEX index_name ON table_name (column1, column2, ...);
```

  

- **作用**：在表的指定列上创建索引，`UNIQUE` 关键字表示创建唯一索引。
- **示例**：

  

收起

sql

```mysql
CREATE INDEX idx_name ON employees (full_name);
```

### 删除索引

  

收起

sql

```mysql
DROP INDEX index_name ON table_name;
```

  

- **作用**：删除指定表上的索引。
- **示例**：

  

收起

sql

```mysql
DROP INDEX idx_name ON employees;
```

## 4. 视图操作

### 创建视图

  

收起

sql

```mysql
CREATE VIEW view_name AS
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

  

- **作用**：创建一个虚拟表，其内容由查询语句定义。
- **示例**：

  

收起

sql

```mysql
CREATE VIEW young_employees AS
SELECT *
FROM employees
WHERE age < 30;
```

### 删除视图

  

收起

sql

```mysql
DROP VIEW view_name;
```

  

- **作用**：删除指定的视图。
- **示例**：

  

收起

sql

```mysql
DROP VIEW young_employees;
```

  

以上就是 MySQL 中常见的 DDL 语句的详细介绍，通过这些语句可以方便地管理数据库和表的结构。

  

你可以将上述内容复制到文本编辑器中，保存为 `.md` 后缀的文件，即可得到对应的 Markdown 文档。