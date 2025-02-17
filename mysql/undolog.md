# 一、undo log的概念
undo log是mysql中比较重要的事务日志之一，顾名思义，undo log是一种用于撤销回退的日志，在事务没提交之前，MySQL会先记录更新前的数据到 undo log日志文件里面，当事务回滚时或者数据库崩溃时，可以利用 undo log来进行回退。
# 二、undo log的作用
在MySQL中，undo log日志的作用主要有两个：

## 1、提供回滚操作【undo log实现事务的原子性】
我们在进行数据更新操作的时候，不仅会记录redo log，还会记录undo log，如果因为某些原因导致事务回滚，那么这个时候MySQL就要执行回滚（rollback）操作，利用undo log将数据恢复到事务开始之前的状态。

如我们执行下面一条删除语句：

```mysql
delete from user where id = 1;
```
那么此时undo log会记录一条对应的insert 语句`【反向操作的语句】`，以保证在事务回滚时，将数据还原回去。

再比如我们执行一条update语句：

```mysql
update user set name = "李四" where id = 1;   # 修改之前name=张三
```
此时undo log会记录一条相反的update语句，如下：

```mysql
update user set name = "张三" where id = 1;
```
如果这个修改出现异常，可以使用undo log日志来实现回滚操作，以保证事务的一致性。

## 2、提供多版本控制(MVCC)【undo log实现多版本并发控制（MVCC）】
MVCC，即多版本控制。在MySQL数据库InnoDB存储引擎中，用undo Log来实现多版本并发控制(MVCC)。当读取的某一行被其他事务锁定时，它可以从undo log中分析出该行记录以前的数据版本是怎样的，从而让用户能够读取到当前事务操作之前的数据【快照读】。

下面解释一下什么是快照读，与之对应的还有一个是---当前读。

快照读：

SQL读取的数据是快照版本【可见版本】，也就是历史版本，不用加锁，普通的SELECT就是快照读。

当前读：

SQL读取的数据是最新版本。通过锁机制来保证读取的数据无法通过其他事务进行修改UPDATE、DELETE、INSERT、SELECT … LOCK IN SHARE MODE、SELECT … FOR UPDATE都是当前读。

# undo log的存储机制
undo log的存储由InnoDB存储引擎实现，数据保存在InnoDB的数据文 件中。在InnoDB存储引擎中，undo log是采用分段(segment)的方式进行存储的。rollback segment称为回滚段，每个回滚段中有1024个undo log segment。在MySQL5.5之前，只支持1个rollback segment，也就是只能记录1024个undo操作。在MySQL5.5之后，可以支持128个rollback segment，分别从resg slot0 - resg slot127，每一个resg slot，也就是每一个回滚段，内部由1024个undo segment 组成，即总共可以记录128 * 1024个undo操作。

下面以一张图来说明undo log日志里面到底存了哪些信息？
![[Pasted image 20241205112636.png]]


如上图，可以看到，undo log日志里面不仅存放着数据更新前的记录，还记录着RowID、事务ID、回滚指针。其中事务ID每次递增，回滚指针第一次如果是insert语句的话，回滚指针为NULL，第二次update之后的undo log的回滚指针就会指向刚刚那一条undo log日志，依次类推，就会形成一条undo log的回滚链，方便找到该条记录的历史版本。

四、undo log的工作原理
在更新数据之前，MySQL会提前生成undo log日志，当事务提交的时候，并不会立即删除undo log，因为后面可能需要进行回滚操作，要执行回滚（rollback）操作时，从缓存中读取数据。undo log日志的删除是通过通过后台purge线程进行回收处理的。

同样，通过一张图来理解undo log的工作原理。

![[Pasted image 20241205112627.png]]

如上图：

1、事务A执行update操作，此时事务还没提交，会将数据进行备份到对应的undo buffer，然后由undo buffer持久化到磁盘中的undo log文件中，此时undo log保存了未提交之前的操作日志，接着将操作的数据，也就是Teacher表的数据持久保存到InnoDB的数据文件IBD。
2、此时事务B进行查询操作，直接从undo buffer缓存中进行读取，这时事务A还没提交事务，如果要回滚（rollback）事务，是不读磁盘的，先直接从undo buffer缓存读取。
用undo log实现原子性和持久化的事务的简化过程：

假设有A、B两个数据，值分别为1,2。

A. 事务开始
B. 记录A=1到undo log中
C. 修改A=3
D. 记录B=2到undo log中
E. 修改B=4
F. 将undo log写到磁盘 -------undo log持久化
G. 将数据写到磁盘 -------数据持久化
H. 事务提交 -------提交事务
之所以能同时保证原子性和持久化，是因为以下特点：

