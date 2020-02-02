# swagger2-springboot集成

### 集成配置
- 引入两个依赖

```xml
<!-- swagger2 version 2.9.2 -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>${swagger.version}</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>${swagger.version}</version>
</dependency>
```

- 增加swagger配置文件 

```java
import io.swagger.annotations.ApiOperation;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

/**
 * swagger2 config
 *
 * @version 1.0
 **/
@Configuration
@EnableSwagger2
public class SwaggerConfig {

    @Bean
    public Docket docket() {
        return new Docket(DocumentationType.SWAGGER_2)
                .groupName("demo")
                .apiInfo(apiInfo())
                .select()
                // 设置basePackage会将包下的所有类的所有方法作为api
                .apis(RequestHandlerSelectors.basePackage("com.lmxdawn.him.api.controller"))
                // 只有标记@ApiOperation才会暴露出给swagger
                .apis(RequestHandlerSelectors.withMethodAnnotation(ApiOperation.class))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("API文档")
                .description("restful风格接口")
                .version("1.0")
                .contact(new Contact("ChuanFu", "", "525491994@qq.com"))
                .build();
    }
}

```

### 接口过滤
- 使用@ApiIgnore注解
- 在Docket上增加过滤
	1. apis():指定包名,
	2. paths(): 筛选api的url进行过滤
	```java
	.apis(RequestHandlerSelectors.basePackage("cn.itweknow.sbswagger.controller"))
.paths(Predicates.or(PathSelectors.ant("/user/add"),
        PathSelectors.ant("/user/find/*")))
  ```


### 相关注解
- @Api(value="",tags={},description=""): 对控制器的描述
- @ApiOperation(value="",notes="") 对接口的描述
- @ApiIgnore() swagger文档忽略该接口
- @ApiImplicitParams() 描述接口的非对象参数集
- @ApiImplicitParam() 描述接口的非对象参数集,属性:
	* paramType  查询参数类型
	* dataType 参数数据类型
	* name  参数名称
	* value 参数意义描述
	* required 是否必须,true:必须 false:非必须
- @ApiModel() 设置接口相关实体描述
- @ApiModelProperty() 实体属性相关描述
