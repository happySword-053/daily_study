统计某个网站的uv(unique visitor 独立访客，一般理解为客户端IP) 大规模防止作弊，需要去除重复统计，独立访客，比如ip同样就认为是同一用户

hyperloglog的优点是，在输入元素的数量或体积非常大的时候，计算基数所需的空间总是固定的，并且是很小的
在redis里面，每个hyperloglog键只需消耗12KB内存，就可以计算接近2^64个不同的基数。这和计算基数时，元素越多耗费内存就越多的集合形成鲜明对比
hyperloglog只会根据输入元素来计算基数，不会存储元素本身

基数是一种数据集，去重复后的真实个数，相当于集合的大小
# 用法
- pfadd key element [element....] 添加指定元素到hyperloglog中
-  pfcount key [key] 返回给定hyperloglog中的基数估算值
- pfmerge destkey sourcekey [sourcekey]将多个hyperloglog合并为一个hyperloglog








# 案例
天猫网站访问量统计