更新数据前记录undo log。
为了保证持久性，必须将数据在事务提交前写到磁盘，只要事务成功提交，数据必然已经持久化到磁盘。
undo log必须先于数据持久化到磁盘。如果在G,H之间发生系统崩溃，undo log是完整的，可以用来回滚。
如果在A - F之间发生系统崩溃，因为数据没有持久化到磁盘，所以磁盘上的数据还是保持在事务开始前的状态。
缺陷：每个事务提交前将数据和undo log写入磁盘，这样会导致大量的磁盘IO，因此性能较差。 如果能够将数据缓存一段时间，就能减少IO提高性能，但是这样就会失去事务的持久性。

undo日志属于逻辑日志，redo是物理日志，所谓逻辑日志是undo log是记录一个操作过程，不会物理删除undo log，sql执行delete或者update操作都会记录一条undo日志。

# 五、undo log的相关参数
**innodb_undo_directory**
指定undo log日志的存储目录，默认值为./。

**innodb_undo_logs**
在MySQL5.6版本之后，可以通过此参数自定义多少个rollback segment，默认值为128。

**innodb_undo_tablespaces**
指定undo log平均分配到多少个表空间文件中，默认值为0，即全部写入一个文件中。不建议修改为非0值，我们直接使用默认值即可。

## 基本概念回顾

1. **每个事务的 Undo Log 链表**
    
    - 在事务执行过程中，对某些数据行做了修改（INSERT、UPDATE、DELETE），就会产生对应的 Undo Log 记录。
    - 这些记录形成**单向链表**（后生成的 Undo 记录指向前一个 Undo 记录），所以从最新一条 Undo 记录可以一直追溯到事务开始时产生的第一条记录。
2. **共享的 Undo Tablespace**
    
    - 逻辑上看，每个事务的 Undo Log 链是独立的；
    - 物理上看，这些 Undo Log 记录都写在一个或多个 Undo Tablespace 文件里（在较新的 MySQL 版本中可以配置多个 Undo Tablespace）。
    - 同时，在内存中会有一个**Undo Log Buffer**，用于暂存事务产生的 Undo 记录，减少频繁 I/O。
3. **写入与回滚 / MVCC**
    
    - **写入**：事务在修改数据时，会生成 Undo 记录并写入内存的 Undo Log Buffer，最终刷到 Undo Tablespace 中。
    - **回滚**：如果事务需要回滚，会顺着该事务自己的 Undo Log 链逆序恢复。
    - **MVCC**：其他事务做一致性读时，如果最新版本不可见，会通过行记录的 `roll_pointer` 找到相应的 Undo 记录，得到旧版本数据。

---

## 2. 示例场景

下面是一个简化的示例场景：我们有一张 `user` 表，里面有一些数据。现在有两个事务 T1、T2 并发执行各自的修改操作。

### 2.1 初始数据

- 表 `user` 中可能有多行，示例只展示部分：

|id|name|age|address|
|---|---|---|---|
|1|Alice|25|GZ|
|2|Bob|29|SH|
|3|Cathy|32|BJ|

### 2.2 事务 T1 的操作

4. **UPDATE**: `UPDATE user SET age = 26 WHERE id = 1;`
    
    - 原来 `id=1` 这行的 age=25，更新后变成 26。
    - T1 生成一条 Undo 记录（记为 `Undo1_T1`），保存“旧值”（age=25），以及该行的行标识等信息。
    - 该记录会暂存在 Undo Log Buffer，稍后刷到 Undo Tablespace。
5. **DELETE**: `DELETE FROM user WHERE id = 3;`
    
    - 原来 `id=3` 这行是 (Cathy, 32, BJ)，被删除。
    - T1 再生成一条 Undo 记录（记为 `Undo2_T1`），用于在回滚时能把这行“插回去”。
    - 这条 Undo 记录的 `prev` 指针指向 `Undo1_T1`，形成 T1 自己的 Undo 链表。

事务 T1 的 Undo Log 链示意（逻辑上）：

sql

复制编辑

