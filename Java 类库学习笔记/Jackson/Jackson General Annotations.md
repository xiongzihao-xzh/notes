# @JsonProperty

@JsonProperty 指定序列化和反序列化属性名称

# @JsonFormat

@JsonFormat 指定序列化日期和时间的格式

```java
public class EventWithFormat {
    public String name;

    @JsonFormat(
      shape = JsonFormat.Shape.STRING,
      pattern = "dd-MM-yyyy hh:mm:ss")
    public Date eventDate;
}
```

# @JsonUnwrapped

@JsonUnwrapped 用于序列化或反序列化时 **unwrapped** 属性

# @JsonView

@JsonView 指示将包含属性以进行序列化/反序列化的视图

```java
public class Views {
    public static class Public {}
    public static class Internal extends Public {}
}

public class Item {
    @JsonView(Views.Public.class)
    public int id;

    @JsonView(Views.Public.class)
    public String itemName;

    @JsonView(Views.Internal.class)
    public String ownerName;
}
```

# @JsonFilter

@JsonFilter 批注指定在序列化期间使用的筛选器