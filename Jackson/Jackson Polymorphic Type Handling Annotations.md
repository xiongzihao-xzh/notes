- jackson允许配置多态类型处理，当进行反序列话时，JSON数据匹配的对象可能有多个子类型，为了正确的读取对象的类型，我们需要添加一些类型信息。可以通过下面几个注解来实现：
- @JsonTypeInfo  
    作用于类/接口，被用来开启多态类型处理，对基类/接口和子类/实现类都有效
- @JsonTypeInfo(use = JsonTypeInfo.Id.NAME,include = JsonTypeInfo.As.PROPERTY,property = "name")  
    这个注解有一些属性:
- use:定义使用哪一种类型识别码，它有下面几个可选值：

- JsonTypeInfo.Id.CLASS：使用完全限定类名做识别
- JsonTypeInfo.Id.MINIMAL_CLASS：若基类和子类在同一包类，使用类名(忽略包名)作为识别码
- JsonTypeInfo.Id.NAME：一个合乎逻辑的指定名称
- JsonTypeInfo.Id.CUSTOM：自定义识别码，由@JsonTypeIdResolver对应，稍后解释
- JsonTypeInfo.Id.NONE：不使用识别码

- include(可选):指定识别码是如何被包含进去的，它有下面几个可选值：

- JsonTypeInfo.As.PROPERTY：作为数据的兄弟属性
- JsonTypeInfo.As.EXISTING_PROPERTY：作为POJO中已经存在的属性
- JsonTypeInfo.As.EXTERNAL_PROPERTY：作为扩展属性
- JsonTypeInfo.As.WRAPPER_OBJECT：作为一个包装的对象
- JsonTypeInfo.As.WRAPPER_ARRAY：作为一个包装的数组

- property(可选):制定识别码的属性名称  
    此属性只有当:

- use为JsonTypeInfo.Id.CLASS（若不指定property则默认为@class）、JsonTypeInfo.Id.MINIMAL_CLASS(若不指定property则默认为@c)、JsonTypeInfo.Id.NAME(若不指定property默认为@type)，
- include为JsonTypeInfo.As.PROPERTY、JsonTypeInfo.As.EXISTING_PROPERTY、JsonTypeInfo.As.EXTERNAL_PROPERTY时才有效

- defaultImpl(可选)：如果类型识别码不存在或者无效，可以使用该属性来制定反序列化时使用的默认类型
- visible(可选，默认为false)：是否可见  
    属性定义了类型标识符的值是否会通过JSON流成为反序列化器的一部分，默认为fale,也就是说,jackson会从JSON内容中处理和删除类型标识符再传递给JsonDeserializer。
- @JsonSubTypes  
    作用于类/接口，用来列出给定类的子类，只有当子类类型无法被检测到时才会使用它,一般是配合@JsonTypeInfo在基类上使用，比如：

```java
@JsonTypeInfo(use = JsonTypeInfo.Id.NAME,include = JsonTypeInfo.As.PROPERTY,property = "typeName")  
@JsonSubTypes({@JsonSubTypes.Type(value=Sub1.class,name = "sub1"),@JsonSubTypes.Type(value=Sub2.class,name = "sub2")})  
```

@JsonSubTypes的值是一个@JsonSubTypes.Type[]数组，里面枚举了多态类型(value对应子类)和类型的标识符值(name对应@JsonTypeInfo中的property标识名称的值，此为可选值，若不制定需由@JsonTypeName在子类上制定)

- @JsonTypeName  
    作用于子类，用来为多态子类指定类型标识符的值  
    比如：

```java
@JsonTypeName(value = "sub1")  
```

# 参考

[https://www.jianshu.com/p/a21f1633d79c](https://www.jianshu.com/p/a21f1633d79c)