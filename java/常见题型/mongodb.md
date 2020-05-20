按照Mongodb默认的配置，￼WiredTiger的写操作会先写入Cache，并持久化到WAL(Write ahead log)，每60s或log文件达到2GB时会做一次Checkpoint，将当前的数据持久化，产生一个新的快照。Wiredtiger连接初始化时，首先将数据恢复至最新的快照状态，然后根据WAL恢复数据，以保证存储可靠性。
![0102-zyd-MongoDB WiredTiger存储引擎实现原理-1](http://www.mongoing.com/wp-content/uploads/2016/01/0102-zyd-MongoDB-WiredTiger%E5%AD%98%E5%82%A8%E5%BC%95%E6%93%8E%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86-1-272x300.png)

Wiredtiger的Cache采用Btree的方式组织，每个Btree节点为一个page，root page是btree的根节点，internal page是btree的中间索引节点，leaf page是真正存储数据的叶子节点；btree的数据以page为单位按需从磁盘加载或写入磁盘。
![0102-zyd-MongoDB WiredTiger存储引擎实现原理-2](http://www.mongoing.com/wp-content/uploads/2016/01/0102-zyd-MongoDB-WiredTiger%E5%AD%98%E5%82%A8%E5%BC%95%E6%93%8E%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86-2-300x189.png)

Wiredtiger采用Copy on write的方式管理修改操作（insert、update、delete），修改操作会先缓存在cache里，持久化时，修改操作不会在原来的leaf page上进行，而是写入新分配的page，每次checkpoint都会产生一个新的root page。

![0102-zyd-MongoDB WiredTiger存储引擎实现原理-3](http://www.mongoing.com/wp-content/uploads/2016/01/0102-zyd-MongoDB-WiredTiger%E5%AD%98%E5%82%A8%E5%BC%95%E6%93%8E%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86-3-300x168.png)

Checkpoint时，wiredtiger需要将btree修改过的PAGE都进行持久化存储，每个btree对应磁盘上一个物理文件，btree的每个PAGE以文件里的extent形式（由文件offset + size标识）存储，一个Checkpoit包含如下元数据：

- root page地址，地址由文件offset，size及内容的checksum组成
- alloc extent list地址，存储从上次checkpoint起新分配的extent列表
- discard extent list地址，存储从上次checkpoint起丢弃的extent列表
- available extent list地址，存储可分配的extent列表，只有最新的checkpoint包含该列表
- file size 如需恢复到该checkpoint的状态，将文件truncate到file size即可