# 使用场景
用于类、方法、接口（不推荐，声明在接口并配置使用CGlib代理会导致注解失效
# 参数
## propogation
- Required 存在加入 不存在新建
- Supports 有则加入 没有则非事务运行
- Mandatory(强制的) 有则加入 没有则抛异常
- Requires_New 新建一个 如果原来有则暂停
- Not_Supported 非事务运行 原来有则暂停
- Never 非事务运行 原来有则抛异常
## isolusion 隔离级别
|取值|含义|
|--|--|
|DEFAULT|数据库默认|
|READ_UNCOMMITTED|读未提交|
|READ_COMMITTED|读已提交 阻止脏读|
|ISOLATION_READ_COMMITTED|可重复读 阻止脏读、不可重复读|
|ISOLATION_REPEATABLE_READ|序列化执行 阻止幻读|

## timeout
超时自动回滚
## readonly
指定只读事务
## rollbackFor
指定可触发回滚的异常类型
## noRollbackFor
指定不触发回滚的异常类型

# 失效场景
1. 用在非public修饰的方法上 `且不会报错，事务无效`
TransactionInterceptor中，AbstractFallbackTransactionAttributeSource的computeTransactionAttribute方法会被间接调用，获取Transaction注解的信息 *此处*会检查是否为public方法 若不是则不会获取
2. propagation设置错误
supports | not_supported | never
3. rollbackFor设置错误
抛出的异常不在配置中，则不会回滚
4. 同类中的方法调用
```Java
class Test {
	methodA(){
		methodB(); //此时methodB的事务不生效
	}
	@Transactional
	methodB(){};
}
```
`因为SpringAOP只有在对象的事务方法被类外的代码调用，才会将其由Spring生成的对象管理`
5. 异常被catch
```Java 
ServiceA{
	try {
		serviceB.execute();
	} catch (Exception e) {
		
	}
}
```
6. 数据库不支持事务
例如使用了MyISAM引擎