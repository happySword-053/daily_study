和set有一定练习，但是zset是有序的
在set的基础上，每个value值钱加一个score分数值。之前set是 k1 v1 v2 v3,
现在zset是 k1 score1 v1 score2 v2

# 用法
- zadd key score member 
- zrange key start stop [withcores] 按照元素分数从小到大的顺序，返回索引start到stop之间的所有元素
- zrevrange key start stop [withcores] 按照元素分数从大到小的顺序，返回索引start到stop之间的所有元素
- zrangebyscore key min max [withscores] 获取指定分数范围的元素
- zscore key member 获取元素的分数
- zcard key 获取集合中元素的数量
- zrem key [某个score下对应的value值] 删除某个元素
- zincrby key increment member 让元素增加increment
- zcount key min max 获得指定分数范围内的元素个数
- zmpop  从键名列表中弹出
- zrank key value值 返回正序下标
- zrevrank key value值 返回逆序下标


# 实例·
每日热销商品
