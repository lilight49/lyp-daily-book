# Mybatis使用以及解析

## 框架解析



## #和$区别

[参考文档]：https://www.cnblogs.com/kingsonfu/p/10399987.html

| $    | 代表直接传入不会修改和转义字符,在一定成都上无法防止sql注入   |
| ---- | ------------------------------------------------------------ |
| #    | 会给值加双引号"",很大程度上可以防止sql注入,sql可以预编译可以再内存中保存sql语法 |



## xml文件属性解析

[参考文档]： https://blog.csdn.net/taiyangb/article/details/94122961





## foreach用法解析

[参考文档]：https://www.cnblogs.com/fnlingnzb-learner/p/10566452.html

### dao层

``` java
   int updateBath(@Param("chapterStepInfos") List<ChapterStepInfo> chapterStepInfos);
```

### mapper.xml

``` xml
    <update id="updateBath" parameterType="com.project.web.course.model.app.ChapterStepInfo">
        <foreach collection="chapterStepInfos" item="item" index="index" separator=";">
            update chapter_step_info
            <set>
                <if test="item.chapterid != null">
                    chapterId = #{item.chapterid},
                </if>
                <if test="item.stepname != null">
                    stepName = #{item.stepname},
                </if>
                <if test="item.guidelanguage != null">
                    guideLanguage = #{item.guidelanguage},
                </if>
                <if test="item.sort != null">
                    sort = #{item.sort},
                </if>
                <if test="item.status != null">
                    status = #{item.status},
                </if>
                <if test="item.createtime != null">
                    createTime = #{item.createtime},
                </if>
            </set>
            where stepId = #{item.stepid}
        </foreach>
    </update>
```

foreach元素的属性主要有item，index，collection，open，separator，close。

- **item：**集合中元素迭代时的别名，该参数为必选。
- **index**：在list和数组中,index是元素的序号，在map中，index是元素的key，该参数可选
- **open**：foreach代码的开始符号，一般是(和close=")"合用。常用在in(),values()时。该参数可选
- **separator**：元素之间的分隔符，例如在in()的时候，separator=","会自动在元素中间用“,“隔开，避免手动输入逗号导致sql错误，如in(1,2,)这样。该参数可选。
- **close:** foreach代码的关闭符号，一般是)和open="("合用。常用在in(),values()时。该参数可选。
- **collection:** 要做foreach的对象，作为入参时，List对象默认用"list"代替作为键，数组对象有"array"代替作为键，Map对象没有默认的键。当然在作为入参时可以使用@Param("keyName")来设置键，设置keyName后，list,array将会失效。 除了入参这种情况外，还有一种作为参数对象的某个字段的时候。举个例子：如果User有属性List ids。入参是User对象，那么这个collection = "ids".如果User有属性Ids ids;其中Ids是个对象，Ids有个属性List id;入参是User对象，那么collection = "ids.id"        [**collection为map时的参考文档**]： https://blog.csdn.net/weter_drop/article/details/85253244





## 模糊查询和大于小于号的使用

```xml
AND qhdm like CONCAT(#{qhdm},'%')

<![CDATA[ AND  farmland_area < #{farmlandAreaMax}]]>
```



