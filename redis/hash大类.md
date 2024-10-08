map<string,map<object,object>>够形象


# 用法 
- hset str_key obj_str_key obj_value 相当于创建一个名为str_key的map，添加键值对
- hget str_key obj_str_key 获取名为str_key的map中obj_str_key的值
- hgetall str_key 获取所有的键值对
- hdel str_ket obj_str_key 删除某个键值对



- hlen str_key 获取某个map的长度

- hexist str_key obj_str_key 查看某个map中是否存在某个键值对
- hkeys str_key 查看某个map中所有的key
- hvals str_key 查看某个map中所有的value
- hincrby str_key obj_str_key map中某个key对应的value自增1
- hincrbyfloat str_key obj_str_key float_num map中某个key对应的value增加float



## 应用场景
购物车