关于 Date 类，我们要有两点认识：
1. 一是，Date 并无时区问题，世界上任何一台计算机使用 new Date() 初始化得到的时间都一样。因为，Date 中保存的是 UTC 时间，UTC 是以原子钟为基础的统一时间，不以太阳参照计时，并无时区划分
2. 二是，Date 中保存的是一个时间戳，代表的是从 1970 年 1 月 1 日 0 点（Epoch 时间）到现在的毫秒数
Java 8 推出了新的时间日期类 ZoneId、ZoneOffset、LocalDateTime、ZonedDateTime 和 DateTimeFormatter，处理时区问题更简单清晰：
* 首先初始化上海、纽约和东京三个时区。我们可以使用 ZoneId.of 来初始化一个标准的时区，也可以使用 ZoneOffset.ofHours 通过一个 offset，来初始化一个具有指定时间差的自定义时区
* 对于日期时间表示，LocalDateTime 不带有时区属性，所以命名为本地时区的日期时间；而 ZonedDateTime=LocalDateTime+ZoneId，具有时区属性。因此，LocalDateTime 只能认为是一个时间表示，ZonedDateTime 才是一个有效的时间。在这里我们把 2020-01-02 22:00:00 这个时间表示，使用东京时区来解析得到一个 ZonedDateTime
* 对于日期时间表示，LocalDateTime 不带有时区属性，所以命名为本地时区的日期时间；而 ZonedDateTime=LocalDateTime+ZoneId，具有时区属性。因此，LocalDateTime 只能认为是一个时间表示，ZonedDateTime 才是一个有效的时间。在这里我们把 2020-01-02 22:00:00 这个时间表示，使用东京时区来解析得到一个 ZonedDateTime
* 使用 DateTimeFormatter 格式化时间的时候，可以直接通过 withZone 方法直接设置格式化使用的时区
```java
//一个时间表示
String stringDate = "2020-01-02 22:00:00";
//初始化三个时区
ZoneId timeZoneSH = ZoneId.of("Asia/Shanghai");
ZoneId timeZoneNY = ZoneId.of("America/New_York");
ZoneId timeZoneJST = ZoneOffset.ofHours(9);
//格式化器
DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
ZonedDateTime date = ZonedDateTime.of(LocalDateTime.parse(stringDate, dateTimeFormatter), timeZoneJST);
//使用DateTimeFormatter格式化时间，可以通过withZone方法直接设置格式化使用的时区
DateTimeFormatter outputFormat = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss Z");
System.out.println(timeZoneSH.getId() + outputFormat.withZone(timeZoneSH).format(date));
System.out.println(timeZoneNY.getId() + outputFormat.withZone(timeZoneNY).format(date));
System.out.println(timeZoneJST.getId() + outputFormat.withZone(timeZoneJST).format(date));
```
![[Java 日期时间类型 20240514004150.png]]
这里有个误区是，认为 java.util.Date 类似于新 API 中的 LocalDateTime。其实不是，虽然它们都没有时区概念，但 java.util.Date 类是因为使用 UTC 表示，所以没有时区概念，其本质是时间戳；而 LocalDateTime，严格上可以认为是一个日期时间的表示，而不是一个时间点
因此，在把 Date 转换为 LocalDateTime 的时候，需要通过 Date 的 toInstant 方法得到一个 UTC 时间戳进行转换，并需要提供当前的时区，这样才能把 UTC 时间转换为本地日期时间（的表示）。反过来，把 LocalDateTime 的时间表示转换为 Date 时，也需要提供时区，用于指定是哪个时区的时间表示，也就是先通过 atZone 方法把 LocalDateTime 转换为 ZonedDateTime，然后才能获得 UTC 时间戳
```java
Date in = new Date();
LocalDateTime ldt = LocalDateTime.ofInstant(in.toInstant(), ZoneId.systemDefault());
Date out = Date.from(ldt.atZone(ZoneId.systemDefault()).toInstant());
```

