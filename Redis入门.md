# 数据类型

## String

- string 类型是二进制安全的。意思是 redis 的 string 可以包含任何数据。比如jpg图片或者序列化的对象。

- string 类型是 Redis 最基本的数据类型，string 类型的值最大能存储 512MB。

## Hash

- hash 特别适合用于存储对象
- HMSET 设置了 **field=>value** 对, HGET 获取对应 **field** 对应的 **value**。
- key field value

## List

## Set

- 集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)。
- 添加一个 string 元素到 key 对应的 set 集合中，成功返回 1，如果元素已经在集合中返回 0。

## Zset

- Redis zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。

- 不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。

- 若member已经存在，就会更新member的score

## HyperLogLog

- Redis HyperLogLog 是用来做基数统计的算法，HyperLogLog 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定 的、并且是很小的。

- 在 Redis 里面，每个 HyperLogLog 键只需要花费 12 KB 内存，就可以计算接近 2^64 个不同元素的基数。这和计算基数时，元素越多耗费内存就越多的集合形成鲜明对比。

- 但是，因为 HyperLogLog 只会根据输入元素来计算基数，而不会储存输入元素本身，所以 HyperLogLog 不能像集合那样，返回输入的各个元素。
- 计算基数有什么用？？
  - 统计？？

# Redis发布订阅

# Redis事务

- 不同于数据库中的事务，redis中的事务更像是一个批量执行的脚本。
- 中间某条指令的失败不会导致前面已做指令的回滚，也不会造成后续的指令不做。