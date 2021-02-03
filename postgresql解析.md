# postgresql



## sql解析

### 时间戳格式化函数

[参考资料]: https://blog.csdn.net/snn1410/article/details/7741283

```sql
to_char(to_timestamp((er.create_time+8*60*60*1000)/1000),'yyyy-MM-dd hh24:mi:ss') as update_time
-- 时区调整 er.create_time+8*60*60*1000
```



### ROW_NUMBER () OVER (ORDER BY qhmc desc) 函数进行数据编号

```sql
-- sql示例
SELECT ROW_NUMBER() OVER(order by qhdm asc) as "id", qhdm, qhmc FROM "sys_adminarea" limit 10
-- ROW_NUMBER() 生成序号函数
-- OVER(order by qhdm asc) 生成需要的依据,区划代码正序排列

-- sql 解析示例
id  qhdm			qhmc
1	1				中华人民共和国
2	11				北京市
3	1101			北京市
4	110101			东城区
5	110102			西城区
6	110102001		西长安街街道办事处
7	110102001001	西长安街街道001街坊
8	110102001002	西长安街街道002街坊
9	110102001003	西长安街街道003街坊
10	110102001006	西长安街街道006街坊

```



### case when then else end

```sql
CASE WHEN C.number IN ('8000', '1001','1010','1011') THEN service_real_area ELSE 0 END

-- 当c.number 包含 8000,1001,1010,1011 时,取service_real_area的值否则取0 结束
```



### extract(epoch from now())

```sql
select extract(epoch from now());
-- 获取从现在到1970-01-01 00:00:00 UTC 的秒数
```

### 浮动取值

```sql
round() --四舍五入
floor() --向下取整
ceiling() --向上取整
```



### 添加序号函数

[参考资料]:https://blog.csdn.net/wuyoudeyuer/article/details/91384971

```sql
(row_number() over (partition by scp.number order by scp.create_time))
-- partition by 后跟分组字段
-- order by 排序字段
```





## sql待学习



### sql函数学习

```sql
CREATE OR REPLACE FUNCTION "public"."st_asgeojson"(text)
  RETURNS "pg_catalog"."text" AS $BODY$ SELECT public._ST_AsGeoJson(1, $1::public.geometry,15,0);  $BODY$
  LANGUAGE sql IMMUTABLE STRICT
  COST 100
```

