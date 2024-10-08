特点： 单key多value ，一个双端链表结构

用法：
- lpush/rpush/lrange  key begin end  lpush从头节点左边放，rpush从尾节点右边放，lrange：对于key，从 begin遍历到end ， 0 ~ -1 表示全遍历
- lpop/rpop key 从左边/右边弹出元素
- lindex key index 按照索引下标获取元素
- llen key 获取列表中元素个数
- lrem key 数值n 给定值v1 删除n个值为v1的元素
- ltrim key 开始index 结束index 截取指定范围的值后再赋值给key，相当于assign函数，专门截取某个范围内的值
- rpoplpush 源列表 目的列表  将源列表的尾节点放置到目标列表的头部
- lset key index value
- linsert key before/after 已有值 插入的新值  