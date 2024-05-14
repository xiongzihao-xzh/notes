# @JsonAnyGetter

@JsonAnyGetter 注解能使 Map 字段序列化时将所有的键值作为标准普通属性获取

```java
public class User {
	
	private String name;
	private Map<String, String> properties;

	@JsonAnyGetter
	public Map<String, String> getProperties() {
		return properties;
	}

}

    @GetMapping
	public User jsonAnyGet() {
		User user = new User();
		user.setName("xzh");
		HashMap<String, String> properties = new HashMap<>();
		properties.put("prop1", "v1");
		properties.put("prop2", "v2");
		user.setProperties(properties);
		
		return user;
	}

// result:
//
// {
//   "name": "xzh",
//   "prop2": "v2",
//   "prop1": "v1"
// }
```

# @JsonGetter

@JsonGetter 定义字段逻辑访问名称，@JsonGetter 是 @JsonProperty 的替代方法

```java
public class User {
	
	private String name;
	private Map<String, String> properties;

	@JsonAnyGetter
	public Map<String, String> getProperties() {
		return properties;
	}

	@JsonGetter("username")
	public String getName() {
		return name;
	}

}

	@GetMapping
	public User jsonAnyGet() {
		User user = new User();
		user.setName("xzh");
		HashMap<String, String> properties = new HashMap<>();
		properties.put("prop1", "v1");
		properties.put("prop2", "v2");
		user.setProperties(properties);
		
		return user;
	}

// result
//
// {
//   "username": "xzh",
//   "prop2": "v2",
//   "prop1": "v1"
// }
```

# @JsonPropertyOrder

@JsonPropertyOrder 序列化时指定属性顺序，@JsonPropertyOrder（alphabetic=true） 按字母顺序对属性进行排序

# @JsonRawValue

@JasonRawValue 注解可以指示字段按原样序列化

```java
public class User {

	private String a;
	private String b;
	private String name;
	private Map<String, String> properties;
	@JsonRawValue
	private String jsonStr;

	@JsonAnyGetter
	public Map<String, String> getProperties() {
		return properties;
	}

	@JsonGetter("username")
	public String getName() {
		return name;
	}

}

    @GetMapping
	public User jsonAnyGet() {
		User user = new User();
		user.setName("xzh");
		HashMap<String, String> properties = new HashMap<>();
		properties.put("prop1", "v1");
		properties.put("prop2", "v2");
		user.setProperties(properties);
		user.setJsonStr("{\"attr1\":\"v1\",\"attr2\":\"v2\"}");
				
		return user;
	}

// result
//
// {
//   "username": "xzh",
//   "b": null,
//   "a": null,
//   "jsonStr": {
//     "attr1": "v1",
//     "attr2": "v2"
//   },
//   "prop2": "v2",
//   "prop1": "v1"
// }
```

# @JsonValue

@JsonValue 用于序列化实例的单个方法，一个类中存在多个 @JsonValue 注解时会抛出异常

# @JsonRootName

@JsonRootName 注解指定名称用来包装序列化实例

```java
@JsonRootName("rootUser")
public class User{
    // ...
}

// result: 
// 
// {
// 	 "rootUser": {//...}
// }
```

使用 @JsonRootName 需要设置 ObjectMapper#enable(SerializationFeature.WRAP_ROOT_VALUE);

# @JsonSerialize

@JsonSerialize 指定实例使用自定义序列化器，继承 com.fasterxml.jackson.databind.ser.std.StdSerializer 类

```java
public class EventWithSerializer {
    public String name;

    @JsonSerialize(using = CustomDateSerializer.class)
    public Date eventDate;
}
```