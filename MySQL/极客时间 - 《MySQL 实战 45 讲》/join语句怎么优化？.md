# Multi-Range Read 优化
大多数的数据都是按照主键递增顺序插入得到的，所以我们可以认为，如果按照主键的递增顺序查询的话，对磁盘的读比较接近顺序读，能够提升读性能
1. 根据索引 a，定位到满足条件的记录，将 id 值放入 read_rnd_buffer 中
2. 将 read_rnd_buffer 中的 id 进行递增排序
3. 排序后的 id 数组，依次到主键 id 索引中查记录，并作为结果返回
如果你想要稳定地使用 MRR 优化的话，需要设置 set optimizer_switch="mrr_cost_based=off"。（官方文档的说法，是现在的优化器策 略，判断消耗的时候，会更倾向于不使用 MRR，把 mrr_cost_based 设置为 off，就是固定 使用 MRR 了。）
