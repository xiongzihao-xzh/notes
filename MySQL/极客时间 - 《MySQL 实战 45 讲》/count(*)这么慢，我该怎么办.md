# count(\*) 实现方式
在不同的 MySQL 引擎中，count(\*) 有不同的实现方式：
1. MyISAM 引擎把一个表的总行数存在了磁盘上，因此执行 count(\*) 的时候会直接返回这个 数，效率很高
2. 而 InnoDB 引擎就麻烦了，它执行 count(\*) 的时候，需要把数据一行一行地从引擎里面读 出来，然后累积计数
InnoDB 是索引组织表，主键索引树的叶子节点是数据，而普通索引树的叶子节点 是主键值。所以，普通索引树比主键索引树小很多。对于 count(\*) 这样的操作，遍历哪个索引树得到的结果逻辑上都是一样的。因此，MySQL 优化器会找到最小的那棵树来遍历。在保 证逻辑正确的前提下，尽量减少扫描的数据量，是数据库系统设计的通用法则之一
# 不同的 count 用法
count() 是一个聚合函数，对于返回的结果集，一行行地判断，如果 count 函数的参数不是 NULL，累计值就加 1，否则不加。最后返回累计值
**对于 count(主键 id) 来说**，InnoDB 引擎会遍历整张表，把每一行的 id 值都取出来，返回给 server 层。server 层拿到 id 后，判断是不可能为空的，就按行累加
**对于 count(1) 来说**，InnoDB 引擎遍历整张表，但不取值。server 层对于返回的每一行，放 一个数字“1”进去，判断是不可能为空的，按行累加
**对于 count(字段) 来说**：
1. 如果这个“字段”是定义为 not null 的话，一行行地从记录里面读出这个字段，判断不能 为 null，按行累加
2. 如果这个“字段”定义允许为 null，那么执行的时候，判断到有可能是 null，还要把值取 出来再判断一下，不是 null 才累加
**count(\*) 是例外**，并不会把全部字段取出来，而是专门做了优化，不取值。count(\*) 肯 定不是 null，按行累加
**所以结论是：按照效率排序的话，count(字段)<count(主键 id)<count(1)≈count(\*)，所以我建议你，尽量使用 count(\*)**
