# @JsonIgnoreProperties

@JsonIgnoreProperites 是一个类级注解，用于标记 Jackson 将忽略的属性或属性列表（包括序列化和反序列化）

```java
@JsonRootName("rootUser")
@JsonPropertyOrder({"username", "b", "a"})
@JsonIgnoreProperties({"a","b"})
@Data
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

// result:
//
// {
//   "username": "xzh",
//   "jsonStr": {
//     "attr1": "v1",
//     "attr2": "v2"
//   },
//   "prop2": "v2",
//   "prop1": "v1"
// }
```

# @JonsIgnore

@JsonIgnore 注解用于在字段级别标记要忽略的属性

# @JsonIgnoreType

@JsonIgnoreType 这个注解用在类上表示这个类在序列化和反序列化的时候会被忽略

# @JsonInclude

@JsonInclude 可以用来排除 empty / null / default values

# @JsonIncludeProperties

@JsonIncludeProperties 用于标记 Jackson 在序列化和反序列化期间将包含的属性或属性列表

# @JsonAutoDetect

@JsonAutoDetect 可以覆盖哪些属性可见，哪些属性不可见的默认语义

```java
@JsonAutoDetect(fieldVisibility = Visibility.ANY)
public class PrivateBean {
    private int id;
    private String name;
}
```