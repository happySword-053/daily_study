和set有一定练习，但是zset是有序的
在set的基础上，每个value值钱加一个score分数值。之前set是 k1 v1 v2 v3,
现在zset是 k1 score1 v1 score2 v2
Redis 的有序集合（Sorted Set，简称 ZSet）是一种独特的数据结构，它结合了集合（Set）和哈希表（Hash）的特性。和集合一样，有序集合中的成员是唯一的，但不同的是，每个成员都会关联一个双精度浮点数类型的分数（score）。Redis 会根据分数对成员进行从小到大的排序。以下是有序集合的常见操作：

### 元素添加与更新

  

- **`ZADD key [NX|XX] [CH] [INCR] score member [score member ...]`**
    - 此命令用于向有序集合中添加一个或多个成员，或者更新已存在成员的分数。
    - `NX`：仅在成员不存在时才添加。
    - `XX`：仅更新已存在的成员，不添加新成员。
    - `CH`：返回此次操作后分数被修改的成员数量。
    - `INCR`：将分数进行增量操作，而不是设置新分数。
    - 示例：`ZADD myzset 1 "member1" 2 "member2"` 会将 `member1` 和 `member2` 及其对应的分数添加到 `myzset` 有序集合中。

### 元素删除

  

- **`ZREM key member [member ...]`**
    - 从有序集合中移除一个或多个成员，不存在的成员会被忽略。
    - 示例：`ZREM myzset "member1"` 会将 `member1` 从 `myzset` 中移除。
- **`ZREMRANGEBYRANK key start stop`**
    - 移除有序集合中指定排名范围内的所有成员，排名从 0 开始，`start` 和 `stop` 可以是负数，表示从末尾开始计数。
    - 示例：`ZREMRANGEBYRANK myzset 0 2` 会移除排名前 3 的成员。
- **`ZREMRANGEBYSCORE key min max`**
    - 移除有序集合中分数在指定范围内的所有成员，`min` 和 `max` 可以使用 `(` 表示开区间，`-inf` 和 `+inf` 分别表示负无穷和正无穷。
    - 示例：`ZREMRANGEBYSCORE myzset 1 3` 会移除分数在 1 到 3 之间（包含 1 和 3）的成员。

### 元素查询

  

- **`ZSCORE key member`**
    - 返回有序集合中指定成员的分数。
    - 示例：`ZSCORE myzset "member1"` 会返回 `member1` 的分数。
- **`ZRANGE key start stop [WITHSCORES]`**
    - 返回有序集合中指定排名范围内的成员，按分数从小到大排序。若指定 `WITHSCORES`，则同时返回成员的分数。
    - 示例：`ZRANGE myzset 0 2 WITHSCORES` 会返回排名前 3 的成员及其分数。
- **`ZREVRANGE key start stop [WITHSCORES]`**
    - 与 `ZRANGE` 类似，但按分数从大到小排序。
    - 示例：`ZREVRANGE myzset 0 2 WITHSCORES` 会返回分数最高的前 3 个成员及其分数。
- **`ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT offset count]`**
    - 返回有序集合中分数在指定范围内的成员，按分数从小到大排序。`LIMIT` 用于分页，`offset` 表示偏移量，`count` 表示返回的数量。
    - 示例：`ZRANGEBYSCORE myzset 1 3 WITHSCORES LIMIT 0 2` 会返回分数在 1 到 3 之间的前 2 个成员及其分数。
- **`ZREVRANGEBYSCORE key max min [WITHSCORES] [LIMIT offset count]`**
    - 与 `ZRANGEBYSCORE` 类似，但按分数从大到小排序。
    - 示例：`ZREVRANGEBYSCORE myzset 3 1 WITHSCORES LIMIT 0 2` 会返回分数在 1 到 3 之间（按从大到小排序）的前 2 个成员及其分数。

### 集合统计

  

- **`ZCARD key`**
    - 返回有序集合中的成员数量。
    - 示例：`ZCARD myzset` 会返回 `myzset` 中的成员数量。
- **`ZCOUNT key min max`**
    - 返回有序集合中分数在指定范围内的成员数量。
    - 示例：`ZCOUNT myzset 1 3` 会返回分数在 1 到 3 之间的成员数量。
- **`ZRANK key member`**
    - 返回有序集合中指定成员的排名（按分数从小到大），排名从 0 开始。
    - 示例：`ZRANK myzset "member1"` 会返回 `member1` 的排名。
- **`ZREVRANK key member`**
    - 返回有序集合中指定成员的排名（按分数从大到小），排名从 0 开始。
    - 示例：`ZREVRANK myzset "member1"` 会返回 `member1` 在按分数从大到小排序时的排名。

### 集合运算

  

- **`ZUNIONSTORE destination numkeys key [key ...] [WEIGHTS weight [weight ...]] [AGGREGATE SUM|MIN|MAX]`**
    - 计算给定的一个或多个有序集合的并集，并将结果存储在 `destination` 中。
    - `numkeys` 表示参与运算的有序集合的数量。
    - `WEIGHTS` 用于为每个有序集合指定一个权重，在计算时会将成员的分数乘以该权重。
    - `AGGREGATE` 用于指定聚合方式，`SUM` 表示分数相加（默认），`MIN` 表示取最小分数，`MAX` 表示取最大分数。
    - 示例：`ZUNIONSTORE result 2 myzset1 myzset2 WEIGHTS 1 2 AGGREGATE SUM` 会计算 `myzset1` 和 `myzset2` 的并集，`myzset2` 中成员的分数会乘以 2，然后将结果存储在 `result` 中。
- **`ZINTERSTORE destination numkeys key [key ...] [WEIGHTS weight [weight ...]] [AGGREGATE SUM|MIN|MAX]`**
    - 计算给定的一个或多个有序集合的交集，并将结果存储在 `destination` 中。参数含义与 `ZUNIONSTORE` 类似。
    - 示例：`ZINTERSTORE result 2 myzset1 myzset2 WEIGHTS 1 2 AGGREGATE SUM` 会计算 `myzset1` 和 `myzset2` 的交集，`myzset2` 中成员的分数会乘以 2，然后将结果存储在 `result` 中。

# 实例·
每日热销商品
