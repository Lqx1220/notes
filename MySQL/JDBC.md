###  JDBC编程步骤

1. 注册驱动(可省略)

   ```Java
    DriverManager.registerDriver(new com.mysql.jdbc.Driver());
   //或
   Class.forName("com.mysql.jdbc.Driver")
   ```

2. 获取连接

   ```java
   String url = "jdbc:mysql://127.0.0.1:3306/mybd1";
           String root  = "root";
           String password = "959977";
           DriverManager.getConnection(url, root, password);
   ```

   ```
          jdbc:mysql:// -->协议
          127.0.0.1     -->IP地址
          3306          -->数据库端口号
          mybd1         -->具体数据库实例名
   ```

3. 获取数据库操作对象（专门执行sql语句的对象）

4. 执行SQL语句

   - executeUpdate()执行增删改
   - executeQuery()执行查询

5. 处理查询结果集(只有当第四步执行的是select语句时,才有第五步处理查询结果集)

   - next()方法判断是否有下一个值
   - 用getString(),getInt()等方法取出值(可以用下标也可以用列名)

6. 释放资源

```Java
public class cj {
    public static void main(String[] args) {
       // 获取连接
        String url = "jdbc:mysql://127.0.0.1:3306/mybd1";
        String root  = "root";
        String password = "959977";
        try (final Connection connection = DriverManager.getConnection(url, root, password);
             // 获取数据库操作对象（专门执行sql语句的对象）
             final Statement statement = connection.createStatement();) {
            String sql = "insert into my_user(id,name,sex,enrollment_time,phone)" +
                    "values(10,'weiqing','M','1832-6-3',1010)";//sql语句最后不需要;
           // 执行SQL语句
            final int i = statement.executeUpdate(sql);
            System.out.println(i==1?"success":"failed");
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

```Java
public class cj {
    public static void main(String[] args) {
        // 获取连接
        String url = "jdbc:mysql://127.0.0.1:3306/mybd1";
        String root = "root";
        String password = "959977";
        String sql = "select * from my_user where id<5";
        try (final Connection connection = DriverManager.getConnection(url, root, password);
             // 获取数据库操作对象（专门执行sql语句的对象）
             final Statement statement = connection.createStatement();) {
            
            // 执行SQL语句
            try (final ResultSet resultSet = statement.executeQuery(sql)) {

                // 处理查询结果集
                while (resultSet.next()) {
                    final String name = resultSet.getString("name");
                    final int id = resultSet.getInt("id");
                    System.out.println(name + "   " + id);
                }
            } 
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

#### SQL注入

##### 原因:

用户输入的信息中含有sql语句的关键字,并且这些关键字参与sql语句的编译过程,导致sql语句的愿意被扭曲,进而达到sql注入

##### 解决

让用户信息不参与SQL语句的编译,用PreparedStatement

###### PreparedStatement原理

预先对SQL语句的框架进行编译,然后再给SQL语句传值

###### PreparedStatement和Statement区别

- Statement存在sql注入问题.PreparedStatement不存在sql注入问题
- Statement编译一次执行一次,效率低.PreparedStatement编译一次执行N次,效率更高
- PreparedStatement会在编译阶段做类型的安全检查



### PreparedStatement的JDBC步骤

```Java
public class cj {
    public static void main(String[] args) {
//        1.获取连接
        String url = "jdbc:mysql://127.0.0.1:3306/mybd1";
        String root  = "root";
        String password = "959977";
        //  要在预编译之前写SQL语句
        String sql = "select * from my_user where id< ? and enrollment_time > ?";
        try (final Connection connection = DriverManager.getConnection(url, root, password);
             //   2.获取预编译数据库操作对象
             final PreparedStatement preparedStatement = connection.prepareStatement(sql);
        ) {
//            2.1 给占位符赋值
            preparedStatement.setString(1,"4");
            preparedStatement.setString(2,"1985-1-1");
//            3.执行SQL语句
           try(final ResultSet resultSet = preparedStatement.executeQuery()) {
//               4.处理查询结果集
               while (resultSet.next()) {
                   final String  name= resultSet.getString("name");
                   final int id = resultSet.getInt("id");
                   System.out.println(name +"   "+id);
               }
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```



```Java
public class cj {
    public static void main(String[] args) {
//        1.获取连接
        String url = "jdbc:mysql://127.0.0.1:3306/mybd1";
        String root  = "root";
        String password = "959977";
        //           要在预编译之前写SQL语句
        String sql = "insert into my_user " +
                "(id,name,sex,enrollment_time,phone)" +
                "values (?,?,?,?,?)";
        try (final Connection connection = DriverManager.getConnection(url, root, password);
             //           2.获取预编译数据库操作对象
             final PreparedStatement preparedStatement = connection.prepareStatement(sql);
        ) {
//            2.1 给占位符赋值
            preparedStatement.setString(1,"5");
            preparedStatement.setString(2,"huoqubing");
            preparedStatement.setString(3,"M");
            preparedStatement.setString(4,"1815-5-18");

            preparedStatement.setString(5,"1006");
//            3.执行SQL语句
            final int i = preparedStatement.executeUpdate();
            System.out.println(i==1?"success":"failed");
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```



### 事务

JDBC会自动提交事务

要保障数据安全需要改为手动提交

步骤

1. 关闭自动提交
2. 手动提交事务
3. 失败自动回滚

```Java
public class cj {
    public static void main(String[] args) {
        String url = "jdbc:mysql://127.0.0.1:3306/mybd1";
        String root  = "root";
        String password = "959977";
        String sql = "insert into my_user " +
                "(id,name,sex,enrollment_time,phone)" +
                "values (?,?,?,?,?)";
        try (final Connection connection = DriverManager.getConnection(url, root, password);) {
//            关闭自动提交
            connection.setAutoCommit(false);
            try(final PreparedStatement preparedStatement = connection.prepareStatement(sql)) {
                preparedStatement.setString(1,"5");
                preparedStatement.setString(2,"huoqubing");
                preparedStatement.setString(3,"M");
                preparedStatement.setString(4,"1815-5-18");
                preparedStatement.setString(5,"1006");
                final int i = preparedStatement.executeUpdate();
                System.out.println(i==1?"success":"failed");
//                手动提交事务
                connection.commit();
            }catch (SQLException e){
//                失败回滚
                connection.rollback();
            }
        } catch (SQLException e) {

            e.printStackTrace();
        }
    }
}
```

