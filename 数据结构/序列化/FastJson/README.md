# FastJson

FastJson 上由阿里的一位工程师开发并开源的。

# 编码

```java
import com.alibaba.fastjson.JSON;

Group group = new Group();
group.setId(0L);
group.setName("admin");

User guestUser = new User();
guestUser.setId(2L);
guestUser.setName("guest");

User rootUser = new User();
rootUser.setId(3L);
rootUser.setName("root");

group.add(guestUser);
group.add(rootUser);

String jsonString = JSON.toJSONString(group);

System.out.println(jsonString);

// {"id":0,"name":"admin","users":[{"id":2,"name":"guest"},{"id":3,"name":"root"}]}
```

# 解码

```java
String jsonString = ...;
Group group = JSON.parseObject(jsonString, Group.class);

@Data
public class Group {

    private Long       id;
    private String     name;
    private List<User> users = new ArrayList<User>();
}

@Data
public class User {
    private Long   id;
    private String name;
}
```
