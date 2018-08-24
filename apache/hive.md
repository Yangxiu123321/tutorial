# Hadoop数据仓库工具：Hive

## 引入

### 数据仓库

#### 概念

数据仓库，英文名称为Data Warehouse，可简写为DW或DWH。数据仓库，是为企业所有级别的决策制定过程，提供所有类型数据支持的战略集合。它是单个数据存储，出于分析性报告和决策支持目的而创建。为需要业务智能的企业，提供指导业务流程改进、监视时间、成本、质量以及控制。

#### 特征

- 面向主题的：传统数据库主要是为应用程序进行数据处理，未必按照同一主题存储数据;数据仓库侧重于数据分析工作，是按照主题存储的。这一点，类似于传统农贸市场与超市的区别—市场里面，白菜、萝卜、香菜会在一个摊位上，如果它们是一个小贩卖的;而超市里，白菜、萝卜、香菜则各自一块。也就是说，市场里的菜(数据)是按照小贩(应用程序)归堆(存储)的，超市里面则是按照菜的类型(同主题)归堆的。
- 与时间相关：数据库保存信息的时候，并不强调一定有时间信息。数据仓库则不同，出于决策的需要，数据仓库中的数据都要标明时间属性。决策中，时间属性很重要。同样都是累计购买过九车产品的顾客，一位是最近三个月购买九车，一位是最近一年从未买过，这对于决策者意义是不同的。
- 不可修改：数据仓库中的数据并不是最新的，而是来源于其它数据源。数据仓库反映的是历史信息，并不是很多数据库处理的那种日常事务数据(有的数据库例如电信计费数据库甚至处理实时信息)。因此，数据仓库中的数据是极少或根本不修改的；当然，向数据仓库添加数据是允许的。

#### 与传统数据库的差异

- 数据库一般存储在线交易数据，数据仓库存储的一般是历史数据。
- 数据库设计是尽量避免冗余，一般采用符合范式的规则来设计，数据仓库在设计是有意引入冗余，采用反范式的方式来设计。
- 数据库是为捕获数据而设计，数据仓库是为分析数据而设计，它的两个基本的元素是维表和事实表。维是看问题的角度，比如时间，部门，维表放的就是这些东西的定义，事实表里放着要查询的数据，同时有维的ID。

#### 优点

- 效率足够高：要求的分析数据具有一定的时效性。
- 数据质量：保证数据在分析过程中不会失真。
- 扩展性：保证在一定时限内都能稳定运行。

### Hive

#### 简介

- Hive是一个构建于Hadoop顶层的数据仓库工具。
- 支持大规模数据存储、分析，具有良好的可扩展性。
- 某种程度上可以看作是用户编程接口，本身不存储和处理数据。
- 依赖分布式文件系统HDFS存储数据。
- 依赖分布式并行计算模型MapReduce处理数据。
- 定义了简单的类似SQL 的查询语言——HiveQL。
- 用户可以通过编写的HiveQL语句运行MapReduce任务。
- 可以很容易把原来构建在关系数据库上的数据仓库应用程序移植到Hadoop平台上。
- 是一个可以提供有效、合理、直观组织和使用数据的分析工具。

#### 特点

- 采用批处理方式处理海量数据。
- 提供数据仓库操作的工具。
- 通过类SQL来分析大数据，而避免了写MapReduce Java程序来分析数据，这样使得分析数据更容易。
- 数据是存储在HDFS上的，Hive本身并不提供数据的存储功能。
- Hive是将数据映射成数据库和一张张的表，库和表的元数据信息一般存在关系型数据库上（比如 MySQL）。
- 数据存储方面：Hive能够存储很大的数据集，并且对数据完整性、格式要求并不严格。
- 数据处理方面：Hive不适用于实时计算和响应，使用于离线分析。

#### 在Hadoop生态环境中的地位

- 依赖于HDFS存储数据。
- 依赖于MapReduce处理数据。
- 某些场景下Pig可以作为Hive的替代工具。
- 依赖于HBase提供数据的实时访问。

#### 与传统数据库的区别

| 对比项目 | Hive         | 传统数据库         |
| -------- | ------------ | ------------------ |
| 数据插入 | 支持批量导入 | 支持单条和批量导入 |
| 数据更新 | 不支持       | 支持               |
| 索引     | 支持         | 支持               |
| 分区     | 支持         | 支持               |
| 执行延迟 | 高           | 低                 |
| 扩展性   | 好           | 有限               |

## Hive的系统架构

与Hadoop的HDFS和MapReduce计算框架不同，Hive并不是分布式架构，它独立于集群之外，可以看做一个Hadoop的客户端。我们可以通过CLI（命令行接口）、Web GUI（Web接口）以及Thrift Server提供的JDBC或ODBC方式访问Hive，其中最常用的是命令行接口。用户通过以上方式向Hive提交查询命令，命令进入Driver模块后进行解释和编译，SQL优化，然后生成执行计划，执行计划将查询分解为若干个MapReduce作业。

