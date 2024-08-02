# Java动态代理

## 反射

### Class

获取Class对象

```java
public class Main {
    public static void main(String[] args) throws ClassNotFoundException {
        Object obj = new Object();
        Class clazz1 = obj.getClass();
        Class clazz2 = Object.class;
        Class clazz3 = Class.forName("java.lang.Object");
    }
}
```

通过反射创建对象，可以避免在代码中通过new硬编码创建对象，

通过对象的Class实例可以获取对象的方法、属性、注解

使用getClass().isArray()判断对象是不是数组

### Method

mehtod的invoke方法，调用给定对象的该方法

### Constructor

反射创建对象，可以通过Class类的newInstance方法，也可以先根据参数类型获取构造方法Contructor对向，再创建实例



## JDK动态代理

jdk动态代理主要使用`java.lang.reflect`包下的`Proxy`类和`InvocationHandler`接口，代码示例：

1. 需要被代理的类及其实现的接口

   ```java
   public interface RoleService {
       String getRoleById(Long id);
   }
   
   public interface UserService {
       String selectNameById(Long id);
   
       void update(Long id, String name);
   }
   
   public class UserServiceImpl implements UserService, RoleService {
       @Override
       public String selectNameById(Long id) {
           if (id == null) {
               return "NULL";
           } else {
               return id.toString();
           }
       }
   
       @Override
       public void update(Long id, String name) {
           System.out.println("update user set name = " + name + " where id = " + id);
       }
   
       @Override
       public String getRoleById(Long id) {
           return "admin";
       }
   }
   ```

   

2. 代理处理器，实现`InvocationHandler`接口

   ```java
   public class LogHandler implements InvocationHandler {
       // 被代理的对象
       Object target;
   
       public LogHandler(Object target) {
           this.target = target;
       }
   
       @Override
       public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
           // 调用被代理对象的method方法前
           System.out.println("before invoke " + target.getClass().getName() + "." + method.getName());
           // 调用被代理对象的method方法
           Object result = method.invoke(target, args);
           // 调用被代理对象的method方法后
           System.out.println("after invoke " + target.getClass().getName() + "." + method.getName());
           // 返回被代理对象method执行结构
           return result;
       }
   }
   ```

   demo中invoke方法的第一个参数proxy并没有使用到，该参数可以用来：

   	- 通过反射获取代理对象的信息
   	- 可以返回代理对象进行连续调用

3. 使用Proxy类提供的方法获取代理对象，通过代理对象调用方法

   ```java
   public class Main {
   
       public static void main(String[] args) {
           client1();
           try {
               client2();
           } catch (Exception e) {
               throw new RuntimeException(e);
           }
       }
   
       public static void client1() {
           UserService userService = new UserServiceImpl();
           ClassLoader classLoader = userService.getClass().getClassLoader();
   
           // Class<?>[] interfaces = userService.getClass().getInterfaces();
   
           Class<?>[] interfaces = new Class[] {UserService.class, RoleService.class};
   		
           InvocationHandler logHandler = new LogHandler(userService);
   		// 使用Proxy的静态方法直接获取代理对象
           Object proxy = Proxy.newProxyInstance(classLoader, interfaces, logHandler);
   
           UserService userServiceProxy = (UserService) proxy;
   
           userServiceProxy.selectNameById(100L);
           userServiceProxy.update(100L, "joby");
   
           RoleService roleServiceProxy = (RoleService) proxy;
           String role = roleServiceProxy.getRoleById(100L);
           System.out.println("role: " + role);
       }
   
       public static void client2() throws NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException {
           UserService userService = new UserServiceImpl();
           ClassLoader classLoader = userService.getClass().getClassLoader();
   
           // Class<?>[] interfaces = userService.getClass().getInterfaces();
   
           Class<?>[] interfaces = new Class[] {UserService.class, RoleService.class};
   
           InvocationHandler logHandler = new LogHandler(userService);
   
           // 获取到代理类的Class对象
           Class proxyClass = Proxy.getProxyClass(classLoader, interfaces);
           // 反射获取代理类构造函数
           Constructor constructor = proxyClass.getConstructor(InvocationHandler.class);
   		// 反射创建
           Object proxy = constructor.newInstance(logHandler);
           UserService userServiceProxy = (UserService) proxy;
   
           userServiceProxy.selectNameById(100L);
           userServiceProxy.update(100L, "joby");
   
           RoleService roleServiceProxy = (RoleService) proxy;
           String role = roleServiceProxy.getRoleById(100L);
           System.out.println("role: " + role);
       }
   
   }
   ```

   

