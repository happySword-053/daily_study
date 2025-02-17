`mysqldump` 是 MySQL 数据库自带的一个非常实用的逻辑备份工具，可用于备份数据库、表等数据，以下详细介绍其常见使用方式：

### 基本语法



```bash
mysqldump -u [用户名] -p [密码选项] [数据库名] [表名选项] > [备份文件名.sql]
```

### 常见使用场景及示例

#### 1. 备份单个数据库

  

假设要备份名为 `testdb` 的数据库，数据库用户名为 `root`，密码为 `password`，将备份文件保存为 `testdb_backup.sql`，可以使用以下命令：



```bash
mysqldump -u root -p testdb > testdb_backup.sql
```

  

执行该命令后，系统会提示你输入密码，输入正确密码后，`mysqldump` 会将 `testdb` 数据库中的所有表结构和数据备份到 `testdb_backup.sql` 文件中。

#### 2. 备份多个数据库

  

如果需要同时备份多个数据库，例如 `testdb1` 和 `testdb2`，可以使用 `--databases` 选项：

  



```bash
mysqldump -u root -p --databases testdb1 testdb2 > multiple_db_backup.sql
```

  

输入密码后，`multiple_db_backup.sql` 文件将包含 `testdb1` 和 `testdb2` 两个数据库的备份信息。

#### 3. 备份全量数据库

  

要备份 MySQL 服务器上的所有数据库，可以使用 `--all-databases` 选项：

  

```bash
mysqldump -u root -p --all-databases > all_db_backup.sql
```

  

这样会将服务器上的所有数据库（包括系统数据库）的结构和数据都备份到 `all_db_backup.sql` 文件中。

#### 4. 备份单个表

  

若只需要备份 `testdb` 数据库中的 `users` 表，可以指定表名：

  

```bash
mysqldump -u root -p testdb users > users_table_backup.sql
```

  

该命令会将 `testdb` 数据库中 `users` 表的结构和数据备份到 `users_table_backup.sql` 文件中。

#### 5. 只备份表结构，不备份数据

  

使用 `--no-data` 选项可以只备份表结构，不包含数据。例如备份 `testdb` 数据库的表结构：

  


```bash
mysqldump -u root -p --no-data testdb > testdb_structure_backup.sql
```

#### 6. 只备份数据，不备份表结构

  

使用 `--no-create-info` 选项可以只备份数据，不包含表结构。例如备份 `testdb` 数据库中 `users` 表的数据：

  


```bash
mysqldump -u root -p --no-create-info testdb users > users_data_backup.sql
```

### 其他常用选项

  

- `--add-drop-table`：在每个 `CREATE TABLE` 语句前添加 `DROP TABLE` 语句，这样在恢复备份时会先删除原有的表，避免冲突。默认情况下该选项是开启的。
- `--single-transaction`：适用于使用 InnoDB 存储引擎的数据库，它会在事务中执行备份操作，确保备份的数据是一个一致性视图，不影响其他事务的正常运行。例如：

  


```bash
mysqldump -u root -p --single-transaction testdb > testdb_consistent_backup.sql
```

  

- `--where`：可以根据指定的条件过滤要备份的数据。例如，只备份 `testdb` 数据库中 `users` 表里年龄大于 18 岁的数据：



```bash
mysqldump -u root -p --where="age > 18" testdb users > users_age_over_18_backup.sql
```

### 注意事项

  

- 确保你有足够的权限执行备份操作。一般来说，需要有对相应数据库或表的 `SELECT` 权限。
- 备份文件会存储在当前命令执行的目录下，你可以根据需要指定完整的文件路径。
- 如果备份的数据量较大，可能需要较长的时间，并且备份文件可能会占用较多的磁盘空间。