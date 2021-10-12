## 三层架构

1. 界面层
   - 主要功能是接收用户的数据,显式请求的处理结果
2. 业务逻辑层
   - 接收界面层递过来的数据,检查数据,计算业务逻辑,调用数据访问层获取数据
3. 数据访问层
   - 主要实现对数据的增,删,改,查.将存储在数据库中的数据提交给业务层,同时将业务层处理的数据保存到数据库

![image-20210815200459371](https://i.loli.net/2021/08/15/b16INaFPWje4GnQ.png)

#### 优点

1. 结构清晰,耦合度低,各层分工明确
2. 可维护性高,可扩展性高
3. 有利于标准化
4. 开发人员可以只关注整个结构中的其中某一层的的功能实现
5. 有 利于各层逻辑的复用

## MyBatis

1. 配置pom.xml
2. 创建实体类
3. 编写Dao接口
4. 编写Dao接口Mapper映射文件
5. 创建MyBatis主配置文件
6. 编写工具类获取sqlSession对象
7. 编写实现类实现Dao接口重写Dao接口中的方法
8. 根据需求执行代码

#### 配置pom.xml

```xml
<!--单元测试-->
<dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
    <!-- mybatis配置-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.1</version>
    </dependency>
    <!-- mysql驱动 -->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.26</version>
    </dependency>
  </dependencies>
```

```xml
<!-- 插件-->
<resources>
  <resource>
    <directory>src/main/java</directory>
    <includes>
      <include>**/*.properties</include>
      <include>**/*.xml</include>
    </includes>
  </resource>
</resources>
```

#### 创建实体类

```java
public class Student {
    private Integer id;
    private String name;
    private String email;
    private Integer age;
    
    //get() set() toString
    }
```

#### 编写Dao接口

```java
public interface StudentDao {
    public List<Student> selectStudents();
    
    public int insertStudent(Student student);

    public int deleteStudent(Integer id);

    public int updateStudent(Student student);
}
```

#### 编写Dao接口Mapper映射文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- 必须有值，自定义的唯一字符串   推荐使用：dao接口的全限定名称-->
<mapper namespace="org.yk.dao.StudentDao">
    <!--
    <select>: 查询数据， 标签中必须是select语句
    id: sql语句的自定义名称，推荐使用dao接口中方法名称，
    使用名称表示要执行的sql语句
    resultType: 查询语句的返回结果数据类型，使用全限定类名
    -->
<select id="selectStudents" resultType="org.yk.domain.Student">
    select * from student;
</select>
    <insert id="insertStudent">
        insert into student (id,name,email,age)
        values(#{id},#{name},#{email},#{age})
    </insert>
    <delete id="deleteStudent">
        delete from student where id=#{id}
    </delete>
    <update id="updateStudent">
        update student set age=#{age} where id = #{id}
    </update>
</mapper>
```

#### 创建MyBatis主配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <!--settings：控制mybatis全局行为-->
    <settings>
        <!--设置mybatis输出日志-->
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
    <environments default="mydev">
        <environment id="mydev">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <!--数据库的驱动类名-->
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <!--连接数据库的url字符串-->
                <property name="url" value="jdbc:mysql://localhost:3306/ssm"/>
                <!--访问数据库的用户名-->
                <property name="username" value="root"/>
                <!--密码-->
                <property name="password" value="123456"/>
            </dataSource>
        </environment>

    </environments>

    <!-- sql mapper(sql映射文件)的位置-->
    <mappers>
         <!--一个mapper标签指定一个文件的位置。
           从类路径开始的路径信息。  target/clasess(类路径)
        -->
        <mapper resource="com/bjpowernode/dao/StudentDao.xml"/>
        <!--<mapper resource="com/bjpowernode/dao/SchoolDao.xml" />-->
        
        
        <!--使用包名-->
        <!--
			这个包中所有xml文件一次都能加载给mybatis

			要求
			 1. mapper文件名称需要和接口名称一样， 区分大小写的一样
             2. mapper文件和dao接口需要在同一目录
		-->
        <!--<package name="com.bjpowernode.dao"/>-->
    </mappers>
</configuration>
```

#### 编写工具类获取sqlSession对象

```java
public class MyBatisUtils {

    private  static  SqlSessionFactory factory = null;
    static {
        String config="mybatis.xml"; // 需要和你的项目中主配置的文件名一样
        try {
            //读取配置文件
            InputStream in = Resources.getResourceAsStream(config);
            //创建SqlSessionFactory对象，使用SqlSessionFactoryBuild
            factory = new SqlSessionFactoryBuilder().build(in);

        } catch (IOException e) {
            e.printStackTrace();
        }
    }


    //获取SqlSession的方法
    public static SqlSession getSqlSession() {
        SqlSession sqlSession  = null;
        if( factory != null){
            //获取SqlSession,SqlSession能执行sql语句
            sqlSession = factory.openSession();// 非自动提交事务
        }
        return sqlSession;
    }
}
```

#### 编写实现类实现Dao接口重写Dao接口中的方法

```Java
public class StudentDaoImpl implements StudentDao {
    @Override
    public List<Student> selectStudents() {
        //获取SqlSession对象
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        //指定要执行的sql语句的标识。  sql映射文件中的namespace + "." + 标签的id值
        String sqlId="com.bjpowernode.dao.StudentDao.selectStudents";
        //执行sql语句， 使用SqlSession类的方法
        List<Student> students  = sqlSession.selectList(sqlId);
        //关闭
        sqlSession.close();
        return students;
    }
    @Override
    public int insertStudent(Student student) {
        //获取SqlSession对象
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        //指定要执行的sql语句的标识。  sql映射文件中的namespace + "." + 标签的id值
        String sqlId="com.bjpowernode.dao.StudentDao.insertStudent";
        //执行sql语句， 使用SqlSession类的方法
        int nums = sqlSession.insert(sqlId,student);
        //提交事务
        sqlSession.commit();
        //关闭
        sqlSession.close();
        return nums;
    }
}
```

#### 根据需求执行代码

```Java
public class Test1 {
    @Test
    public void test() throws IOException {
        final StudentDaoImpl studentDao = new StudentDaoImpl();
        final List<Student> select = studentDao.select();
        select.forEach(student -> System.out.println(student));
    }
}
```

## Mybatis代理

更改最后两步

- 去掉Dao接口实现类

- getMapper获取代理对象

```Java
public class Test1 {
    @Test
    public void selectTest() throws IOException {
        final SqlSession sqlSession = MyBatisUntil.getSqlSession();
        final StudentDao mapper = sqlSession.getMapper(StudentDao.class);
        final List<Student> students = mapper.selectStudent();
        students.forEach(student -> System.out.println(student));
    }
    @Test
    public void insertTest(){
        final SqlSession sqlSession = MyBatisUntil.getSqlSession();
        final StudentDao mapper = sqlSession.getMapper(StudentDao.class);
        final Student student = new Student(15,"韩非","hanfei.@gmail",20);

        final int i = mapper.insertStudent(student);
    }
    @Test
    public void deleteTest(){
        final SqlSession sqlSession = MyBatisUntil.getSqlSession();
        final StudentDao mapper = sqlSession.getMapper(StudentDao.class);
        final int i = mapper.deleteStudent(1002);
    }
    @Test
    public void updateTest(){
        final SqlSession sqlSession = MyBatisUntil.getSqlSession();
        final StudentDao mapper = sqlSession.getMapper(StudentDao.class);
        final Student student = new Student();
        student.setId(2);
        student.setAge(70);

        final int i = mapper.updateStudent(student);
    }
}
```

#### 参数

```Java
public interface StudentDao {
    public Student selectStudentById(Integer id);
    public List<Student> selectStudentByParam(@Param("myid") int id ,@Param("myname") String name);

    public List<Student> selectStudentByClass(Student student);

    public List<Student> selectStudentByNum(Integer id, String name);

    public List<Student> selectStudentByMap(Map<String, Object> map);
}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.yk.dao.StudentDao">

    <!--Dao接口中方法的参数只有一个简单类型（ java基本类型和 String），占位符 #{ 任意字符 }，和方法的参数名无关。-->
<select id="selectStudentById" parameterType="java.lang.Integer" resultType="org.yk.domain.Student">
    select * from student where id=#{idx} ;
</select>

<!--    当Dao接口方法多个参数，需要通过名称使用参数。 在方法形参前面加入 @Param(“自定义参数名 mapper文件使用 #{自定义参数名 }。-->
    <select id="selectStudentByParam" resultType="org.yk.domain.Student">
select * from student where id=#{myid} or name = #{myname}
    </select>

<!--    使用 java对象传递参数， java的属性值就是 sql需要的参数值。 每一个属性就是一个参数。-->
<!--    语法格式：#{ property,javaType=java中数据类型名 ,jdbcType=数据类型名称 }-->
<!--    javaType, jdbcType的类型 MyBatis可以检测出来，一般不需要设置。 常用格式 #{ property }-->
    <select id="selectStudentByClass" resultType="org.yk.domain.Student">
  select * from student where id=#{id} or name =#{name}
    </select>
    
    
    <!-- 以下两种不建议使用-->
    

<!--    参数位置从 0开始， 引用参数语法 #{ arg位置 } 第一个参数是 #{arg0}, 第二个是 #{arg1}-->
<!--    注意： mybatis 3.3版本和之前的版本使用 #{0},#{1}方式， 从 mybatis3.4开始使用 #{arg0}方式。-->
    <select id="selectStudentByNum" resultType="org.yk.domain.Student">
        select * from student where id=#{arg0} or name = #{arg1}
    </select>

<!--    Map集合可以存储多个值， 使用 Map向 mapper文件一次传入多个参数。 Map集合使用 String的 key-->
<!--    Object类型 的值存储参数。 mapper文件使用 # { key } 引用参数值。-->
    <select id="selectStudentByMap" resultType="org.yk.domain.Student">
        select * from student where id=#{myid} or name =#{myname};
    </select>

</mapper>
```

```Java
public class Test1 {
 @Test
    public void test1(){
     final SqlSession sqlSession = MyBatisUntil.getSqlSession();
     final StudentDao mapper = sqlSession.getMapper(StudentDao.class);
     final Student student = mapper.selectStudentById(1);
     System.out.println(student);
 }
@Test
 public void test2(){
 final SqlSession sqlSession = MyBatisUntil.getSqlSession();
 final StudentDao mapper = sqlSession.getMapper(StudentDao.class);
 final List<Student> students = mapper.selectStudentByParam(4,"嬴政");
 students.forEach(student -> System.out.println(student));
}
 @Test
 public void test3(){
  final SqlSession sqlSession = MyBatisUntil.getSqlSession();
  final StudentDao mapper = sqlSession.getMapper(StudentDao.class);
  final Student student = new Student();
  student.setId(2);
  student.setName("霍去病");
  final List<Student> students = mapper.selectStudentByClass(student);
  students.forEach(student1 -> System.out.println(student));
 }
 @Test
 public void Test4(){
  final SqlSession sqlSession = MyBatisUntil.getSqlSession();
  final StudentDao mapper = sqlSession.getMapper(StudentDao.class);
  final List<Student> students = mapper.selectStudentByNum(2, "韩非");
  students.forEach(student -> System.out.println(student));
 }
 @Test
 public void Test5(){
  final StudentDao mapper = MyBatisUntil.getSqlSession().getMapper(StudentDao.class);
  final Map<String, Object> map = new HashMap<>();
  map.put("myid", 2);
  map.put("myname", "霍去病");
  final List<Student> students = mapper.selectStudentByMap(map);
  students.forEach(student -> System.out.println(student));
 }
}
```

#### #和 $区别

1. #使用 ？在sql语句中做占位的， 使用PreparedStatement执行sql，效率高
2. #能够避免sql注入，更安全。
  3. $不使用占位符，是字符串连接方式，使用Statement对象执行sql，效率低
  4. $有sql注入的风险，缺乏安全性。
  5. $:可以替换表名或者列名

#### 别名

- 方式一:

```xml
		<!--type:自定义类型的全限定名称
            alias:别名（短小，容易记忆的）-->
<typeAlias type="com.bjpowernode.domain.Student" alias="stu" />
```

- 方式二:

```xml
<!-- <package> name是包名， 这个包中的所有类，类名就是别名（类名不区分大小写）--> 
<package name="com.bjpowernode.domain"/>
```

#### resultType

- resultType结果类型， 指sql语句执行完毕后， 数据转为的java对象， java类型是任意的。
- 处理方式
  1. mybatis执行sql语句， 然后mybatis调用类的无参数构造方法，创建对象。
  2. mybatis把ResultSet指定列值付给同名的属性。

```xml
	<select id="selectMultiPosition" resultType="com.bjpowernode.domain.Student">
          select id,name, email,age from student
        </select>
```

```Java
	  //对等的jdbc
ResultSet rs = executeQuery(" select id,name, email,age from student" )
		  while(rs.next()){
               Student  student = new Student();
					student.setId(rs.getInt("id"));
					student.setName(rs.getString("name"))
		  }
```

###### 返回Map

- sql 的查询结果作为Map 的 key 和 value。推荐使用Map<Object,Object>。 
- 注意：Map 作为接口返回值，sql 语句的查询结果最多只能有一条记录。大于一条记录是错误。

![image-20210919144855239](https://i.loli.net/2021/09/19/FIcHeSPpXb8WNaL.png)

#### resultMap

- resultMap:结果映射， 指定列名和java对象的属性对应关系。
  1. 自定义列值赋值给哪个属性
  2. 当你的列名和属性名不一样时，一定使用resultMap<!--实体类中setXX和数据库中列名不一致时-->

- 使用步骤
  1. 定义resultMap
  2. 在select标签,使用resultMap来引用1定义的

```xml
 <!--定义resultMap
        id:自定义名称，表示你定义的这个resultMap
        type：java类型的全限定名称
    -->
    <resultMap id="studentMap" type="com.bjpowernode.domain.Student">
        <!--列名和java属性的关系-->
        <!--注解列，使用id标签
            column :列名
            property:java类型的属性名
        -->
        <id column="id" property="id" />
        <!--非主键列，使用result-->
        <result column="name" property="name" />
        <result column="email" property="email" />
        <result column="age" property="age" />

    </resultMap>
    <select id="selectAllStudents" resultMap="studentMap">
        select id,name, email , age from student
    </select>
```

- 第二种列名和属性名不一样时的解决方案

  ```xml
   <!--列名和属性名不一样：第二种方式
         resultType的默认原则是 同名的列值赋值给同名的属性， 使用列别名(java对象的属性名)
      -->
      <select id="selectDiffColProperty" resultType="com.bjpowernode.domain.MyStudent">
          select id as stuid ,name as stuname, email as stuemail , age stuage from student
      </select>
  ```

  

## 动态SQL

#### `<if>`

```xml
<if test=”条件”>  sql 语句的部分 </if> 
```

#### `<where>` 

<if/>标签的中存在一个比较麻烦的地方：需要在where 后手工添加1=1 的子句。因为，若 where 后的所有<if/>条件均为 false，而 where 后若又没有 1=1 子句，则 SQL 中就会只剩下一个空的 where，SQL出错。所以，在 where 后，需要添加永为真子句 1=1，以防止这种情况的发生。但当数据量很大时，会严重影响查询效率。 
使用<where/>标签，在有查询条件时，可以自动添加上 where 子句；没有查询条件时，不会添加where 子句。需要注意的是，第一个<if/>标签中的SQL 片断，可以不包含 and。不过，写上 and 也不错，系统会将多出的and 去掉。**但其它<if/>中SQL 片断的and，必须要求写上。**否则SQL 语句将拼接出错 

```xml
<where> 其他动态sql </where>
```

#### `<foreach>`

```xml
<foreach collection="集合类型" open="开始的字符" close="结束的字符" item="集合中的成员" separator="集合成员之间的分隔符"> 
		 #{item 的值}
 </foreach> 
```

- collection:表示接口中的方法参数的类型， 如果是数组使用array , 如果是list集合使用list
- item:自定义的，表示数组和集合成员的变量
- open:循环开始是的字符
- close:循环结束时的字符
- separator:集合成员之间的分隔符

###### 遍历 List<简单类型 >

```xml
<select id="selectStudentForList" resultType="com.bjpowernode.domain.Student"> 
    select id,name,email,age from student 
    <if test="list !=null and list.size > 0 "> 
        where id in 
        <foreach collection="list" open="(" close=")" item="stuid" separator=","> 
            #{stuid} 
        </foreach> 
    </if> 
</select>
```

![image-20210919162543667](https://i.loli.net/2021/09/19/zjg5uDOlE6IqNxd.png)

###### 遍历 List<对象类型 >

```xml
<select id="selectStudentForList2" resultType="com.bjpowernode.domain.Student">
    select id,name,email,age from student
    <if test="list !=null and list.size > 0 "> 
        where id in 
        <foreach collection="list" open="(" close=")" item="stuobject" separator=","> 
            #{stuobject.id} 
        </foreach> 
    </if> 
</select>
```

![image-20210919162522797](https://i.loli.net/2021/09/19/cM31Lf2KyzuRxQZ.png)

