# Mybatis Plus学习笔记

## 简介

Mybatis Plus简称MP，是一个Mybatis的增强工具，在Mybatis上制作增强不做改变。简化开发，提高效率

通过Mybatis的开发需要编写xml，有了Mybatis Plus之后，单表的CRUD都可以由Mybatis Plus的API执行。它提供了很多查询和分页方式

## 使用步骤

1.新建Springboot工程

2.指定maven的mp坐标

​	mysql的驱动

3.创建实体类，定义属性和主键类型

4.创建Dao接口，需要继承BaseMapper<实体.class>

5.在springboot启动类上，加入@MapperScan(value="指定Dao接口的包名")

6.测试使用

​	在测试类或Service注入Dao接口，框架实现动态代理创建Dao的实现类对象



## 源码

底层还是基于动态代理实现的，调用Mybatis的SQLSession操作实现



## ActiveRecord

在Entity里面声明的实体类继承了Model抽象类（来自MP），Model接口里实现了各种各样的CRUD方法，在对数据库进行操作的时候，我们就可以直接生成实体类然后进行操作，不必通过MapperDao

使用Mapper的数据库操作是因为它我们写的Mapper接口继承了BasedMapper接口，里面封装了很多数据库操作

Mapper调用和AR的实操区别

```
//Mapper调用
@Test
    public void testUserGetId(){
        //创建User对象
        User user = new User();
        user.setName("李四");
        user.setAge(20);
        user.setEmail("lisi@163.com");
        //调用UserMapper的方法，也就是父接口BaseMapper中的提供的方法
        int rows = userDao.insert(user);
        System.out.println("Insert的结果： "+rows);
        int id = user.getId();
        System.out.println("主键id："+id);
    }
```

```
//AR方式
@Test
    public void testARInsert(){
        //定义dept的实体
        Dept dept = new Dept();
        dept.setName("销售部");
        dept.setMobile("010-12345678");
        dept.setManager(1);
        //调用实体对象自己的方法，完成对象自身到数据库的添加操作
        boolean flag = dept.insert();
        System.out.println("ar insert result:"+flag);
    }
```









## 表和列

### 1.主键类型

```java
public enum IdType {
    AUTO(0),
    NONE(1),
    INPUT(2),
    ASSIGN_ID(3),
    ASSIGN_UUID(4);

    private final int key;

    private IdType(int key) {
        this.key = key;
    }

    public int getKey() {
        return this.key;
    }
}
```

0.none 没有主键（一般用不到

1.auto 自动增长（mysql，sqlserver）

2.input 手工输入 (用的也比较少)

3.id_worker：实体类用Long id，表的列用bigint，int类型的大小不够

//twitter雪花算法-分布式ID

4.id_worker_str 实体类使用String id，表的列使用varchar 50

//将生成的id转换成字符串类型

5.uuid 实体类使用String id，列使用varchar 50

### 表名

TableName(value = "xxx")

### 列名

TableField(value = "xxx")

在实体类里面添加，添加在每个属性的上面

### 命名

数据库表中列的命名用下划线

实体类属性用驼峰式

自动对应