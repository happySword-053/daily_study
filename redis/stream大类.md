redis版本的MQ（消息队列）
底层就像一个消费者模型
![[Pasted image 20241001191351.png]]
![[Pasted image 20241001191719.png]]

# 队列相关指令
- xadd  添加消息到队尾 消息ID必须比上一个ID大
![[Pasted image 20241001192617.png]]    
    *    代表系统自动生成ID \ 时间戳
- xrange 获取消息列表
- ![[Pasted image 20241004154817.png]]
![[Pasted image 20241004154731.png]]
- xdel 删除相关信息
![[Pasted image 20241004155010.png]] 

- xlne key 获取key中所有组员的数量
- xtrim 用于对stream的长度进行截取
- ![[Pasted image 20241004155853.png]]
     - xtrim key maxlen num 从后面截取num个组员
     - xtrim key minid id 截断比id还小的组员
- xread 用于获取消息(阻塞\非阻塞)，只返回大于指定ID的消息 
![[Pasted image 20241004160501.png]]
![[Pasted image 20241004160746.png]]
# 四个特殊符号
![[Pasted image 20241001191812.png]]



# 消费者相关指令
- xgroup create [生产者key] key 0\$用于创建消费者组key去消费生产者key的消息
 ![[Pasted image 20241004161834.png]]
![[Pasted image 20241004164441.png]]



- XREADGROUP GROUP my-group alice BLOCK 0 STREAMS my-stream > 它允许你以消费者组的形式来消费消息。
![[Pasted image 20241004165807.png]]

- XPENDING stream-key group-name [[start end count]] [consumer]
命令用于查看消费者组中尚未被确认（即未完成处理）的消息。



- XACK stream-key group-name id...命令用于确认消费者组中已经成功处理的消息。当一个消息被成功处理后，你需要使用 `XACK` 命令来告诉 Redis 这个消息已经被正确处理，这样 Redis 就可以从待处理的消息列表中移除该消息，并且不会再将其分配给其他消费者。
比如：XACK mystream mygroup 1577358644292-0






# stream流程
![[Pasted image 20241004165655.png]]