# STL 实现

#### 1.map & unordered_map

 		对于map的底层原理，是通过红黑树（一种非严格意义上的平衡二叉树）来实现的，因此map内部所有的数据都是有序的，map的查询、插入、删除操作的时间复杂度都是O(logn)。此外，map的key需要定义operator <，对于一般的数据类型已被系统实现，若是用户自定义的数据类型，则要重新定义该操作符。
		unordered_map和map类似，都是存储的key-value的值，可以通过key快速索引到value。不同的是unordered_map不会根据key的大小进行排序，存储时是根据key的hash值判断元素是否相同，即unordered_map内部元素是无序的。unordered_map的底层是一个防冗余的哈希表（开链法避免地址冲突）。unordered_map的key需要定义hash_value函数并且重载operator ==。

​		哈希表最大的优点，就是把数据的存储和查找消耗的时间大大降低，时间复杂度为O(1)；而代价仅仅是消耗比较多的内存。哈希表的查询时间虽然是O(1)，但是并不是unordered_map查询时间一定比map短，因为实际情况中还要考虑到数据量，而且unordered_map的hash函数的构造速度也没那么快，所以不能一概而论，应该具体情况具体分析。
