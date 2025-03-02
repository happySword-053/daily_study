和list很详细，但是无重复

Redis 的 Set 是无序且唯一的字符串集合，以下为你详细介绍 Set 类型的常见操作：

### 集合元素操作

#### 添加元素

  

- **`SADD key member [member ...]`**：将一个或多个成员添加到指定集合中。若成员已存在于集合中则忽略，若集合不存在则先创建集合再添加成员。例如 `SADD myset "apple" "banana"`，会将 `"apple"` 和 `"banana"` 添加到 `myset` 集合中。

#### 删除元素

  

- **`SREM key member [member ...]`**：从指定集合中移除一个或多个成员。若成员不存在则忽略。如 `SREM myset "apple"`，会从 `myset` 集合中移除 `"apple"`。

#### 检查元素是否存在

  

- **`SISMEMBER key member`**：检查指定成员是否存在于集合中。若存在返回 1，不存在返回 0。例如 `SISMEMBER myset "banana"`，可判断 `"banana"` 是否在 `myset` 集合里。

#### 获取集合所有元素

  

- **`SMEMBERS key`**：返回集合中的所有成员。如 `SMEMBERS myset`，会列出 `myset` 集合的所有元素。

#### 获取集合元素数量

  

- **`SCARD key`**：返回集合中元素的数量。例如 `SCARD myset`，可得到 `myset` 集合的元素个数。

### 集合间操作

#### 交集操作

  

- **`SINTER key [key ...]`**：返回给定多个集合的交集元素。例如有集合 `set1` 包含 `{"apple", "banana", "cherry"}`，集合 `set2` 包含 `{"banana", "date"}`，执行 `SINTER set1 set2` 会返回 `{"banana"}`。
- **`SINTERSTORE destination key [key ...]`**：将给定多个集合的交集元素存储到 `destination` 集合中，同时返回交集元素的数量。如 `SINTERSTORE result set1 set2`，会把 `set1` 和 `set2` 的交集元素存储到 `result` 集合中。

#### 并集操作

  

- **`SUNION key [key ...]`**：返回给定多个集合的并集元素。例如对于上述的 `set1` 和 `set2`，执行 `SUNION set1 set2` 会返回 `{"apple", "banana", "cherry", "date"}`。
- **`SUNIONSTORE destination key [key ...]`**：将给定多个集合的并集元素存储到 `destination` 集合中，同时返回并集元素的数量。如 `SUNIONSTORE result set1 set2`，会把 `set1` 和 `set2` 的并集元素存储到 `result` 集合中。

#### 差集操作

  

- **`SDIFF key [key ...]`**：返回第一个集合与其他集合的差集元素。以 `set1` 和 `set2` 为例，执行 `SDIFF set1 set2` 会返回 `{"apple", "cherry"}`。
- **`SDIFFSTORE destination key [key ...]`**：将第一个集合与其他集合的差集元素存储到 `destination` 集合中，同时返回差集元素的数量。如 `SDIFFSTORE result set1 set2`，会把 `set1` 和 `set2` 的差集元素存储到 `result` 集合中。

### 随机操作

#### 随机获取元素

  

- **`SRANDMEMBER key [count]`**：从集合中随机返回一个或多个成员。当 `count` 为正数时，返回不重复的成员；当 `count` 为负数时，返回可能重复的成员。例如 `SRANDMEMBER myset 2`，会从 `myset` 集合中随机返回 2 个不重复的元素。

#### 随机移除元素

  

- **`SPOP key [count]`**：从集合中随机移除并返回一个或多个成员。例如 `SPOP myset 1`，会从 `myset` 集合中随机移除并返回 1 个元素。

# 案例
####                  QQ你可能认识的人
	假如两个不认识的人，他们朋友列表如果有交集，说明他们可能认识
####                   微信抽奖小程序
	假如有3000人参与一个抽奖，将这些人的id放入集合中，用spop命令来随机弹出中将的人
####                 微信朋友圈点赞查看同赞的朋友
	微信朋友圈可以查看好友的点赞，只需要发布朋友圈的点赞的人与点赞朋友圈的人的好友列表进行交集即可