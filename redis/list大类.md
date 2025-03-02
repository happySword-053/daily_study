特点： 单key多value ，一个双端链表结构

在 Redis 中，List 类型是一种有序的字符串列表，以下是其常见操作：

### 插入操作

  

- **`LPUSH key value1 [value2 ...]`**：将一个或多个值从列表的左侧（头部）插入。例如 `LPUSH mylist "apple" "banana"`，会将 `"banana"` 和 `"apple"` 依次插入到 `mylist` 列表的头部。
- **`RPUSH key value1 [value2 ...]`**：把一个或多个值从列表的右侧（尾部）插入。如 `RPUSH mylist "cherry"`，会将 `"cherry"` 插入到 `mylist` 列表的尾部。

### 删除操作

  

- **`LPOP key`**：移除并返回列表的头元素。例如执行 `LPOP mylist`，会移除 `mylist` 列表最左边的元素并返回该元素的值。
- **`RPOP key`**：移除并返回列表的尾元素。比如 `RPOP mylist`，会移除 `mylist` 列表最右边的元素并返回其值。
- **`LREM key count value`**：根据 `count` 的值，从列表中移除与 `value` 相等的元素。当 `count > 0` 时，从列表头部开始移除 `count` 个匹配元素；当 `count < 0` 时，从列表尾部开始移除 `|count|` 个匹配元素；当 `count = 0` 时，移除列表中所有匹配元素。例如 `LREM mylist 2 "apple"`，会从 `mylist` 列表头部开始移除 2 个值为 `"apple"` 的元素。

### 获取操作

  

- **`LRANGE key start stop`**：返回列表中指定区间内的元素，`start` 和 `stop` 为元素的索引，索引从 0 开始。例如 `LRANGE mylist 0 -1`，会返回 `mylist` 列表中的所有元素，`-1` 表示最后一个元素。
- **`LINDEX key index`**：获取列表中指定索引位置的元素。如 `LINDEX mylist 1`，会返回 `mylist` 列表中索引为 1 的元素。

### 阻塞操作

  

- **`BLPOP key1 [key2 ...] timeout`**：从 `key1`、`key2` 等列表的表头弹出一个元素。如果所有列表都没有元素，会阻塞等待 `timeout` 秒；当 `timeout = 0` 时，会一直阻塞等待，直到有元素可弹出。例如 `BLPOP mylist1 mylist2 5`，会尝试从 `mylist1` 或 `mylist2` 的头部弹出元素，若都为空则阻塞 5 秒。
- **`BRPOP key1 [key2 ...] timeout`**：与 `BLPOP` 类似，但从列表的表尾弹出元素。

### 其他操作

  

- **`LLEN key`**：返回列表的长度（元素个数）。比如 `LLEN mylist`，会返回 `mylist` 列表中的元素数量。
- **`LTRIM key start stop`**：对列表进行修剪，只保留指定区间 `[start, stop]` 的元素，其他元素将被删除。例如 `LTRIM mylist 1 3`，会使 `mylist` 列表仅保留索引 1 到 3 的元素。
- **`RPOPLPUSH source destination`**：从 `source` 列表的表尾弹出一个元素，并将其插入到 `destination` 列表的表头。例如 `RPOPLPUSH list1 list2`，会把 `list1` 列表的尾元素移动到 `list2` 列表的头部。

分享

list类型的操作适用于什么场景？

如何使用Redis的List类型实现队列和栈？

如何获取Redis中List类型的长度？