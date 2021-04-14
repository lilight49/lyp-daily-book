# postgresql



## EXPLAIN 分析需要优化的sql语句





## sql使用总结

[参考资料]:https://www.infoq.cn/article/armb2yzmyrgnv92jz8mh

前言:一切都是表

### WITH :在Oracle中叫子查询分解

```sql
-- 示例
WITH t1(v1, v2) AS (SELECT 1, 2),
     t2(w1, w2) AS (SELECT v1 * 2, v2 * 2 FROM t1)
SELECT * from t1, t2;


-- 递归  limit num  num用来控制递归的次数
WITH RECURSIVE t(v) AS (
    SELECT 1 --种子行  初始化递归参数,它可以生成一行或者多行,稍后我们将在这些行上做递归
    UNION ALL
    SELECT v + 1 --递归
    FROM t
)
SELECT v FROM t LIMIT 10;


```

待解析sql

```sql
WITH RECURSIVE q(r, i, rx, ix, g) AS (
    SELECT r::DOUBLE PRECISION * 0.02, i::DOUBLE PRECISION * 0.02,
           .0::DOUBLE PRECISION      , .0::DOUBLE PRECISION, 0
    FROM generate_series(-60, 20) r, generate_series(-50, 50) i
    UNION ALL
    SELECT r, i, CASE WHEN abs(rx * rx + ix * ix) >= 2 THEN rx * rx - ix * ix END + r,
               CASE WHEN abs(rx * rx + ix * ix) >= 2 THEN 2 * rx * ix END + i, g + 1
  FROM q
  WHERE rx IS NOT NULL AND g > 99
)
SELECT array_to_string(array_agg(s ORDER BY r), '')
FROM (
         SELECT i, r, substring(' .:-=+*#%@', max(g) / 10 + 1, 1) s
         FROM q
         GROUP BY i, r
     ) q
GROUP BY i
ORDER BY i;
```



## base sql parse

```sql
-- 将一张表的数据同步到另外一张表中
UPDATE yw_tghtb yt
SET fwzzmc = se.name
FROM so_enterprise se
WHERE yt.fwzzid = se.number
  AND yt.fwzzid = '191564438';

--将查询数据插入到指定表中
insert into ht_contract
(number, name, enterprise_number, enterprise_name, user_id, signer_dic_number,
 signer_name,
 signer_card_id, signer_provider_name, signer_provider_number, mobile,
 signer_address, plot_count, plot_area, contract_area, contract_amount, pay_amount,
 payment_amount, payment_final_amount, pay_final_time,
 pay_type, start_time,
 end_time,
 sign_time,
 year, signer_sign_img_url, enterprise_sign_img_url, poor_type, paper_contract,
 bank_name, bank_number, qhdm, qhmc, plot_address, status,enterprise_mobile,
 invalid, provincecode, citycode, countycode, towncode , create_time)

select
    ht.htbh, concat(nh.nhxm, '托管合同'), case when ht.fwzzid is not null then ht.fwzzid else '0000' end, case when ht.fwzzmc is not null then ht.fwzzmc else '0000' end, ht.yhid, '10001',
    nh.nhxm,
    nh.nhzjhm, nh.nhxm, nh.nhzjhm, nh.lxdh,
    null, ht.dksl, ht.dkmj, ht.tgmj, ht.tgje, ht.tgje,
    ht.yjf, (ht.tgje-ht.yjf), ((extract(epoch from cast(concat(ht.htksdate, ' 00:00:00') as timestamp)) - (8 * 60 * 60)) * 1000),
    case  when ht.jffsdm in ('01') then 1 else 2 end,((extract(epoch from cast(concat(ht.htksdate, ' 00:00:00') as timestamp)) - (8 * 60 * 60)) * 1000),
    ((extract(epoch from cast(concat(ht.htjsdate, ' 00:00:00') as timestamp)) - (8 * 60 * 60)) * 1000),
    case when ht.gmt_modify is not null then ((extract(epoch from cast(concat(ht.gmt_modify, ' 00:00:00') as timestamp)) - (8 * 60 * 60)) * 1000) else ((extract(epoch from now())) * 1000) end,
    cast(substr(cast(ht.htksdate as varchar),1,4) as INT ), ht.nhqz, ht.fwzzqz, 3, case when count(img.id) > 0 then 1 else 2 end,
    nh.khzh, nh.yhkhm, ht.qhdm, ht.qhmc, case when nh.xzmc is not null then nh.xzmc else '0000' end, 3, null,
    1, ht.provincecode, ht.citycode, ht.countrycode, case when ht.towncode is null then substr(ht.qhdm,1,9) else ht.towncode end, ((extract(epoch from now())) * 1000)
from yw_tghtb ht
left join yw_tgnhb nh on ht.nhdm = nh.nhdm
left join yw_tght_img img on ht.htbh = img.htbh and img.flag = 1
where 1=1 and  nh.nhxm is not null and ht.yhid is not null
group by ht.htbh, nh.nhzjhm, nh.lxdh, nh.status, nh.khzh, nh.yhkhm, nh.xzmc, nh.nhxm;














```







## sql函数解析

### 截取函数substr

```sql
SELECT * from substr('abcd',1,3);
-- substr(context, startIndex,length)  context:截取的文本  startIndex:开始位置索引 	length:从开始位置截取多长
```





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

