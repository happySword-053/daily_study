
Redis 的 String 类型是最基本的数据类型，以下是其常见操作：
- ex seconds 以秒为单位设置过期时间
- px xxx 以毫秒为单位设置过期时间 
### 存储与获取

  

- **`SET key value`**：设置键值对，若键已存在则覆盖其原有值。例如 `SET name "Alice"`。
- **`GET key`**：根据键获取对应的值，如 `GET name`。
- **`GETSET key value`**：先返回键原来的值，再将新值设置给该键。比如 `GETSET count 10`，会先返回 `count` 原来的值，然后将 `count` 的值设为 `10`。

### 批量操作

  

- **`MSET key1 value1 [key2 value2 ...]`**：同时设置多个键值对，例如 `MSET name "Bob" age "30"`。
- **`MGET key1 [key2 ...]`**：同时获取多个键对应的值，如 `MGET name age`。
- **`MSETNX key1 value1 [key2 value2 ...]`**：只有当所有键都不存在时，才设置多个键值对，只要有一个键已存在则所有设置都不执行。

### 追加操作

  

- **`APPEND key value`**：将给定值追加到键对应值的末尾，并返回追加后值的总长度。例如 `APPEND message " world"`，假设 `message` 原来的值为 `"hello"`，执行后其值变为 `"hello world"` 。

### 长度获取

  

- **`STRLEN key`**：获取键对应值的长度，如 `STRLEN name`。

### 数字增减操作

  

- **`INCR key`**：将键中存储的数字值增 1，若键不存在则初始化为 1 后再增 1，只能对数字值操作。例如 `INCR visit_count`。
- **`DECR key`**：将键中存储的数字值减 1，如 `DECR stock_count`。
- **`INCRBY key increment`**：将键中存储的数字值增加指定的增量，如 `INCRBY score 5` 。
- **`DECRBY key decrement`**：将键中存储的数字值减少指定的减量，例如 `DECRBY balance 10` 。

### 截取与替换

  

- **`GETRANGE key start end`**：获取值中指定范围的子字符串，索引从 0 开始。比如 `GETRANGE text 0 4`，会获取 `text` 值中索引 0 到 4 的子串。
- **`SETRANGE key offset value`**：从指定的偏移量开始，用给定值替换键对应值的子字符串。例如 `SETRANGE name 2 "xy"`，若 `name` 的值为 `"abc"`，执行后变为 `"axy"`。

### 过期时间设置

  

- **`SETEX key seconds value`**：设置键值对并同时设置过期时间（单位为秒），如 `SETEX token 3600 "abc123"`，表示 `token` 键 3600 秒后过期。
- **`EXPIRE key seconds`**：为已存在的键设置过期时间（单位为秒），例如 `EXPIRE cache_key 60`
- **`PEXPIRE`**：和 `EXPIRE` 类似，但设置的过期时间单位为毫秒。语法是 `PEXPIRE key milliseconds`。如 `PEXPIRE mystring 60000` 表示键 `mystring` 在 60000 毫秒（即 60 秒）后过期。

### 其他操作

  

- **`DEL key [key ...]`**：删除给定的键及其对应的值，如 `DEL name`。
- **`EXISTS key`**：判断键是否存在，存在返回 1，不存在返回 0，例如 `EXISTS user_id`。
- **`SETNX key value`**：只有当键不存在时，才设置键的值，若键已存在则不执行任何操作，返回 0；设置成功返回 1 ，可用于解决 `SET` 覆盖问题 。

分享

除了string类型，Redis还有哪些常用的数据类型？

详细介绍下Redis的List类型的操作

如何使用Redis的Set类型进行数据存储和操作？
# 分布式锁
- setnx key value