4. 生成的代理类字节码反编译得到class文件

   通过设置jvm参数将生成的代理类class文件输出到类路径中

   - Java8及以下版本使用：`-Dsun.misc.ProxyGenerator.saveGeneratedFiles=true`
   - Java9及以上版本使用：`jdk.proxy.ProxyGenerator.saveGeneratedFiles=true`

   ```java
   package com.sun.proxy;
   
   import java.lang.reflect.InvocationHandler;
   import java.lang.reflect.Method;
   import java.lang.reflect.Proxy;
   import java.lang.reflect.UndeclaredThrowableException;
   
   public final class $Proxy0 extends Proxy implements UserService, RoleService {
       private static Method m1;
       private static Method m2;
       private static Method m4;
       private static Method m3;
       private static Method m0;
       private static Method m5;
   
       public $Proxy0(InvocationHandler var1) throws  {
           super(var1);
       }
   
       public final boolean equals(Object var1) throws  {
           try {
               return (Boolean)super.h.invoke(this, m1, new Object[]{var1});
           } catch (RuntimeException | Error var3) {
               throw var3;
           } catch (Throwable var4) {
               throw new UndeclaredThrowableException(var4);
           }
       }
   
       public final String toString() throws  {
           try {
               return (String)super.h.invoke(this, m2, (Object[])null);
           } catch (RuntimeException | Error var2) {
               throw var2;
           } catch (Throwable var3) {
               throw new UndeclaredThrowableException(var3);
           }
       }
   
       public final String selectNameById(Long var1) throws  {
           try {
               return (String)super.h.invoke(this, m4, new Object[]{var1});
           } catch (RuntimeException | Error var3) {
               throw var3;
           } catch (Throwable var4) {
               throw new UndeclaredThrowableException(var4);
           }
       }
   
       public final void update(Long var1, String var2) throws  {
           try {
               super.h.invoke(this, m3, new Object[]{var1, var2});
           } catch (RuntimeException | Error var4) {
               throw var4;
           } catch (Throwable var5) {
               throw new UndeclaredThrowableException(var5);
           }
       }
   
       public final int hashCode() throws  {
           try {
               return (Integer)super.h.invoke(this, m0, (Object[])null);
           } catch (RuntimeException | Error var2) {
               throw var2;
           } catch (Throwable var3) {
               throw new UndeclaredThrowableException(var3);
           }
       }
   
       public final String getRoleById(Long var1) throws  {
           try {
               return (String)super.h.invoke(this, m5, new Object[]{var1});
           } catch (RuntimeException | Error var3) {
               throw var3;
           } catch (Throwable var4) {
               throw new UndeclaredThrowableException(var4);
           }
       }
   
       static {
           try {
               m1 = Class.forName("java.lang.Object").getMethod("equals", Class.forName("java.lang.Object"));
               m2 = Class.forName("java.lang.Object").getMethod("toString");
               m4 = Class.forName("UserService").getMethod("selectNameById", Class.forName("java.lang.Long"));
               m3 = Class.forName("UserService").getMethod("update", Class.forName("java.lang.Long"), Class.forName("java.lang.String"));
               m0 = Class.forName("java.lang.Object").getMethod("hashCode");
               m5 = Class.forName("RoleService").getMethod("getRoleById", Class.forName("java.lang.Long"));
           } catch (NoSuchMethodException var2) {
               throw new NoSuchMethodError(var2.getMessage());
           } catch (ClassNotFoundException var3) {
               throw new NoClassDefFoundError(var3.getMessage());
           }
       }
   }
   ```



## CGLIB动态代理

cglib动态代理基于asm字节码操纵框架，动态生成代理对象，代码示例

1. 代理逻辑处理类，实现`MethodInterceptor`接口，类似实现`InvocationHandler`接口

   ```java
   import net.sf.cglib.proxy.MethodInterceptor;
   import net.sf.cglib.proxy.MethodProxy;
   
   import java.lang.reflect.Method;
   
   public class LogIntercepter implements MethodInterceptor {
       @Override
       public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
           // 调用被代理对象的method方法前
           System.out.println("cglib调用前");
           // 调用被代理对象的method方法
           Object result = methodProxy.invokeSuper(o, objects);
           System.out.println("调用方法"+method.getName());
           // 调用被代理对象的method方法后
           System.out.println("cglib调用后");
           // 返回被代理对象method执行结构
           return result;
       }
   }
   ```

2. 获取代理对象

   ```java
   public class Main {
   
       public static void main(String[] args) throws IOException {
           Enhancer enhancer = new Enhancer();
           enhancer.setSuperclass(UserServiceImpl.class);
           enhancer.setCallback(new LogIntercepter());
           UserServiceImpl proxy = (UserServiceImpl) enhancer.create();
           proxy.getRoleById(100L);
           System.in.read();
       }
   }
   ```

