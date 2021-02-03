# mysql 解析



## 填坑

### 计算问题

| null + 任何数值都为 null |
| :----------------------: |
|                          |
|                          |
|                          |
|                          |





## 数据类型解析

MySQL保存boolean值时用1代表TRUE，0代表FALSE。boolean在MySQL里的类型为tinyint(1)



## sql函数解析

### if()函数

[参考资料]: https://www.cnblogs.com/zjdxr-up/p/8383609.html

```mysql
# expression是一个 Boolean 的表达式,当值为 true 是返回a1,当值为 false 时返回a2
IF(expression, a1, a2)  
```



### EXISTS()函数

[参考资料]: https://www.cnblogs.com/zhuyeshen/p/11984044.html

```mysql
# EXISTS() 与 in() 的区别
# exists() 的返回结果是 Boolean 类型的

create table a(a_id int, a_name varchar(20));
create table b(b_id int, b_name varchar(20));

#要求是查询 a 表中与 b 表中名称相同的数据
# in()函数的使用方式
select * from a where a_name in (select b_name from b)

# exists()函数的使用方式
select * from a where exists( select b_id from b where b.b_name = a.a_name )
```



### group by 分组统计函数

[参考资料]: https://blog.csdn.net/yaruli/article/details/79239859



### CURRENT_DATE  日期函数

```sql
select current_date;
-- 实例  2020-08-11
```



### CONCAT 拼接

```sql
-- 拼接函数 将所有的字段拼接
CONCAT('210181', '%')
```



### COALESCE 选值函数

[参考资料]:https://blog.csdn.net/yilulvxing/article/details/86595725

```sql
-- 当字段值不为空返回字段原有的值  当字段值为空则返回指定值 -
CONCAT(COALESCE(b.nhxm, '-')

-- 依次处理 当success_cnt不为空则返回success_cnt, 当success_cnt为空,period不为空则返回period, 当success_cnt和period都为空则返回1
coalesce(success_cnt,period,1)
```



### chr 特定函数

[参考资料]:https://blog.csdn.net/ssh4412/article/details/76732735



### left right 截取函数

[参考资料]:https://www.cnblogs.com/muzhuang/p/11763509.html

```sql
SELECT LEFT('abcedf',3) as leftResult; -- 返回从左侧数前 3 个字符，第二个参数不接收负数，会报错

SELECT RIGHT('abcedf',3) as rightResult; -- 返回从右侧数前 3 个字符
```



### length() 长度获取函数

```sql
select se.name, length(se.name) from so_enterprise se limit 1; -- 统计改字段值得长度
```

| name | length |
| :--: | :----: |
| test |   4    |



### from_unixtime()时间戳格式化

```sql
select from_unixtime(create_time/1000) FROM iot_device_gps where id = 9850
```



### 自定义函数

[参考资料]:https://blog.csdn.net/qq_33696345/article/details/82288677

<font color=DarkOrange>函数示例 </font>

```sql
create function 函数名 (参数)
returns 返回值数据类型
as 
begin
sql语句(必须有return变量或值)
end

```





























