# MyBatis

## 1. 数据库 ORM 

ORM 是 对象关系映射的缩写，是为了解决面向对象与关系数据库存在的互不匹配的现象的技术。

O:Object 对象 -> Java里的类/对象。
R:Relation 关系 -> 数据库中的表。
M:Mapping 映射 -> 对象与数据库之间的映射关系。

也就是说：
| Java      | MySQL    |
| --------- | -------- |
| 类 class   | 表 table  |
| 对象 object | 一行数据 row |
| 属性 field  | 列 column |

比如
```Java
public class User {
    private Integer id; //主键 ID
    private String name; //name 列
    private Integer age; //age 列
} 
```
相当于
```sql
CREATE TABLE user (
    id INT, 
    name VARCHAR(50),
    age INT
);
```

类要写 private，符合Jean Bean 规范，方便框架操作。

### ORM 做了什么？

```
拼 SQL -> 连数据库 ->执行 SQL ->获取结果集 ->创建 User 对象 -> 把字段塞进去 ->返回对象
```

也就是说 JAVA User 其实是数据库里：select * from user where id = 1; 的结果。


### getter and setter

getter and setter 是 Java Bean 规范，用来获取和设置对象属性的方法。

因为 private 修饰的属性，外部无法直接访问，所以需要 getter and setter 方法来访问。

- 通过getXX向类中传入数据
- 通过setXX向类中获取数据

```Java
public class User {
    private Integer id; //主键 ID

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
```

### toString

toString 是 Java Object 类的方法，用来返回对象的字符串表示。

```Java
public class User {
    private Integer id; //主键 ID
    private String name; //name 列
    private Integer age; //age 列

    @Override
    public String toString() {
        return "User{" +
```