## Hive的工作过程

1. 由Hive驱动模块中的编译器对用户输入的HQL进行词法和语法解析，将HQL语句转化为抽象语法树的形式。
2. 抽象语法树的结构仍很复杂，不方便直接翻译为MapReduce算法程序，因此，把抽象语法书转化为查询块。
3. 把查询块转换成逻辑查询计划，里面包含了许多逻辑操作符。
4. 重写逻辑查询计划，进行优化，合并多余操作，减少MapReduce任务数量。
5. 将逻辑操作符转换成需要执行的具体MapReduce任务。
6. 对生成的MapReduce任务进行优化，生成最终的MapReduce任务执行计划。
7. 由Hive驱动模块中的执行器，对最终的MapReduce任务进行执行输出。

## 存储

### 数据类型

#### 基本类型

| 类型      | 示例         | 描述                              |
| --------- | ------------ | --------------------------------- |
| boolean   | true         | true/false                        |
| tinyint   | -128~127     | 1字节的有符号整数                 |
| smallint  | 12s          | 2个字节的有符号整数，-32768~32767 |
| int       | 1            | 4字节的有符号整数                 |
| bigint    | 1L           | 8字节的有符号整数                 |
| float     | 1.0          | 4字节单精度浮点数                 |
| double    | 1.0          | 8字节双精度浮点数                 |
| deicimal  | 1.0          | 任意精度的带符号小数              |
| string    | "ab", 'c'    | 变长字符串                        |
| varchar   | "ab", 'c'    | 变长字符串                        |
| char      | "ab", 'c'    | 定长字符串                        |
| binary    | ——         | 字节数组                          |
| timestamp | 122347489987 | 时间戳，精确到纳秒                |
| date      | 1993-08-12   | 日期                              |

和其他的SQL语言一样，这些都是保留字。需要注意的是所有的这些数据类型都是对Java中接口的实现，因此这些类型的具体行为细节和Java中对应的类型是完全一致的。例如，string类型实现的是Java中的String，float实现的是Java中的float，等等。

#### 复杂类型

| 类型   | 示例                                         | 描述                                              |
| ------ | -------------------------------------------- | ------------------------------------------------- |
| array  | array(1, 2)                                  | 有序的同类型集合                                  |
| map    | map('a',1,'b',2)                             | Key-Value组合，Key必须为原始类型，Value可任意类型 |
| struct | named_stract('col1','1','col2',1,'clo3',1.0) | 字段集合，类型可以不同                            |

### 存储格式

Hive会为每个创建的数据库在HDFS上创建一个目录，该数据库的表会以子目录形式存储，表中的数据会以表目录下的文件形式存储。对于default数据库，默认的缺省数据库没有自己的目录，default数据库下的表默认存放在/user/hive/warehouse目录下。

- **textfile**为默认格式，存储方式为行存储。数据不做压缩，磁盘开销大，数据解析开销大。
- **SequenceFile**是Hadoop API提供的一种二进制文件支持，其具有使用方便、可分割、可压缩的特点。支持三种压缩选择：NONE, RECORD, BLOCK。 Record压缩率低，一般建议使用BLOCK压缩。
- **RCFile**是一种行列存储相结合的存储方式。
- **ORCFile**数据按照行分块，每个块按照列存储，其中每个块都存储有一个索引。hive给出的新格式，属于RCFILE的升级版，性能有大幅度提升，而且数据可以压缩存储，压缩快，快速列存取。
- **Parquet**也是一种行式存储，同时具有很好的压缩性能；同时可以减少大量的表扫描和反序列化的时间。

### 数据格式

当数据存储在文本文件中，必须按照一定格式区别行和列，并且在Hive中指明这些区分符。Hive默认使用了几个平时很少出现的字符，这些字符一般不会作为内容出现在记录中。Hive默认的行和列分隔符如下表所示：

| 分割符 | 描述                                                                  |
| ------ | --------------------------------------------------------------------- |
| \n     | 对于文本文件来说，每行是一条记录，所以\n来分割记录。                  |
| \001   | 分割字段                                                              |
| \002   | 用于分割 Arrary 或者 Struct 中的元素，或者用于 map 中键值之间的分割。 |
| \003   | 用于map中键和值之间的分割。                                           |

用户也可以根据需要在建表时自定义分隔符，例如`row format delimited fields terminated by '\t'`指定了列分隔符为'\t'，也就是tab键（制表符）。

## 安装与配置

详见[Ubuntu16.04环境下安装配置Hive2.3.3](./installing-hive2.3.3-on-ubuntu.md)