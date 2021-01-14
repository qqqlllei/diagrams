### 通常一个mapper.xml文件，都会对应一个Dao接口，这个Dao接口的工作原理是什么？Dao接口里的方法，参数不同时，方法能重载吗？
    根据这个Dao生成一个MapperProxy对象，该对象通过全类名和mapper.xml的namespace对应绑定关系。
    mapper.xml文件中以一个update|select|insert|delete标签来解析出一个mappedStatement对象，对应关系就是namespace.methodName，及不能有方法的重载
### 简述Mybatis的插件运行原理，以及如何编写一个插件。
    
    @Intercepts({@Signature(type= Executor.class,method = "query", args = {MappedStatement.class,Object.class, RowBounds.class, ResultHandler.class})})
    @Component // 将插件变成一个spring管理的bean，MybatisAutoConfiguration会将spring 管理的bean 和mybatis 建立关联关系
    public class MybatisPlugin implements Interceptor {
        @Override
        public Object intercept(Invocation invocation) throws Throwable {
            System.out.println("开始拦截.....");
            Object proceed = invocation.proceed();
            System.out.println("结束拦截.....");
            return proceed;
        }
        @Override
        public Object plugin(Object target) {
            System.out.println("生成代理对象....");
            return  Plugin.wrap(target, this);
        }
        @Override
        public void setProperties(Properties properties) {
    
        }
    }
    
### Mybatis动态sql有什么用？执行原理？有哪些动态sql？
    
### Mybatis是否支持延迟加载？如果支持，它的实现原理是什么？

### Mybatis的一级、二级缓存:

### 在mapper中如何传递多个参数?
    
###  一对一、一对多的关联查询 ？ 

### 什么是MyBatis的接口绑定？有哪些实现方式？

