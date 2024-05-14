# @JsonCreator

@JsonCreator 注解用来调整反序列化中使用的构造函数或工厂

```java
{
    "id":1,
    "theName":"My bean"
}
```

我们的目标实体中没有 theName 字段，只有一个 name 字段。现在我们不想更改实体本身，我们只需要通过用 @JsonCreator 注释构造函数并使用 @JsonProperty 注释来对解组过程进行更多控制：

```java
public class BeanWithCreator {
    public int id;
    public String name;

    @JsonCreator
    public BeanWithCreator(
      @JsonProperty("id") int id, 
      @JsonProperty("theName") String name) {
        this.id = id;
        this.name = name;
    }
}
```

# @JacksonInject

@JacksonInject 表示属性将从注入中获取值，而不是从 JSON 数据中获取

```java
public class BeanWithInject {
    @JacksonInject
    public int id;
    
    public String name;
}
```

```java
@Test
public void whenDeserializingUsingJsonInject_thenCorrect()
  throws IOException {
 
    String json = "{\"name\":\"My bean\"}";
    
    InjectableValues inject = new InjectableValues.Std()
      .addValue(int.class, 1);
    BeanWithInject bean = new ObjectMapper().reader(inject)
      .forType(BeanWithInject.class)
      .readValue(json);
    
    assertEquals("My bean", bean.name);
    assertEquals(1, bean.id);
}
```

# @JsonAnySetter

@JsonAnySetter 在反序列化时将标准属性添加到 Map 映射中

# @JonsSetter

@JsonSetter 定义字段逻辑名，@JsonSetter 是将方法标记为 setter 方法的 @JsonProperty 的替代方法

```java
public class MyBean {
    public int id;
    private String name;

    @JsonSetter("name")
    public void setTheName(String name) {
        this.name = name;
    }
}
```

# @JsonDeserialize

@JsonDeserialize 表示使用自定义反序列化器，继承 com.fasterxml.jackson.databind.deser.std.StdDeserializer 类

```java
public class EventWithSerializer {
    public String name;

    @JsonDeserialize(using = CustomDateDeserializer.class)
    public Date eventDate;
}
```

# @JsonAlias

@JsonAlias 在反序列化期间为字段定义一个或多个备用名称

```java
public class AliasBean {
    @JsonAlias({ "fName", "f_name" })
    private String firstName;   
    private String lastName;
}
```