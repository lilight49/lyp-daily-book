# swagger解析



## mavenjar包

``` xml
 <!-- Swagger配置 -->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.9.2</version>
            <exclusions>
                <exclusion>
                    <groupId>io.swagger</groupId>
                    <artifactId>swagger-annotations</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>io.swagger</groupId>
                    <artifactId>swagger-models</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.9.2</version>
        </dependency>
        <dependency>
            <groupId>io.swagger</groupId>
            <artifactId>swagger-annotations</artifactId>
            <version>1.5.21</version>
        </dependency>
        <dependency>
            <groupId>io.swagger</groupId>
            <artifactId>swagger-models</artifactId>
            <version>1.5.21</version>
        </dependency>
		
```



## 创建配置类SwaggerConfig配置类	

  ``` java
@EnableSwagger2
@Configuration
public class Swagger2 {
@Bean
public Docket createRestApi() {
    //添加head参数(每个接口必传的参数)
    ParameterBuilder parameterBuilder = new ParameterBuilder();
    List<Parameter> parameters = new ArrayList<>();
    parameterBuilder.name("apiToken").description("API令牌").modelRef(new ModelRef("string")).parameterType("header").required(false).build();
    parameters.add(parameterBuilder.build());
    parameterBuilder.name("apiId").description("API标识").modelRef(new ModelRef("string")).parameterType("header").required(false).build();
    parameters.add(parameterBuilder.build());

    return new Docket(DocumentationType.SWAGGER_2)
            .apiInfo(apiInfo())//调用apiInfo方法,创建一个ApiInfo实例,里面是展示在文档页面信息内容
            .select()
            .apis(RequestHandlerSelectors.basePackage("com.project.web.system.controller"))//提供接口所在的package
            .paths(PathSelectors.any())
            .build()
            .globalOperationParameters(parameters);
}

/**
 * 构建 api文档的详细信息函数
 *
 * @return
 */
private ApiInfo apiInfo() {
    return new ApiInfoBuilder()
            .title("系统服务API")
            .contact(new Contact("北京坚小持科技", "http://www.jianxc.com", "system@jianxc.com"))
            .version("1.0.0")
            .description("一、接口调用规则如下" +
                    "\r\n 1: 接口域名：https://aip.jianxc.com" +
                    "\r\n 2: /system/auth/* header中参数为必填" +
                    "\r\n 3: /system/anon/* header中参数为非必填" +
                    "\r\n 4: /system/common/* header中参数为非必填" +
                    "\r\n 二、参数传递类型如下" +
                    "\r\n 1:【header】参数在request headers 里边提交" +
                    "\r\n 2:【form】以表单的形式提交仅POST方式" +
                    "\r\n 3:【query】get方式,参数完成自动映射赋值" +
                    "\r\n 4:【path】URL地址路径作为参数" +
                    "\r\n 5:【body】支持POST方式JSON提交参数")
            .build();
}
  ```



## 接口处的注解配置信息

``` java
@Api(tags = "学员管理API")//@api：用于类表示是swagger的资源，tag：分组说明标签
@RestController("systemUserInfoController")
@RequestMapping("/system/auth/userInfo")
public class UserInfoController extends BaseController {
// 日志工具类
public static final Logger log = LoggerFactory.getLogger(UserInfoController.class);

@Resource(name = "userInfoServiceImpl")
private UserInfoService userInfoService;

/**
 * 查询系统用户
 *
 * @param userInfo 系统用户
 * @return
 */
@ApiOperation(value = "查询系统用户列表【含分页】")
//@ApiOperation用于标识方法，表示是一个HTTP请求的的操作，value：方法的描述，notes：提示信息
@ApiImplicitParams({//@ApiImplicitParams：用于controller的方法，包含多个@ApiImplicitParam  @ApiImplicitParam：表示单独的请求参数 name：参数的名称，value:参数的说明 dataType:数据类型 paramType:参数类型
        @ApiImplicitParam(paramType = "query", name = "pageNum", value = "页码", dataType = "Integer", required = true),
        @ApiImplicitParam(paramType = "query", name = "pageSize", value = "每页条数", dataType = "Integer", required = true),
        @ApiImplicitParam(paramType = "query", name = "userInfo", value = "学员用户", dataType = "UserInfo", required = true)
})
@GetMapping(value = "/getPageList")
public Result<PageInfo<UserInfo>> getPageList(UserInfo userInfo, Integer pageNum, Integer pageSize) {
    PageInfo pageInfo = PageInfoUtil.initPageInfo(pageNum, pageSize);
    PageInfo<UserInfo> mbrH5OrderPageInfo = userInfoService.selectList(userInfo, pageInfo);
    return getResult(mbrH5OrderPageInfo);
}
```



*********************************************************************************



``` java
@ApiModel("会员h5订单表")
//@ApiModel实体类的标识，当实体类作为参数时，value：对象名，description：描述信息
public class MbrH5Order extends BaseEntity {
/**
 * 支付人名称【仅针对H5支付】
 */
@ApiModelProperty("支付人名称【仅针对H5支付】")
//@ApiModelProperty实体类的字段标识，表示对model属性的说明，value:字段说明，name:重写属性名称dataType：重写数据类型 required:是否必填，example:举例说明 hidden:是否隐藏
private String payUserName;
}
```

*********************************************************************************


## swagger访问页面

[wsagger的页面访问地址]( http://localhost:8080/swagger-ui.html )

[ swagger的页面访问地址]: http://localhost:8080/swagger-ui.html













