### 通常一个mapper.xml文件，都会对应一个Dao接口，这个Dao接口的工作原理是什么？Dao接口里的方法，参数不同时，方法能重载吗？
    根据这个Dao生成一个MapperProxy对象，该对象通过全类名和mapper.xml的namespace对应绑定关系。
    mapper.xml文件中以一个update|select|insert|delete标签来解析出一个mappedStatement对象，对应关系就是namespace.methodName，及不能有方法的重载
    可以多个mapper.xml文件对应一个Dao接口，这样基本的crud可以放在基本mapper.xml文件中，复杂拓展的sql，可以放在extMapper.xml文件中
    
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
    根据匹配条件动态的拼接sql
    if || choose when otherwise || foreach 
    
### Mybatis是否支持延迟加载？如果支持，它的实现原理是什么？
    支持延迟加载
    <mapper namespace="cn.xh.dao.UserDao">
        <select id="findCategoryWithLazingload" resultMap="categoryMap">
            select * from category
        </select>
        <resultMap id="categoryMap" type="cn.xh.pojo.Category">
            <id column="cid" property="cid"></id>
            <result column="cname" property="cname"></result>
    
            <collection property="books" column="cid" select="findBookWithLazy"></collection>
        </resultMap>
    
        <select id="findBookWithLazy" parameterType="int" resultType="cn.xh.pojo.Book">
            select * from book where cid = #{cid}
        </select>
    </mapper>
    
    动态代理技术，调用对象的get方法返回null 时，触发二次查询操作

### Mybatis的一级、二级缓存:
    一级缓存时sqlSession级别的缓存，第一次查询后，对结果集进行缓存，第二次查询就可以走内存。
    sqlSession进行写操作，会清空一级缓存。
    mybatis 的一级缓存会破坏数据库的隔离级别，如果数据库的隔离级别时读已提交则会有问题，因为sqlSession的二次查询会读取内存数据。可以通过设置localCacheScope = STATEMENT来解决这个问题。

    二级缓存时mapper级别的，多个sqlSession访问相同mapper的sql语句，多个sqlSession可以共享二级缓存
### 在mapper中如何传递多个参数?
    @Param注解
###  一对一、一对多的关联查询 ？ 
    一对一通过标签<association>
        <resultMap type="Teacher" id="teacherPositionResultMap">
        		<id property="id" column="id"/>
        		<result property="no" column="t_no"/>
        		<result property="name" column="t_name"/>
        		<association property="pos" javaType="Position">
        			<id property="id" column="id"/>
        			<result property="name" column="t_pos_name"/>
        		</association>
        </resultMap>
    一对多通过标签<collection>
        <resultMap type="Position" id="positionTeacherResultMap">
            <id property="id" column="id"/>
            <result property="name" column="t_pos_name"/>
            <collection property="teacherList" javaType="list" ofType="Teacher" >
                <id property="id" column="t_id"/>
                <result property="no" column="t_no"/>
                <result property="name" column="t_name"/>	
            </collection>
        </resultMap>

    查询字段不能重名

### 什么是MyBatis的接口绑定？有哪些实现方式？
    定义接口，把接口的方法和sql语句绑定起来，通过调用方法执行数据库操作。
    注解绑定@Select @Update @Delete @Insert
    xml绑定 映射文件的namespace必须和接口的全路径名保持一致
    

