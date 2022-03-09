## 基本结构
- 负载因子0.75
- 链表长度 > 8
	- 桶数组 < 64 ：触发扩容
	- 桶数组 >= 64 ：将链表转换为红黑树
- 哈希桶数组初始化长度16
- 定位策略： 
	1. 取key的hashCode 
	2. 高位运算 
		h^h>>>16 减小因长度选用合数带来的碰撞概率
	3. 取模
		$\because$ length = $2^n$
		$\therefore$ h%length = h&(length-1)

- put方法流程
	![[DD392180979E47EFB75C915B93FDAD38.png]]
- 扩容
	- transfer方法 采用链表头插
## 多线程下问题
在1.7版本中，HashMap采用了单项链表，在扩容操作中使用了头插法，在多线程环境下可能产生循环链表
具体流程为：
![[Pasted image 20220304131915.png]]
![[Pasted image 20220304131949.png]]
![[Pasted image 20220304131956.png]]
![[Pasted image 20220304132003.png]]

&emsp;&emsp;在1.8版本中，对链表的扩容方式进行了优化，不会产生循环链表，但依旧可能造成数据覆盖的情况，造成该情况的主要原因是在进行键值对插入时，首先会根据hash值判断当前hash槽是否被使用，若没有则插入
&emsp;&emsp;&emsp;在并发状态下发生hash碰撞，对同一槽位进行插入可能造成数据覆盖，并且在计算size时，使用了++size，若执行代码前时区时间片，另一线程获取时间片并完成size+1，转回当前线程，持有的依旧是之前的size值，则会造成size偏小

## ConcurrentHashMap
1.7：在Segment持有锁

| hash | hash |hash|
| ----| ----|----|
|Entry|Entry|Entry|
|Segment|

1.8：在Node节点或TreeNode头结点持有锁

HashTable：锁表