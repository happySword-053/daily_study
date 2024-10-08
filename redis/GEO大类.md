地理空间
经度范围(-180,180],维度范围(-90,90]

geo的每一个key都是zset的子类
# 用法
- geoadd key 经度  纬度 名称  多个经度、纬度、位置名称添加到指定的key中
	   geoadd city 116.4039 39.9151 "天安门"
- geopos key member 从键里面返回所有给定位置元素的位置
		geopos city 天安门 
- geodis key k1 k2 m/km 返回两个给定位置之间的距离 （这个只能在同一个key下使用）
- geohash 返回坐标的geohash表示
- georadius key 经度 纬度 nKM [withdist(返回距离)]      [withcoord(将位置元素的经度和维度一起返回)]  [withhash(返回哈希值)] [count 数字 (限定返回多少)] 返回距离当前经纬度 nKM范围内的元素
- georadiusbymember
- geohash key member 返回一个或多个位置元素的geohash表示heohash算法生成32位编码值





# 案例
美团附近酒店
附件核酸检测点