# 索引

## 1. 索引结构推导

1. 使用hash索引，快， O(1)。 但是无法解决范围查找问题
2. 使用数组做索引，快，O(1)。 但是插入删除很慢， 需要移动 O(N)元素。
3. 使用链表做索引，慢，O(N)。 需要遍历来查找元素。
4. 使用跳表做索引（链表加索引），索引层级会比较多
5. 在 查询 和 插入删除 之间取平衡，想到 树 结构。
   1. 查找效率 O(lgN)
   2. 插入删除效率 O(lgN)
6. 树的高度直接决定了查询效率，所以希望使树尽量矮。 
   1. 中间结点不要存具体data，只存索引，这样每个中间结点就能存更多的索引数据，树就更矮
7. 要解决范围查找问题
   1. 将叶子结点的数据，全部用双向链表连接起来
8. 一般建议2KW数据，[依据](https://juejin.cn/post/7116381265300815903)， 一般实际场景，B+树中间结点的出度（fanout）为1280， 所以只要3层左右，就能轻松存在2kw数据了。

## 2. 索引类型

1. 聚集索引 ( clustered-index )：叶子结点挂着真实的行数据 （数据堆在叶子结点，所以叫聚簇，cluster）。 

   1. innoDB 按照主键聚集，如果建表时没有指定，innoDB会使用UK作为主键来聚集，如果UK也没有，innorDB会创建隐含的自增主键。

      

2. 非聚集索引(non-clustered-index, secondary-index)。 叶子结点索引的是 主键值

   1. 非聚集索引，需要回表一次。因为真实row都在聚集索引的B+树上

      ​			<img src="Mysql.assets/image-20220525172533070.png" alt="image-20220525172533070" style="zoom:50%;" />
   
      <img src="Mysql.assets/image-20220525172543904.png" alt="image-20220525172543904" style="zoom:50%;" />
   
   2. 由于其他的索引叶子结点 挂的都是 primary-key。 所以primary-key不建议用太大的值（如使用UUID是不建议的，太长了）， 否则的话其他索引将会占用比较大的空间
   
      
   
3. 联合索引（Composite Indexes ）  [好文章-联合索引](https://juejin.cn/post/6876046792056635405)

   1. 联合索引最左匹配，建一个联合索引 `(col1,col2,col3)`，实际相当于建了 `(col1)`，`(col1,col2)`，`(col1,col2,col3)` 三个索引。
   2. 达到 **覆盖索引** 的效果，比如上面的索引，如果查询的就是 col1,2,3 的数据，则只查索引结构即可，不需要再回表查询。
   3. 一般情况下，将查询需求频繁或者字段选择性高的列放在前面
   4. 索引列的前缀，和联合索引最左匹配 是两个概念， 索引列前缀指对于特定的列，With col_name(N) syntax in an index specification for a string column, you can create an index that uses only the first N characters of the column。

   

4. 覆盖索引(Covering Indexes)

   1. 包含满足查询的所有列。只访问索引的查询，只需读索引而不用读数据。（避免回表，提高性能）



## 3. 索引八股

1. [清晰概念](https://mp.weixin.qq.com/s?__biz=Mzg3NjU3OTQyMA==&mid=2247483750&idx=1&sn=006f399e2cd868e3bbc1ab7e0cd4e701&chksm=cf315e55f846d743bf1839f6f27aafac88c2d611adbaff4fb8831922f8be70a27decd85749a5&token=138873163&lang=zh_CN#rd)
2. 深分页问题。
   1. 会去扫 limit + offset 的所有数据，然后再取offset条数据。 这个归功于mysql的 “early row lookup” 问题，会过早地去查row。 [参考 stackoverflow](https://stackoverflow.com/questions/4481388/why-does-mysql-higher-limit-offset-slow-the-query-down) 
3. 





# 事务

## 1. 事务的隔离级别

![image-20220603123813080](Mysql.assets/image-20220603123813080.png)

1. 上图只是规范，如RR隔离级别不解决幻读，但是实际实现是看数据库厂商的，例如Mysql默认隔离级别RR，且解决了幻读。
2. **不可重复读 VS. 幻读（教科书式定义）：**
   1. 不可重复读：读期间记录被 <u>更新</u>了， 导致前后两次读取的数据不一样。A non-repeatable read occurs, when during the course of a transaction, a row is retrieved twice and the values within the row differ between reads. 
   2. 幻读：读期间，有记录被 <u>插入或删除</u>， 导致前后读出的记录数不一样。（记录没有被修改，还是能重复读的，只不过是多了或者少了！）A phantom read occurs when, in the course of a transaction, two identical queries are executed, and the collection of rows returned by the second query is different from the first.
3. 



# 分库分表

## 分库分表要考虑的一些问题

1. 唯一主键（此例子中将主键作为user_id）

   > 1. 每个库主键自增会冲突，使用全局唯一的主键。 每次请求中心获取id太耗费性能，优化为一次获取一段范围的id，用完之后再继续获取。每个库存一张 sequence表。 TDDL中间件保证各个库中sequence全局唯一。
   > 2. 使用全局唯一生成方案，如snowflake。

2. 唯一主键确定之后，数据生成后落到哪个库哪张表？

   > 例如分了256张表，则使用 hash取模 256 来唯一确定数据路由到哪张表。 则此时 user_id 就是我们的 sharding-key。

3. 对于findByUserId 可以方便的找到对应的表去查询。 对于findByEmail，怎么查？

   > 1. 额外索引。空间换时间，多保存一份从 emial 到 user_id 的索引表。 该索引表以email作为sharding-key
   >
   >    该索引表可以是主链路上插入，也可以用旁路插入方案，精卫监听binlog，异步插入异构索引表。
   >
   > 2. 基因法。 将login_name_hash 的最后4位（如果是16张表的话）融入到 user_id中， 这样 login_name_hash 直接 %16  ===  user_id %16 值是一样的，直接路由就行。 缺点是只能满足两种key的查找。

4. 事务问题

   > 分布式事务方案。  XA协议、两阶段提交、事务补偿	

5. 跨库join查询

   > 1. 全局表。 例如一些维度表，可以每个库中都放一张全量的维度表，直接本库join
   > 2. 字段冗余，例如订单分表后，有userId字段，同时也将userName，age等字段保存，避免去join user表
   > 3. 应用层组装数据，不使用数据库的join

6. 分页排序

   > [好文-分页方案](https://zhuanlan.zhihu.com/p/468309538)
   >
   > 变成多个库后，分库依据是user_id，排序依据是time，数据库层失去了time排序的全局视野。
   >
   > 1. 例如找第5页，每页10条数据， 则各个表的前5页全部捞取，然后内存全局视野排序。（低效）
   > 2. 业务折中1，只允许下一页，每次拿到一页，记录改页的max值，然后找下一页时，从max值往后找10条数据即可，然后内存全局视野排序
   > 3. 业务折中2，允许精度损失，假设数据均匀分布。 则直接平均切分然取数据
   > 4. 终极方案，二次改写。先数据均分查，然后对结果去最小、最大值再去捞，捞出来的数再作为全局视角去查找。

1. 分库分表中间件

   > 阿里Cobar、TDDL； 开源MyCat
   >
   > 基本原理：
