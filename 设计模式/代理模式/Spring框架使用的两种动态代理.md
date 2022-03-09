## JDK动态代理

**前记：**代理对象持有被代理对象的引用，多为以借口类型声明的引用 私以为原因是代理类只关心被代理类方法的调用并在调用前后进行扩充 而被代理类的属性在此处实际是无意义的 可有可无的 因此奥卡姆剃刀

### 主要对象：Proxy类
```Java 
使用
Proxy.newProxyInstance(
	ClassLoader loader, 
	Class<?>[] interfaces, 
	InvocationHandler h)
方法生成代理对象

ClassLoader ：加载代理对象的类加载器
Class<?>[]：被代理类实现的接口
InvocationHandler：实现InvocationHandler对象
```


InvocationHandler：通过实现此借口，可实现自定义逻辑处理
```Java 
主要方法为public Object invoke(Object proxy, Method method, Object[] args)
Object 动态生成的代理类
Method 对应代理对象调用的方法
Object[] 当前method方法的参数
```
**最后方法的调用被转发到了实现了InvocationHandler的被代理类的invoke方法**
#### 具体实现
```Java
interface interA {
	methodA();
}
class ImplA implements interA {
	@Override
	methodA(){	}
}
class invokeC implements InvocationHandler {
	private final Object target;
	@Override
	Object invoke(Object proxy, Method method, Object[] args){
		//方法前执行
		Object result = method.invoke(target, args);
		//方法后执行
		return result;
	}
}
class FactoryF {
	public static Object getProxy(Object obj){
		return Proxy.getNewInstance(
			obj.getClass().getClassLoader(),
			obj.getClass().getInterfaces(),
			new InvokeC(obj)
		);
	}
}
main(){
	interA proxyA = FactoryF.getProxy(new ImplA);
	proxyA.method();
}

```



## CGLib动态代理
CGLIB (opens new window)(Code Generation Library)是一个基于ASM (opens new window)的字节码生成库，它允许我们在运行时对字节码进行修改和动态生成。
CGLIB 通过继承方式实现代理。
Spring 中的 AOP 模块中：如果目标对象实现了接口，则默认采用 JDK 动态代理，否则采用 CGLIB 动态代理

### 主要对象
`Enhancer` 类
使用Enhancer类动态获取代理类 代理类调用方法时 实际调用的是`MethodInterceptor`中的`intercept`方法

`MethodInterceptor` 接口
```Java
public interface MethodInterceptor extends Callback{ 
	// 拦截被代理类中的方法 
	public Object intercept(
		Object obj, 
		java.lang.reflect.Method method, 
		Object[] args, 
		MethodProxy proxy) throws Throwable; 
}

Object：被代理对象（被增强的对象）
Method：被拦截的方法（被增强的方法）
args：方法入参
proxy：用于调用原始方法
```
### 具体实现
```Java 
public class ServiceA {
	public void doSth(){
		return;
	}
}

public class CustomsizeInterceptor implements MethodInterceptor{
	@Override 
	public Object intercept(
		Object o, 
		Method method, 
		Object[] args, 
		MethodProxy methodProxy
	) throws Throwable { 
		//调用方法之前，我们可以添加自己的操作 
		Object object = methodProxy.invokeSuper(o, args); 
		//调用方法之后，我们同样可以添加自己的操作 
		return object; 
	} 
}

public class CgLibProxyFactory{
	public static Object getProxy(Class<?> clazz){
		Enhancer enhancer = new Enhancer();
		enhancer.setClassLoader(clazz.getClassLoader());
		enhancer.setSuperClass(clazz);
		enhancer.setCallback(new CustomsizeInterceptor());
		return enhancer.create();

	}
}

main(){
	ServiceA serviceA = (ServiceA) CglibProxyFactory.getProxy(ServiceA.class); 
	serviceA.doSth();
}
```