+------------+          +------------+    
| Undo2_T1   |  -->     | Undo1_T1   |  
| (DELETE 3) |          | (UPDATE 1) |    
+------------+          +------------+`

### 2.3 事务 T2 的操作

2. **INSERT**: `INSERT INTO user (id, name, age, address) VALUES (4, 'Tom', 23, 'HZ');`
    
    - 插入一行 (4, Tom, 23, HZ)。
    - T2 生成一条 Undo 记录（记为 `Undo1_T2`），如果回滚，需要删除这条新插入的行。
3. **UPDATE**: `UPDATE user SET address = 'SZ' WHERE id = 2;`
    
    - 原来 `id=2` 这行的 address=SH，被更新成 SZ。
    - T2 再生成一条 Undo 记录（记为 `Undo2_T2`），保存旧值（address=SH），并指向 `Undo1_T2`。

事务 T2 的 Undo Log 链示意（逻辑上）：

sql

复制编辑

   `+------------+          +------------+    | Undo2_T2   |  -->     | Undo1_T2   |    | (UPDATE 2) |          | (INSERT 4) |    +------------+          +------------+`

### 2.4 两条链都写在共享的 Undo Tablespace

尽管 T1、T2 各自有自己的 Undo Log 链表，但它们最终都要刷到同一个（或多个）Undo Tablespace 文件中。在物理存储层面，可以想象是：

scss

复制编辑

`Undo Tablespace 文件(们) ： ┌───────────────────────────────┐ │  ...                          │ │  [ 页1 ] -> 包含 Undo1_T1、部分Undo1_T2 等等 │  ...                          │ │  [ 页2 ] -> 包含 Undo2_T1、Undo2_T2 │  ...                          │ │  [ 页3 ] -> 其他事务的 Undo 记录 │  ...                          │ └───────────────────────────────┘`

在这些页中，每条 Undo 记录都有一个指针指向**同一事务**的前一条 Undo 记录，形成了**逻辑上的链表**。不同事务之间的 Undo 记录也可能混合分布在同一个数据页中（具体要看分配策略和空间大小），但它们的链表指针只在同一个事务内串联。

---

## 3. Undo Log Buffer 与刷盘流程

4. **事务产生 Undo 记录时**
    
    - 先写入内存中的 `Undo Log Buffer`；
    - 同时会更新指针（本条 Undo 指向上一条 Undo），形成链表。
5. **写入 Undo Tablespace**
    
    - InnoDB 会在合适的时机（比如内存满、事务提交、或后台线程定期）将 `Undo Log Buffer` 中的内容刷新到磁盘的 Undo Tablespace。
    - 刷新时，会将 Undo 记录分配到具体的物理页面中，可能分散在不同的页。
6. **事务提交后**
    
    - Undo Log 记录并不会立即删除，因为其他长事务可能还需要用到这些旧版本做一致性读。
    - 当确定没有任何事务需要再访问某个 Undo 记录后，后台的 purge 线程才会将其物理清理或标记可复用空间。

---

## 4. 回滚与 MVCC 读取的过程

- **回滚**  
    如果 T1 在执行完上述操作后发现有错误，决定回滚，就会从 `Undo2_T1` 开始逆序遍历 T1 的 Undo Log 链表：
    
    1. 撤销 DELETE id=3（将行插回表）
    2. 撤销 UPDATE id=1（把 age=26 改回 25）  
        回滚结束后，表恢复到初始状态。
- **一致性读（MVCC）**  
    如果有另一个事务 T3 在 T1、T2 还没提交时去读 `id=1` 这一行，发现最新版本由 T1 修改，但根据 T3 的 Read View 判断 T1 还未提交，则**不可见**。InnoDB 会根据行记录的 `roll_pointer` 找到 `Undo1_T1`，从中拿到旧值（age=25）返回给 T3。
    

---

## 5. 小结

7. **是否所有事务都会写入一个 Undo Log 链表？**
    
    - 是的，**每个事务**都拥有自己独立的 Undo Log 链，用于记录本事务所做的所有修改（旧值），以支持回滚和 MVCC。
    - 但这些链表**并不**是一个文件一个链表，而是**统一写到共享的 Undo Tablespace** 中。
8. **举例说明**
    
    - 如上所示，T1 有自己的 Undo 链表，T2 也有自己的 Undo 链表；它们都混合写入同一个或多个 Undo Tablespace 文件中，最终由指针串联成各自的链。
9. **在 Undo Tablespace 中的“流动”**
    
    - 事务产生新 Undo 记录 -> 写入 Undo Log Buffer -> 刷到 Undo Tablespace 中对应的物理页面；
    - 事务提交后，Undo 记录可能暂时保留，直到不再被任何事务需要才被后台清理或标记可复用。

通过以上示例与图示，希望能更清晰地理解：

- **逻辑结构**：一个事务对应一条 Undo 链；
- **物理存储**：所有事务的 Undo 记录都存放在共享的 Undo Tablespace（多个或单个文件）中；
- **工作过程**：从生成、写入、刷盘、回滚、再到最终清理的整体流程。

这样就完整回答了“是所有事务都会写入一个undo log链表吗、以及在 undo tablespace 里的流动”的问题。