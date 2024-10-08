基本的 `IN` 语句的语法如下：



```mysql
SELECT column1, column2, ...
FROM table_name
WHERE column_name IN (value1, value2, ..., valueN);

```
### 示例

假设有一个名为 `employees` 的表，其中包含以下数据：


```mysql

+----+----------+-------+
| id | name     | dept  |
+----+----------+-------+
|  1 | John Doe | HR    |
|  2 | Jane Doe | IT    |
|  3 | Mike     | Sales |
|  4 | Sarah    | HR    |
|  5 | Tom      | IT    |
+----+----------+-------+
```
#### 查询所有属于 HR 或 IT 部门的员工：


```mysql

SELECT *
FROM employees
WHERE dept IN ('HR', 'IT');
```
你可以这样写查询：


```mysql

SELECT *
FROM employees
WHERE dept IN (SELECT name FROM departments);
```