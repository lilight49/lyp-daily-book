# BigDecimal 解析



## 保留两位小数

```java
BigDecimal.valueOf(15.6987).setScale(2,BigDecimal.ROUND_HALF_UP).doubleValue()
```

