# 代码记录



##  获取系统名称

```java
private String getZipFilePath(String dicPath) {
    String os = System.getProperties().getProperty("os.name");
    if(os != null && os.toLowerCase().indexOf("linux") > -1){//Linux
        return dicPath +"/";
    }else{//windows
        return dicPath +"\\";
    }
}
```



## 文件夹操作

```java
File file = new File(dicPath);
if(!file.exists()){
    file.mkdirs();
}
```



## ==和equals

```java
//==:判断两个对象的内存地址是不是一样的,即判断两个对象是不是同一个对象,(基本类型比较的是值,引用类型比较的是内存地址)
//equals:判断两个对象是否相等,
//分为两种情况1:没有重写equals方法则实际比较的是==  2:重写了equals方法的这按照规则比较内容信息

```



## 获取内存地址信息

```java
//object
System.identityHashCode(Object)
```

## 重载和重写的

[参考资料]:https://www.runoob.com/java/java-override-overload.html

重写是写从上父类获取的方法

| 区别     | 重载(overload) | 重写(override)                            |
| -------- | -------------- | ----------------------------------------- |
| 参数列表 | 必须修改       | 不可修改                                  |
| 返回类型 | 可以修改       | 不可修改                                  |
| 异常     | 可以修改       | 可以删除和减少,不能抛出新的或者更广的异常 |
| 访问     | 可以修改       | 一定不能做严格的限制(可以降低限制)        |







# 注解

## springBoot 实体类的值为null或者数组为空时,字段不返回的解决方案

```java
import com.fasterxml.jackson.annotation.JsonInclude;

@JsonInclude(JsonInclude.Include.NON_NULL)

```























# 基本类型操作解析

[正则表达式参考资料]: https://blog.csdn.net/weixin_43860260/article/details/91417485

## 正则表达式规则

| 表达式符号. | 表达式意义         |
| ----------- | ------------------ |
| 英文句点 .  | 匹配任意一个字符串 |
|             | 0次或者多次        |
| ?           | 0次或多次          |





## String批量处理字符串

``` java
//String字符串批量修改替换
		String regEx = " style=\"(.*?)\"";// style="(.)"
        Pattern p = Pattern.compile(regEx);//将给定的正则表达式编译为模式。
        Matcher m = p.matcher(" style=\"aaaa\"first" + " style=\"aaaabbbbbbbbbbbb\" second");

        String c = null;
        if(m.find()){
            c = m.replaceAll("");
        }

        System.out.println(c);

//运行结果
first second
```



## String.format()

[参考资料]:https://segmentfault.com/a/1190000019350486

```java
//format 指针对不足长度的进行补位操作
String b =  String.format("%,6.1f", 42.000);
System.out.println(b);

int one = 123456789;
double two = 123456.789;
String s = String.format("one: %,d two: %,.2f", one, two);
System.out.println(s);
System.out.println(String.format("%,.5f", two));//小数点不足5位自动补零,超过的位数已四色五入的方式进行截取

String raw = "hello";//字符
System.out.println(String.format("%1$-7s",raw));//长度不足7位,从后面以空格补位,

int num = 1;//数字
String numString = String.format("%1$-6d", num).replaceAll(" ","0");//长度不足6位,从后面以空格补位
System.out.println(numString);
```





## for循环的用法

```java
List<Integer> lista = new ArrayList<>();

//当list为空时 并不会进入for循环
for (Integer a: lista) {
    System.out.println(a);
    System.out.println(lista.size());
}
```



## ASCII字典顺序

```java
List<String> list = new ArrayList<String>();
        list.add("b");
        list.add("d");
        list.add("a");
        list.add("c");
        list.add("A");
        list.add("0");

        Collections.sort(list);//ASCII字典顺序排列  0-9A-Za-z

        for (String s : list) {
            System.out.println(s);
        }
```