3. 查看生成的代理类

   ```java
   //
   // Source code recreated from a .class file by IntelliJ IDEA
   // (powered by FernFlower decompiler)
   //
   
   import java.lang.reflect.Method;
   import net.sf.cglib.core.Signature;
   import net.sf.cglib.proxy.Callback;
   import net.sf.cglib.proxy.Factory;
   import net.sf.cglib.proxy.MethodInterceptor;
   import net.sf.cglib.proxy.MethodProxy;
   
   public class UserServiceImpl$$EnhancerByCGLIB$$f9970ba9 extends UserServiceImpl implements Factory {
       private boolean CGLIB$BOUND;
       private static final ThreadLocal CGLIB$THREAD_CALLBACKS;
       private MethodInterceptor CGLIB$CALLBACK_0;
       private static final Method CGLIB$update$0$Method;
       private static final MethodProxy CGLIB$update$0$Proxy;
       private static final Object[] CGLIB$emptyArgs;
       private static final Method CGLIB$getRoleById$1$Method;
       private static final MethodProxy CGLIB$getRoleById$1$Proxy;
       private static final Method CGLIB$selectNameById$2$Method;
       private static final MethodProxy CGLIB$selectNameById$2$Proxy;
       private static final Method CGLIB$finalize$3$Method;
       private static final MethodProxy CGLIB$finalize$3$Proxy;
       private static final Method CGLIB$equals$4$Method;
       private static final MethodProxy CGLIB$equals$4$Proxy;
       private static final Method CGLIB$toString$5$Method;
       private static final MethodProxy CGLIB$toString$5$Proxy;
       private static final Method CGLIB$hashCode$6$Method;
       private static final MethodProxy CGLIB$hashCode$6$Proxy;
       private static final Method CGLIB$clone$7$Method;
       private static final MethodProxy CGLIB$clone$7$Proxy;
   
       public UserServiceImpl$$EnhancerByCGLIB$$f9970ba9() {
           CGLIB$BIND_CALLBACKS(this);
       }
   
       static {
           CGLIB$STATICHOOK1();
       }
   
       protected final void finalize() throws Throwable {
           MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
           if (var10000 == null) {
               CGLIB$BIND_CALLBACKS(this);
               var10000 = this.CGLIB$CALLBACK_0;
           }
   
           if (var10000 != null) {
               var10000.intercept(this, CGLIB$finalize$3$Method, CGLIB$emptyArgs, CGLIB$finalize$3$Proxy);
           } else {
               super.finalize();
           }
       }
   
       public final boolean equals(Object var1) {
           MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
           if (var10000 == null) {
               CGLIB$BIND_CALLBACKS(this);
               var10000 = this.CGLIB$CALLBACK_0;
           }
   
           if (var10000 != null) {
               Object var2 = var10000.intercept(this, CGLIB$equals$4$Method, new Object[]{var1}, CGLIB$equals$4$Proxy);
               return var2 == null ? false : (Boolean)var2;
           } else {
               return super.equals(var1);
           }
       }
   
       public final String toString() {
           MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
           if (var10000 == null) {
               CGLIB$BIND_CALLBACKS(this);
               var10000 = this.CGLIB$CALLBACK_0;
           }
   
           return var10000 != null ? (String)var10000.intercept(this, CGLIB$toString$5$Method, CGLIB$emptyArgs, CGLIB$toString$5$Proxy) : super.toString();
       }
   
       public final int hashCode() {
           MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
           if (var10000 == null) {
               CGLIB$BIND_CALLBACKS(this);
               var10000 = this.CGLIB$CALLBACK_0;
           }
   
           if (var10000 != null) {
               Object var1 = var10000.intercept(this, CGLIB$hashCode$6$Method, CGLIB$emptyArgs, CGLIB$hashCode$6$Proxy);
               return var1 == null ? 0 : ((Number)var1).intValue();
           } else {
               return super.hashCode();
           }
       }
   
       protected final Object clone() throws CloneNotSupportedException {
           MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
           if (var10000 == null) {
               CGLIB$BIND_CALLBACKS(this);
               var10000 = this.CGLIB$CALLBACK_0;
           }
   
           return var10000 != null ? var10000.intercept(this, CGLIB$clone$7$Method, CGLIB$emptyArgs, CGLIB$clone$7$Proxy) : super.clone();
       }
   
       public final void update(Long var1, String var2) {
           MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
           if (var10000 == null) {
               CGLIB$BIND_CALLBACKS(this);
               var10000 = this.CGLIB$CALLBACK_0;
           }
   
           if (var10000 != null) {
               var10000.intercept(this, CGLIB$update$0$Method, new Object[]{var1, var2}, CGLIB$update$0$Proxy);
           } else {
               super.update(var1, var2);
           }
       }
   
       public Object newInstance(Callback var1) {
           CGLIB$SET_THREAD_CALLBACKS(new Callback[]{var1});
           return new UserServiceImpl$$EnhancerByCGLIB$$f9970ba9();
       }
   
       public Object newInstance(Class[] var1, Object[] var2, Callback[] var3) {
           CGLIB$SET_THREAD_CALLBACKS(var3);
           UserServiceImpl$$EnhancerByCGLIB$$f9970ba9 var10000 = new UserServiceImpl$$EnhancerByCGLIB$$f9970ba9;
           switch (var1.length) {
               case 0:
                   var10000.<init>();
                   return var10000;
               default:
                   throw new IllegalArgumentException("Constructor not found");
           }
       }
   
       public Object newInstance(Callback[] var1) {
           CGLIB$SET_THREAD_CALLBACKS(var1);
           return new UserServiceImpl$$EnhancerByCGLIB$$f9970ba9();
       }
   
       public void setCallback(int var1, Callback var2) {
           switch (var1) {
               case 0:
                   this.CGLIB$CALLBACK_0 = (MethodInterceptor)var2;
           }
   
       }
   
       public final String getRoleById(Long var1) {
           MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
           if (var10000 == null) {
               CGLIB$BIND_CALLBACKS(this);
               var10000 = this.CGLIB$CALLBACK_0;
           }
   
           return var10000 != null ? (String)var10000.intercept(this, CGLIB$getRoleById$1$Method, new Object[]{var1}, CGLIB$getRoleById$1$Proxy) : super.getRoleById(var1);
       }
   
       public final String selectNameById(Long var1) {
           MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
           if (var10000 == null) {
               CGLIB$BIND_CALLBACKS(this);
               var10000 = this.CGLIB$CALLBACK_0;
           }
   
           return var10000 != null ? (String)var10000.intercept(this, CGLIB$selectNameById$2$Method, new Object[]{var1}, CGLIB$selectNameById$2$Proxy) : super.selectNameById(var1);
       }
   
       public void setCallbacks(Callback[] var1) {
           this.CGLIB$CALLBACK_0 = (MethodInterceptor)var1[0];
       }
   
       public static void CGLIB$SET_THREAD_CALLBACKS(Callback[] var0) {
           CGLIB$THREAD_CALLBACKS.set(var0);
       }
   
       public Callback getCallback(int var1) {
           CGLIB$BIND_CALLBACKS(this);
           MethodInterceptor var10000;
           switch (var1) {
               case 0:
                   var10000 = this.CGLIB$CALLBACK_0;
                   break;
               default:
                   var10000 = null;
           }
   
           return var10000;
       }
   
       public Callback[] getCallbacks() {
           CGLIB$BIND_CALLBACKS(this);
           return new Callback[]{this.CGLIB$CALLBACK_0};
       }
   
       public static MethodProxy CGLIB$findMethodProxy(Signature var0) {
           String var10000 = var0.toString();
           switch (var10000.hashCode()) {
               case -1574182249:
                   if (var10000.equals("finalize()V")) {
                       return CGLIB$finalize$3$Proxy;
                   }
                   break;
               case -508378822:
                   if (var10000.equals("clone()Ljava/lang/Object;")) {
                       return CGLIB$clone$7$Proxy;
                   }
                   break;
               case 12671749:
                   if (var10000.equals("selectNameById(Ljava/lang/Long;)Ljava/lang/String;")) {
                       return CGLIB$selectNameById$2$Proxy;
                   }
                   break;
               case 589213525:
                   if (var10000.equals("update(Ljava/lang/Long;Ljava/lang/String;)V")) {
                       return CGLIB$update$0$Proxy;
                   }
                   break;
               case 713659722:
                   if (var10000.equals("getRoleById(Ljava/lang/Long;)Ljava/lang/String;")) {
                       return CGLIB$getRoleById$1$Proxy;
                   }
                   break;
               case 1826985398:
                   if (var10000.equals("equals(Ljava/lang/Object;)Z")) {
                       return CGLIB$equals$4$Proxy;
                   }
                   break;
               case 1913648695:
                   if (var10000.equals("toString()Ljava/lang/String;")) {
                       return CGLIB$toString$5$Proxy;
                   }
                   break;
               case 1984935277:
                   if (var10000.equals("hashCode()I")) {
                       return CGLIB$hashCode$6$Proxy;
                   }
           }
   
           return null;
       }
   
       final void CGLIB$update$0(Long var1, String var2) {
           super.update(var1, var2);
       }
   
       final void CGLIB$finalize$3() throws Throwable {
           super.finalize();
       }
   
       final boolean CGLIB$equals$4(Object var1) {
           return super.equals(var1);
       }
   
       final String CGLIB$toString$5() {
           return super.toString();
       }
   
       final int CGLIB$hashCode$6() {
           return super.hashCode();
       }
   
       final Object CGLIB$clone$7() throws CloneNotSupportedException {
           return super.clone();
       }
   
       static void CGLIB$STATICHOOK1() {
           CGLIB$THREAD_CALLBACKS = new ThreadLocal();
           Class var0;
           ClassLoader var10000 = (var0 = Class.forName("UserServiceImpl$$EnhancerByCGLIB$$f9970ba9")).getClassLoader();
           CGLIB$emptyArgs = new Object[0];
           CGLIB$update$0$Proxy = MethodProxy.create(var10000, (CGLIB$update$0$Method = Class.forName("UserServiceImpl").getDeclaredMethod("update", Class.forName("java.lang.Long"), Class.forName("java.lang.String"))).getDeclaringClass(), var0, "(Ljava/lang/Long;Ljava/lang/String;)V", "update", "CGLIB$update$0");
           CGLIB$getRoleById$1$Proxy = MethodProxy.create(var10000, (CGLIB$getRoleById$1$Method = Class.forName("UserServiceImpl").getDeclaredMethod("getRoleById", Class.forName("java.lang.Long"))).getDeclaringClass(), var0, "(Ljava/lang/Long;)Ljava/lang/String;", "getRoleById", "CGLIB$getRoleById$1");
           CGLIB$selectNameById$2$Proxy = MethodProxy.create(var10000, (CGLIB$selectNameById$2$Method = Class.forName("UserServiceImpl").getDeclaredMethod("selectNameById", Class.forName("java.lang.Long"))).getDeclaringClass(), var0, "(Ljava/lang/Long;)Ljava/lang/String;", "selectNameById", "CGLIB$selectNameById$2");
           CGLIB$finalize$3$Proxy = MethodProxy.create(var10000, (CGLIB$finalize$3$Method = Class.forName("java.lang.Object").getDeclaredMethod("finalize")).getDeclaringClass(), var0, "()V", "finalize", "CGLIB$finalize$3");
           CGLIB$equals$4$Proxy = MethodProxy.create(var10000, (CGLIB$equals$4$Method = Class.forName("java.lang.Object").getDeclaredMethod("equals", Class.forName("java.lang.Object"))).getDeclaringClass(), var0, "(Ljava/lang/Object;)Z", "equals", "CGLIB$equals$4");
           CGLIB$toString$5$Proxy = MethodProxy.create(var10000, (CGLIB$toString$5$Method = Class.forName("java.lang.Object").getDeclaredMethod("toString")).getDeclaringClass(), var0, "()Ljava/lang/String;", "toString", "CGLIB$toString$5");
           CGLIB$hashCode$6$Proxy = MethodProxy.create(var10000, (CGLIB$hashCode$6$Method = Class.forName("java.lang.Object").getDeclaredMethod("hashCode")).getDeclaringClass(), var0, "()I", "hashCode", "CGLIB$hashCode$6");
           CGLIB$clone$7$Proxy = MethodProxy.create(var10000, (CGLIB$clone$7$Method = Class.forName("java.lang.Object").getDeclaredMethod("clone")).getDeclaringClass(), var0, "()Ljava/lang/Object;", "clone", "CGLIB$clone$7");
       }
   
       final String CGLIB$getRoleById$1(Long var1) {
           return super.getRoleById(var1);
       }
   
       private static final void CGLIB$BIND_CALLBACKS(Object var0) {
           UserServiceImpl$$EnhancerByCGLIB$$f9970ba9 var1 = (UserServiceImpl$$EnhancerByCGLIB$$f9970ba9)var0;
           if (!var1.CGLIB$BOUND) {
               var1.CGLIB$BOUND = true;
               Object var10000 = CGLIB$THREAD_CALLBACKS.get();
               if (var10000 != null) {
                   var1.CGLIB$CALLBACK_0 = (MethodInterceptor)((Callback[])var10000)[0];
               }
           }
   
       }
   
       final String CGLIB$selectNameById$2(Long var1) {
           return super.selectNameById(var1);
       }
   }
   
   ```

   

