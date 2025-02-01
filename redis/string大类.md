- ex seconds 以秒为单位设置过期时间
- px xxx 以毫秒为单位设置过期时间 

1 SET key value 此命令设置指定键的值。
2 GET key 获取指定键的值。
3 GETRANGE key start end 获取存储在键上的字符串的子字符串。
4 GETSET key value 设置键的字符串值并返回其旧值。
5 GETBIT key offset 返回在键处存储的字符串值中偏移处的位值。
6 MGET key1 [key2…] 获取所有给定键的值
7 SETBIT key offset value 存储在键上的字符串值中设置或清除偏移处的位
8 SETEX key seconds value 使用键和到期时间来设置值
9 SETNX key value 设置键的值，仅当键不存在时
10 SETRANGE key offset value 在指定偏移处开始的键处覆盖字符串的一部分
11 STRLEN key 获取存储在键中的值的长度
12 MSET key value [key value …] 为多个键分别设置它们的值
13 MSETNX key value [key value …] 为多个键分别设置它们的值，仅当键不存在时
14 PSETEX key milliseconds value 设置键的值和到期时间(以毫秒为单位)
15 INCR key 将键的整数值增加1
16 INCRBY key increment 将键的整数值按给定的数值增加
17 INCRBYFLOAT key increment 将键的浮点值按给定的数值增加
18 DECR key 将键的整数值减1
19 DECRBY key decrement 按给定数值减少键的整数值
20 APPEND key value 将指定值附加到键

# 分布式锁
- setnx key value
