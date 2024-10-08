
由0和1状态表现的二进制位的bit数组，但是底层还是string类型

bitmap支持的最大位是2^32

bitmap的下标从0开始
# 用法


- setbit key index num 设置下标index的值为num
- getbit key index 获取下标index的值
- strlen key 获取bitmap的字节长度，注意是**字节长度**，八位一字节，0-7为一字节，假如说下标7之后都是0，那么该bitmap只有一个字节
- bitcount key start end获取该bitmap中1的数量
- bitop operation key1 key2 对不同的bitmap进行位运算(AND, OR, NOT, XOR)(与，或，或非，异或)  将key1和ley2进行位运算
       





# 案例
钉钉打卡上班
全天登录统计