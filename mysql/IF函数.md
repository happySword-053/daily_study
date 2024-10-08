函数原型
```mysql
	IF(条件，条件为真时执行的语句，条件为假时执行的语句)
```
例子
```mysql
SELECT 
	IF( 
		(SELECT COUNT(DISTINCT Salary) FROM Employee) > 1, # 检查是否有超过一个不同的薪水 
		(SELECT MAX(Salary) FROM Employee WHERE Salary < (SELECT MAX(Salary) FROM Employee)), -- 如果有，则返回第二高的薪水 
		NULL -- 如果只有一个不同的薪水，则返回 NULL 
	) AS SecondHighestSalary;
```