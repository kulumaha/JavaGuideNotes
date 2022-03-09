## @SpringBootApplication
等价于 @Configuration + @ComponentScan + @EnableAutoConfiguration（启用SpringBoot自动配置机制）

## @Autowired
## @Component
## @Repository / @MybatisRepository（自定义）
标注Dao层
## @Service
## @Controller 返回视图（页面）
## RestController
等价于@Controller + @ResponseBody
## @Scope
取值：
- singleton 单例
- prototype 每次请求创建一个
- request 同上 但只在该request内生效
- session 同上 但只在该session中有效
## @Configuration
声明配置类 可由@Component代替
## Mapping类
### @GetMapping
请求资源
### @PostMapping
创建资源
### @PutMapping
整体更新资源
### @PatchMapping
更新资源的某一部分
### @DeleteMapping  
删除资源
## 参数
### @PathVariable
### @RequestParam
### @RequestBody
## 参数校验
### @NotEmpty
### @NotBlank
### @NotNull
### @AssetTrue 元素必须为True
### @Max
### @Min
### @Past 元素为过去的日期
### @Future 元素为将来的日期
### @Pattern
## 取值
读取Request的Body部分 系统使用HttpMessageConverter(可自定义) 进行Json到Java对象的转化
### @Value("${property}")
### @ConfigurationProperties(prefix = "beanPrefix")
读取配置信息 与配置对象Bean进行绑定


