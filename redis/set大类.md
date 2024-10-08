和list很详细，但是无重复

- sadd key member 向key中添加元素
- smembers key 遍历所有元素
- sismember key member
- srem key member 从key中删除member
- scard key 获取集合中元素的个数
- srandmember key [数字] 从集合中随机展现n个元素
- spop key [数字] 从集合中随机弹出n个袁术
- smove key1 key2 [key1中存在的某个值] 将key1里已经存在的某个值赋给key2




# 集合运算
- 集合差集运算A-B    **sdiff key1 key2**     查看属于key1但不属于key2 的元素
- A^B集合并集运算 sunion key1 key2
- A&B集合交集运算 sinter key1 key2
- sintercard member_key key1 key2.... 返回所有集合交集的集合中元素的个数


# 案例
####                  QQ你可能认识的人
	假如两个不认识的人，他们朋友列表如果有交集，说明他们可能认识
####                   微信抽奖小程序
	假如有3000人参与一个抽奖，将这些人的id放入集合中，用spop命令来随机弹出中将的人
####                 微信朋友圈点赞查看同赞的朋友
	微信朋友圈可以查看好友的点赞，只需要发布朋友圈的点赞的人与点赞朋友圈的人的好友列表进行交集即可