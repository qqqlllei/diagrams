## 代理
### 动态代理
#### java proxy
##### 使用
    public class JdkProxy {
        public static void main(String[] args) {
            CaiQuan caiQuan = new CaiQuan(); // 被代理对象
            LogProxyFactory logProxyFactory = new LogProxyFactory();
            Dog dog = (Dog) logProxyFactory.newInstance(caiQuan); // 生成proxy
            dog.say();
            YingDuan yingDuan = new YingDuan();
            Cat cat = (Cat) logProxyFactory.newInstance(yingDuan);
            cat.say();
        }
    }
    interface Cat{ void say();} // 需要定义接口
    interface Dog{ void say();} 
    
    class YingDuan implements Cat{
        @Override
        public void say() {
            System.out.println("YingDuan");
        }
    }
    class CaiQuan implements Dog{
        @Override
        public void say() {
            System.out.println("CaiQuan");
        }
    }
    class LogProxyFactory{
        public Object newInstance(final Object target){
           return Proxy.newProxyInstance(Thread.currentThread().getContextClassLoader(),
                   target.getClass().getInterfaces(),
                   (proxy, method, args) -> {  // 实现InvocationHandler的内部类
                       System.out.println("begin"); // 前置增强
                       Object obj = method.invoke(target,args); // 执行目标方法
                       System.out.println("end");  // 后置增强
                       return obj;
                   });
        }
    }
##### 原理
        public static Object newProxyInstance(ClassLoader loader,
                                              Class<?>[] interfaces,
                                              InvocationHandler h) throws IllegalArgumentException{
            final Class<?>[] intfs = interfaces.clone(); // 获取接口类型
            Class<?> cl = getProxyClass0(loader, intfs); // 动态创建java字节码文件 ProxyClassFactory.apply()-->ProxyGenerator.generateProxyClass()返回字节码二进制-->defineClass0()本地方法
            final Constructor<?> cons = cl.getConstructor(constructorParams); 
            final InvocationHandler ih = h;
            return cons.newInstance(new Object[]{h});
        }
        
        
        // 动态生成的二进制字节码
        public final class Log$proxy extends Proxy implements Dog, Cat {
            private static Method m1;
            private static Method m2;
            private static Method m4;
            private static Method m3;
            private static Method m0;
        
            public Log$proxy(InvocationHandler var1) throws  {
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
        
            public final void sayCat() throws  {
                try {
                    super.h.invoke(this, m4, (Object[])null);
                } catch (RuntimeException | Error var2) {
                    throw var2;
                } catch (Throwable var3) {
                    throw new UndeclaredThrowableException(var3);
                }
            }
        
            public final void say() throws  {
                try {
                    super.h.invoke(this, m3, (Object[])null);
                } catch (RuntimeException | Error var2) {
                    throw var2;
                } catch (Throwable var3) {
                    throw new UndeclaredThrowableException(var3);
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
        
            static {
                try {
                    m1 = Class.forName("java.lang.Object").getMethod("equals", Class.forName("java.lang.Object"));
                    m2 = Class.forName("java.lang.Object").getMethod("toString");
                    m4 = Class.forName("com.example.demo.jvm.Cat").getMethod("sayCat");
                    m3 = Class.forName("com.example.demo.jvm.Dog").getMethod("say");
                    m0 = Class.forName("java.lang.Object").getMethod("hashCode");
                } catch (NoSuchMethodException var2) {
                    throw new NoSuchMethodError(var2.getMessage());
                } catch (ClassNotFoundException var3) {
                    throw new NoClassDefFoundError(var3.getMessage());
                }
            }
        }
#### cglib
#### aspecctj
#### instrumentation