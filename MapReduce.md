## MapReduce大规模数据并行处理

### PPT复习

#### Ch1 大数据技术简介

* 大数据的类型
  * 结构特征
    * 结构化数据
    * 非结构化/半结构化数据
  * 获取和处理方式
    * 静态（线下数据），非实时数据
    * 动态（流式、增量式、线上），实时数据
  * 关联特征
    * 无关联/简单关联数据（键值记录型数据）
    * 复杂关联数据（图数据）
* 大数据研究的基本途径
  * 降低尺度：寻找数据尺度无关近似算法
  * 新算法：寻找新算法降低复杂度
  * 并行化：分而治之，并行化处理
* 大数据处理的主要技术问题
  * 数据存储、数据计算、数据分析处理
    * 数据的存储能力和计算能力落后于数据的增长速度
    * 数据规模导致传统算法失效，复杂的数据关联性导致高复杂度的计算
    * 大数据隐含很多小数据时难以发现的深度价值



------

#### Ch2 MapReduce简介

* 为什么需要大规模数据并行处理
  * 处理数据的能力大幅落后于数据增长
  * 海量数据隐含着更准确的事实

* **什么是Mapreduce？（三层含义）**
  * **基于集群的高性能并行计算平台**
  * **并行程序开发与运行框架**
  * **并行程序设计模型与方法**
* 为什么MapReduce如此重要？
  * 高效的大规模数据处理方法
  * 改变了大规模尺度上组织计算的方式
  * 第一个不同于冯诺依曼结构的、基于集群而非单机的计算方式的 重大突破
  * 目前为止最为成功的基于大规模计算资源的并行计算抽象方法
* **Mapreduce的基本处理方法（基本模型与处理思想）**
  * 对付大数据处理：**分而治之**
  * 上升到抽象模型：Map和Reduce
    * **用Map和Reduce两个函数提供了高层的并行编程抽象模型**
    * **提供一种抽象机制，把做什么和怎么做分开，程序员仅需要描述做什么，不需要关心怎么做**
* 上升到架构：**自动并行化并为程序员隐藏系统层细节**
* master，worker
  * master：负责划分和分配任务
  * worker：负责数据块的计算
* map，reduce
  * Map：对一组数据元素进行某种重复式的处理
  * Reduce：对Map的中间结果进行某种进一步的结果整理
* **Combiner，Partitioner**
  * **Combiner：将Map输出的具有相同主键的所有键值对合并为一个键值对，带宽优化，减少通信开销**
    * 是在本地执行的一个Reducer
    * **概括：带宽优化**
  * **Partitioner：数据分区，将数据分配到合适的Reduce节点，避免不同Reduce节点上数据的相关性**
    * 一个reduce节点所处理的数据可能会来自多个map节点，因此，map节点输出的中间结果需要使用一定的策略进行适当的划分（Partitioner）处理，保证相关数据发送到同一个reduce节点。
    * 决定把给定的键值对传给哪一个Reduce节点
    * **概括：用数据分区解决数据相关性问题**
* **Mapreduce的主要设计思想与技术特点**
  * 向“外”横向扩展，而非向“上”纵向扩展
  * 失效被认为是常态
  * 把处理向数据迁移
  * 顺序处理数据，避免随机访问数据
  * 为应用开发者隐藏系统层细节
  * 平滑无缝的可扩展性
    * 主要包括数据扩展和系统规模扩展
* MapReduce提供统一的构架并完成以下的主要功能
  * 任务调度：为这些划分后的计算任务分配和调度计算节点
  * 数据/代码互定位：为减少数据通信，尽可能地本地化数据处理
  * 出错处理：出错是常态，需要能检测并隔离出错节点，调度分配新的节点接管
  * 分布式数据存储与文件管理
  * Combiner和Partitioner(设计目的和作用)：
    * 为了减少数据通信开销，中间结果数据进入reduce节点前需要进行combine处理，把具有相同主键的数据合并到一起避免重复传送。 
    * map节点输出的中间结果需要用一定策略进行适当的划分处理，保证相关数据发送到同一 个reduce节点。
* **试述MapReduce在数据存储和计算失效容错处理和性能优化方面的主要处理措施。**
  * **数据存储方面**：
    * MapReduce在数据存储方面采用多副本存储设计，一个数据块的多个副本存放在不同的节点上：加快传输速度；采用多个副本可以判断网络传输是否出错；多个副本可以保证某个datanode失效的情况下不会丢失数据。
  * **服务器节点失效处理：**
    * 为了处理主节点的失效，主节点会周期性地设置**检查点**（checkpoint），检查整个计算作业的执行情况，一旦某个任务失效，可以从最近有效的检查点开始重新执行，避免从头开始计算的时间浪费。
    * 为了处理工作节点的失效，主节点会周期性地给工作节点发送心跳检测，如果工作节点没有回应，认为该工作节点失效，主节点将终止该工作节点的任务并把失效的任务重新调度到其他工作节点上重新执行。
  * **带宽优化**
    * 增加Combiner类，为了减少数据通信开销，中间结果数据进入Reduce节点前需要进行合并处理（Combine），把具有相同主键的数据合并到一起避免重复传送。
  * **计算优化**
    * 问题：Reduce节点必须要等到所有Map节点计算结束才能开始执行，因此，如果有一个计算量大、或者由于某个问题导致很慢结束的Map节点，则会成为严重的“拖后腿者”。
    * 把一个计算任务同时让多个 Map 节点做，取最快完成者的计算结果。
  * **用数据分区解决数据相关性问题**
    * 问题：一个Reduce节点上所处理的数据可能来自多个Map节点，因此，map节点输出的中间结果需要使用一定的策略进行适当的划分处理（Partition），保证相关数据发送到同一个reduce节点。
    * 利用Partitioner类，将数据分配到合适的Reducer，避免不同Reducer之间的数据相关性。



------

#### Ch3 Hadoop MapReduce

* **Mapreduce基本工作过程(基本架构/工作原理）**
  * **JobTracker的作用：**
    * **总结：作业控制（作业分解、状态监控）和资源管理**
    * JobTracker主要负责资源监控和作业调度。JobTracker监控所有的TaskTracker与作业的健康状态；同时JobTracker会跟踪任务的执行进度、资源使用量等信息。
  * **TaskTracker的作用：**
    * **总结：定期汇报心跳，执行JobTracker的命令**
    * TaskTracker会周期性地通过心跳将本节点上资源的使用情况和任务运行进度汇报给JobTracker，同时接收JobTracker发送过来的命令并执行相应的操作。
  * **MapReduce作业执行过程**
    * 将待处理的大数据，划分成大小相同数据块(如 64MB)，以及相应的用户作业程序。
    * 系统中有一个负责调度的主节点(JobTracker)以及数据 Map 和 Reduce 工作节点 (TaskerTracker)。
    * 用户提交作业给主节点。
    * 主节点为作业程序寻找和配备可用的 Map 节点，并将程序和作业传送给 Map 节点。
    * 主节点也为作业程序寻找和配备可用的 Reducer 节点，并将程序传送给 Reducer 节点。
    * 主节点启动每个 Map 及节点执行程序，每个 Map 节点尽可能读取本地或本机架的数据 进行计算。
    * 每个 Map 节点处理数据，做数据整理，将中间结果存放在本地;同时通知主节点任务完 成并告知中间结果存储位置。
    * 主节点等所有的 Map 节点计算完成后，开始启动 Reduce 节点执行，Reduce 节点从主节 点所掌握的中间结果数据位置信息，远程读取数据。
    * Reduce 节点将计算结果汇总输出到一个结果文件获得最后的结果。
* Combiner：本地Reducer

* partitinoer->sort
  * 传输到每一个Reducer节点上的、将被所有的Reduce函数接收到的键值对会被Hadoop自动排序（Map生成的结果传送到某一个节点的时候会被自动排序）
* **namenode 和 datanode的作用**
  * **Namenode：存储所有文件元数据、文件与数据块之间的映射关系、文件属性等核心数据**
  * **Datanode：存储具体的数据块**
* 程序执行时的容错处理与计算性能优化
  * 由Hadoop系统自己解决
  * 主要方法是将失败的任务进行再次执行
  * TaskTracker会把状态信息汇报给JobTracker，最终由JobTracker决定重新执行那一个任务。
  * 为了加快执行的速度，Hadoop也会自动重复执行同一个任务，以最先执行成功的为准（投机执行）
* **HDFS基本架构**
  * **namenode的作用**：负责管理HDFS的目录树和相关的文件元数据信息；同时还负责监控各个DataNode的健康状态。
  * **datanode的作用**：负责存储实际的数据，并将数据信息定期汇报给namenode。
* **HDFS可靠性与出错恢复**
  * datanode节点的检测
    * 心跳：namenode不断检测datanode是否有效
    * 若失效，则寻找新的节点替代，将失效节点数据重新分布
  * 集群负载均衡
  * 数据一致性：校验和checksum
  * 主节点元数据失效：checkpoint



------

#### Ch5 MapReduce算法设计

* **MapReduce可解决哪些算法问题？**
  * 基本算法：各种全局数据相关性小、能适当划分数据的计算任务
    * 分布式排序
    * 分布式文本匹配查找算法
    * 关系代数操作
    * 矩阵向量相乘、矩阵相乘
    * 词频统计
    * 文档倒排索引
    * 单词同现关系分析
  * 复杂算法或应用
    * Web搜索：网页爬取、倒排索引、搜索算法、网页排序
    * Web访问日志分析：分析和挖掘用户在Web上的访问，购物行为特征，以定制个性化用户界面或投放用户感兴趣的产品广告。
    * 数据/文本统计分析：如科技文献引用关系和统计、专利文献引用关系和统计
    * 图算法
    * 聚类

* Mapreduce应用案例
  * 纽约时报历史文章处理
  * 中国移动通信数据挖掘
  * IBM智力问答竞赛机器人Waston（自然语言处理）
  * 应用算法专著
* 排序算法：Partitinoer：快速查找、均匀划分
  * 如何避免在某些Reducer上聚集过多的数据而拖慢了整个程序？
  * 当有大量的key要分配到多个Partitioner时，如何高效地找到每个Key所属的partition？
    * TotalOrderPartitioner
    * TeraSort

* MapReduce算法流程
  * map(k1,v1)->[(k2,v2)]
  * Shuffle and sort
  * reduce(k2,[v2])->[(k3,v3)]
  * [...]表示一个列表



------

#### Ch6 HBase与Hive程序设计

* Hadoop可使用  **Configuration** 完成全局参数在全局计算节点上的传递和共享，而使用  **distributed cache **进行小数据量文件的全局传递。
* **HBase数据模型通过...进行索引和定位**
  * **行关键字**
  * **列关键字**
  * **时间戳**
* HBase基本架构
  * 由一个Master Server和一组子表数据区服务器Region Server构成，大表中的底层数据存储在HDFS中。
* HBase数据存储管理方法
  * 大表被分成很多个子表（region)，每个子表存储在一个子表服务器RegionServer上
  * 每个子表的数据区region由很多个数据存储块Store构成
  * 每个Store数据块又由存放在内存中的memStore和存放在文件中的storeFile构成
  * 当内存中的memStore数据达到一定大小时，系统将自动把数据写入到文件数据块StoreFile中， 而每个文件数据块最后都写入到底层HDFS中。
* **HBase的数据操作访问是通过 java 编程实现的**
* **Hive是一个分布式数据仓库，其数据操作访问编程是通过 HiveQL 实现的。**



------

#### Ch7 高级MapReduce编程技术

* 默认的Partitioner是**HashPartitioner**

* 重写Partitioner

  ```java
  class NewPartitioner extends HashPartitioner(K, V){
    void getPartition(K key, V value, int numReduceTasks){
        term = key.toString().split(",")[0];
        super.getPartition(term, value, numReduceTasks);
    }
  }
  ```

* 重写Combiner

  ```java
  class NewCombiner extends Reducer(K,V,K,V){
    public void Reduce(K key,V value, Context context){
      ...
    }
  }
  ```

* 复合键值对的使用
  * 用复合键让系统完成排序：
    * partition使得进入reduce节点的键值对是按照key进行排序的，而[value]不保证是排好序的，如果列表数据量很大时需要对value排序，可以将value中需要排序的部分加入到key中形成复合键，利用mapreduce系统的排序功能完成排序。
    * 但需要实现一个新的Partitioner，保证原来同一key值的键值对最后分区到同一个reduce节点上。
  * 把小的键值对合并成大的键值对
    * 有大量键值对，减少键值对传输和排序的开销。
* Hadoop可使用  **Configuration** 完成全局参数在全局计算节点上的传递和共享
  * Configuartion类专门提供以下用于保存和获取属性的方法。
  * mapper/reducer类初始化方法setup()从configuration对象中读出属性
* 使用  **Distributed Caches** 进行小数据量文件的全局传递。



------

#### Ch8 搜索引擎算法

###



------

#### Ch9 数据挖掘基础算法

* **K-means聚类**

  * 主要处理思路和过程
    * 将所有的数据分配到不同的mapreduce节点，每个节点只能对自己的数据进行计算；每个map节点可以获取上一轮迭代生成的cluster centers，并判断自己的各个数据点应该属于哪个cluster；reduce节点综合属于每个cluster的数据点，计算出新的cluster centers。并且需要保存全局共享的数据：当前的迭代次数、cluster id、cluster center、属于该cluster的数据点的个数。
    * 主要三个阶段
      * 扫描原始数据集合中的所有的点，并随机选取 K 个点作为初始的簇中心
      * 各个 Map 节点读取存储在本地的数据集，计算每个数据和簇中心的距离，由此 生成聚类集合，最后在 Reduce 节点用若干聚类集合生成新的全局聚类中心。迭代重复此过 程直至满足结束条件。
      * 根据最终生成的簇中心对所有的数据元素进行划分聚类的工作。
  * map

  ```java
  class Mapper{
    setup()
    {
      读取每个cluster的中心数据：cluster centers；
    }
    
    map(key, p) //p为一个数据点
    {
      minDis = Double.MAX VALUE;
      int index = -1;
      for i=0 to centers.length
      {
        dis = countDis(centers[i], p);
        if(dis < minDis) //计算与该数据点距离最小的数据中心
        {
          minDis = dis;
          index = i;
        }
      }
      emit(centers[i].clusterID, (p,1)); //将该数据点划分到该簇中
    }
  }
  ```

  * combine

  ```java
  class Combiner{
    reduce(clusterID, [(p1,1),(p2,1),...])
    {
      n = 数据点列表[(p1,1),(p2,1),...]的长度;
      pm = 0.0;
      for i=0 to n
        pm += p[i];
      pm = pm/n;  //求得这些属于一个map节点的属于相同cluster数据点的平均值
      emit(clusterID, (pm,n));
    }
  }
  ```

  * reduce

  ```java
  class Reducer{
    reduce(clusterID, [(pm1,n1),(pm2,n2),...])
    {
      k = 数据点列表[(pm1,n1),(pm2,n2),...]的长度;
      pm = 0.0;
      n = 0;
      for i=0 to k
      {
        pm += pm[i];
        n += n[i];
      }
      pm = pm/n; //求得所有属于clusterID的数据点的平均值为新的聚类中心
      emit(clusterID, (pm,n));
    }
  }
  ```

* **K最近邻分类**

  * 主要处理思路和过程
    * 将测试样本数据分块后分布在不同的mapreduce节点上进行处理；将训练数据放在distributed cache文件中供每个节点共享访问。map阶段对每个读出的测试样本数据，计算其与每个训练样本数据的相似度，得到与该样本最近的k个训练样本，根据带加权投票表决模型计算出 y'，发射（tsid,y')。reduce阶段直接输出（tsid,y')。
  * map

  ```java
  class Mapper{
    setup()
    {
      读取全局训练样本数据文件，转入本地内存的数据表TR中；
    }
    map(key, ts){ //ts表示一个测试样本数据
      ts -> tsid, Bi, xi;
      MaxS(k)赋值为空;
      
      for i to TR.length
      {
        TR[i] -> trid, Ai, yD;
        S = sim(Bi, Ai);
        若S属于前k个最大者，(S,yD)加入到MaxS;
      }
      根据 MaxS 和带权重投票表决模型计算出 y';
      emit(tsid, y');
    }
  }
  ```

  * reduce

  ```java
  class Reducer{
    reduce(tsid, [(y')])
    {
      emit(tsid, y');
    }
  }
  ```



------

#### Ch10 Spark

* **MapReduce的主要技术特点和不足，与hadoop相比，spark有哪些主要技术优点？**

* **MapReduce计算模式的缺陷**
  
  * **针对高吞吐量批处理，对低延迟执行不友好**
  * **需要将数据存储在HDFS上，不利于作业之间数据共享和迭代计算**
  * **磁盘读取花时间**
  * **没有充分利用内存，很难达到高性能**
* **对复杂计算支持不好，比如图计算、迭代计算**
  
* **与hadoop相比，Spark有哪些技术优点：**	
  
  * map reduce计算模式具有缺陷，2阶段固定模式下，磁盘计算量大，I/O性能低下
  * **Spark的运行速度更快。**内存速度有Hadoop MapReduce 100倍快，硬盘速度有10倍快。
  * **Spark更易使用。**可以使用Java、Scala、Python和R语言等快速写应用程序。
  * **Spark具有更广泛性**。结合了SQL、streaming、和复杂分析。
  * **Spark可以在多处运行。**它可以运行在Hadoop、Mesos、standalone或者在云端。他可以访问各种各样的数据源包括HDFS、Cassandra、HBase、和S3。
  * 在MapReduce中，跨作业数据分享的方式是通过磁盘文件，太慢了。而spark将大部分数据尽量放在内存中，更快。
  
* Scala里面类与对象的区别

* Spark的基本架构和组件

  * Master node + Driver
  * Worker node + Executer

* Spark应用程序的基本结构

  * Application (Driver+Executer)
  * Job：包含多个task的并行计算（一个作业就是一张RDD世系图，DAG图）
  * Stage：job拆分成多组task，每组任务被称为stage
  * Task：基本程序执行单元，在一个executer上执行

* Spark的技术特点

  * 基于内存计算的弹性分布式数据集(RDD)
  * 灵活的计算流图(DAG)
  * 覆盖多种计算模式(查询分析,批处理,流式计算,迭代计算,图计算,内存计算)
  * 事件驱动的调度方式
    * Spark采用了事件驱动的Scala库类Akka来完成任务 的启动，通过复用线程池的方式来取代MapReduce进程或者线程启动和 切换的开销。

* spark数据共享元语：RDD

  * 基于内存的数据共享，读出来的数据放到内存中，快

  * **弹性分布式数据集 Resilient Distributed Datasets (RDDs)**

    * 是Spark最核心的分布式数据抽象

    * RDD 是指能横跨集群所有节点进行并行计算的分区元素集合

    * Spark使用RDD以及对应的**Transformation/Action**等操作算子执行 分布式计算

      * **Transformation:**一种惰性操作，只是定义了一个新的RDD，并不马上计算新的RDD内部的值。（**只记录，不计算**）
      * **Action:**立即计算这个RDD的值，并返回结果给程序，或者将结果写入到外存储中。（**计算**）

    * 基于RDD之间的依赖关系组成**lineage（世系）**，**重计算以及checkpoint**等机制来保证整个分布式计算的**容错性**

    * **只读、可分区**，**这个数据集的全部或部分可以缓存在内存中**，在多次计算间重用，弹性是指内存不够时可以与磁盘进行交换

      ```java
      messages.cache()  
      ```

    * 一个作业(Job)就是一组 **Transformation**操作和一个**action**操作的集合。每执 行一次action操作，那么就会提交一个Job。

    * Stage分为两种，**Shuffle Stage和final Stage**，每个作 业必然只有一个final Stage，即每个action操作会生成一个final stage，如果一个作业中还包含Shuffle操作，那么每进行一次shuffle操作，便会生成一个 Shuffle Stage

    * Shuffle操作只有在宽依赖的时候才会触发。

    * 任务(Task)作用的单位是**Partition**，针对同一个 Stage，分发到不同的Partition上进行执行

    * 总而言之，Job和Stage是针对一个RDD执行过程的 划分，而Task则是具体到了RDD中每个分区的执行

* **宽依赖、窄依赖**

  * **窄依赖**：父RDD中的一个Partition最多被子RDD中的一个Partition所依赖
  * **宽依赖**：父RDD中的一个Partition被子RDD中的多个Partition所依赖
  * 如何划分宽依赖与窄依赖的

* RDD常见的持久化方式

  * 未序列化的Java对象，存于内存中
  * 序列化的数据，存于内存中
  * 磁盘存储

* DAG是如何判断有多少个Stage

* Spark环境中其它功能组件

  * SparkSQL
  * SparkStreaming
  * GraphX
  * MLlib



------

### 算法实现

#### 带词频统计的文档倒排索引

* 总体思路：
  * 用Mapper计算每个单词在所有文档中出现的次数，用Reducer汇聚每个单词的结果。这里键值对的设计如下：
    * Key：word:filename
    * Value：count（出现的次数，在Mapper中每个word的value赋值为1）
  * Mapper：计算每个单词在文档中出现的次数，映射为<word:filename, 1>
  * Combinoner：提前汇总Reduce的一部分结果，减少网络传输以及Reducer的负担
  * Partitioner：由于采用了word:filename作为键，同一个单词的键值对可能被划分到不同的Reducer，因此定制Partitioner按照word分区选择正确的Reducer，而不是word:filename，保证同一word下的键值对一定被分到同一个Reducer上去。
  * Reducer：执行每个单词的统计，映射为<word, [(filename:sum)]>的形式
* mapper

```java
protected void map(Object key, Text value, Context context){
  FileSplit fileSplit = (FileSplit)contex.getInputSplit();
  String filename = key.getPath().getName(); //获取文件名
  IntWritable _one = IntWritable(1);
  Text word = new Text();
  StringTokenizer tokenizer = new StringTokenizer(value.toString());
  while(tokenizer.hasMoreTokenizer()){
    word.set(tokenizer.nextToken() + "#" + filename); //key
    context.write(word, _one);  //value:1
  }
}
```

* conbinner

```java
protected void combine(Text key, Iterable<IntWritable> values, Context context){
  int sum = 0;
  for(IntWritable value:values){
    sum += value.get();  //提前汇聚mapper的一部分结果，减少通讯开销
  } 
  context.write(key,newIntWritable(sum));
}
```

* Partitioner

```java
public int getPartition(Text key, IntWritable value, int numReduceTasks){
  String word = key.toString().split("#")[0]; //使partitioner按照word划分到reducer，避免...
  return super.getPartitioner(new Text(word), value, numReduceTasks);
}
```

* reducer

```java
class Reducer{
  private static Text _EMPTY_WORD = new Text();
  private Text m_preWord;
  private List<String> m_postingList;
  private int m_wordCnt;  //对于所有的文件中某个单词的个数
  
  protected void setup(Context context){
    m_preWord = _EMPTY_WORD;
    m_postintList = new ArrayList<String>();
    m_wordCnt = 0;
  }
  
  privated void reduce(Text key, Iterable<IntWritable> values, Context context){
    String[] wordFile = key.toString().split("#");
    Text word =new Text(wordFile[0]);
    String filename = wordFile[1];
    
    if(!m_preWord.equals(word) && !m_postingList.isEmpty()){
      //如果遇到新的单词，说明上一个单词统计结束了，该上报了。
      commitResultOnce(context); //提交一次统计结果，包含统计平均值的计算
      m_preWord = word;
      m_wordCnt = 0;
      m_postingList.clear();
    }
    // 正常执行每个单词的统计，映射为 <Word,[fileName:sum]>的形式
    int perFileCnt = 0;
    for (IntWritable val:values){
      perFileCnt += val.get();
    }
    wordCnt += perFileCnt;
    m_postingList.add(filename + ":" + perFileCnt);//这里相当于完成了对一个单词在一个小说的统计
  }
  
  protected void cleanup(Context context){
    commitResultOnce(context); //提交最后一个单词的统计结果
    super.cleanup(context);
  }
}
```



#### 专利文献数据分析

* 专利文献数据

<img src="/Users/gaohedi/Library/Application Support/typora-user-images/image-20201221205533396.png" alt="image-20201221205533396" style="zoom: 33%;" />

* 专利被引列表

  * Map

  ```java
  class Mapper{
    public void map(LongWritable key, Text value, Context context){
      String[] citation = value.toString().split(",");
      context.writ(new Text(citation[1]), new Text(citation[0]));
      //输出key:被引用专利号，value:引用专利号
    }
  }
  ```

  * Reduce

  ```java
  class Reducer{
    public void reduce(Text key, Iterable<Text> values, Context context){
      String csv = new String();
      for(Text val : values){
        if (cvs.length()>0){
          csv += ",";
        }
        csv += val.toString();
      }
      context.write(key, new Text(csv));
    }
  }
  ```

* 专利被引次数

  * map

  ```java
  class Mapper{
    public void map(LongWritable key, Text value, Context context){
      String[] citation = value.toString().split(",");
      context.write(new Text(citation[1]),new IntWritable(1));
    }
  }
  ```

  * reduce

  ```java
  class Reducer{
    public void reduce(Text key, Iterable<Text> values, Context context){
      int cnt = 0;
      for(Text val : values){
        cnt += val.get();
      }
      context.write(key, new IntWritable(cnt));
    }
  }
  ```

* 年份/国家专利数统计

  <img src="/Users/gaohedi/Library/Application Support/typora-user-images/image-20201221212322238.png" alt="image-20201221212322238" style="zoom: 33%;" />

  * 主要设计思想是:扫描以上的专利描述数据集，根据要统计的 列名(年份或国家等)，取出对应列上的年份(col_idx=1)或国家 (col_idx=4)，然后由Map发出(year，1)或(country，1)，再由 Reduce累加。

  * map

  ```java
  class Mapper{
    public void map(Text key, Text value, Context context){
      String[] cols = value.toString().split(",");
      String col_data = cols[1]; //1:年份
      context.write(new Text(col_data), new IntWritable(1));
    }
  }
  ```

  * reduce

  ```java
  class Reducer{
    public void reducer(Text key, Iterable<IntWritable> values, Context context){
      int count = 0;
      for(values.hasNext()){
        count += values.next().get();
      }
      context.write(key, new IntWritable(count));
    }
  }
  ```

* 每年申请美国专利的国家数统计

  * map

  ```java
  class Mapper{
    public void Map(Text key, Text value, Context context){
      String[] cols = value.toString().split(",");
      String col_data = cols[1] + "," + cols[4]; //1:年份,4:国家
      context.write(new Text(col_data), new IntWritable(1));
    }
  }
  ```

  * Combine

  ```java
  class Combiner{
    public void Combine(Text key, Iterable<Text> values, Context context){
      context.write(key, new IntWritable(1));
    }
  }
  ```

  * Partition

  ```java
  class Partitioner{
    public int getPartition(Text key, IntWritable value, int numReduceTasks){
      String year = key.toString()[0];
      return super.getPartition(new Text(year), value, numReduceTasks);
    }
  }
  ```

  * reduce

  ```java
  class reducer{
    private static Text _NO = new Text();
    private Text m_preYear;
    private int m_countCountry;
    
    public void setup(Context context){
      m_preYear = _NO;
      m_countCountry = 0;
    }
    
    public void reduce(Text key, Iterable<IntWritable> values, Context context){
      String[] yearAndCountry = key.toString().split(",");
      Text year = new Text(yearAndCountry[0]);
      
      if(!m_preYear.equals(year) && !(m_countCoutry==0)){
        //开始下一个year了
        context.write(pre_year, new IntWritable(m_countCountry));
        m_preYear = year;
        m_countCountry = 0;
      }
      
      for(IntWritable val:values){
        m_countCountry += val.get();
      }
    }
    
    public void cleanup(Context context){
      context.write(pre_year, new IntWritable(m_countCountry)); //提交最后一个年份的统计结果
      super.cleanup(context);
    } 
  }
  ```



#### K最近邻分类并行化算法

* K最近邻分类并行化算法设计思想：

  * 计算测试样本到各个训练样本的距离，取其中距离最小的K个，并根据这K个训练样本的标记进行投票的到测试样本的标记。

* MapReduce并行化算法设计思路

  * 将测试样本数据分块后分布在不同的节点上进行处理，将训练样本数据文件放在DistributedCache中供每个节点共享访问。

  * Map阶段对每个读出的测试样本数据ts(tsid, A', y')

    * 计算其与每个训练样本数据tr(trid, A, y)之间的相似度S=Sim(A', A)

    * 检查S是否比目前的k个S中最小的大，若是则将(S, y)计入k个最大者

    * 根据所保留的k个S值最大的(S, y)，根据模型 计算出ts的分类标记值y'，发射出(tsid, y')
      $$
      y^{'} = \sum S_i*y_i/\sum S_i
      $$

  * Reduce阶段直接输出(tsid, y')

* 伪代码

  * Mapper伪代码

  ```java
  class Mapper{
    setup{
      读取全局训练样本数据文件，转入本地内存的数据表TR中
    }
    map(key,ts)//ts为一个测试样本
    {
      空集 -> MaxS;
      ts -> tsid, A', y';
      for (i=0 to TR.length)
      {
        TR[i] -> trid, A, y;
        S = Sim(A, A');
        若S属于k个最大者，(S,y)->MaxS;
      }
      根据MaxS和带加权投票表决模型计算出 y'= ...
      emit(tsid, y');
    }
  }
  ```



#### K-Means聚类算法

* MapReduce并行化聚类算法设计思路
  * 将所有的数据分布不到不同的MapReduce节点上，每个节点只对自己的数据进行计算
  * 每个Map节点能够读取上一次迭代生成的cluster centers，并判断自己的数据点应该属于哪一个cluster
  * Reduce节点综合每个属于每个cluster的数据点，计算出新的cluster centers
* 需要全局共享的数据（唯一的全局文件）
  * 当前的迭代计数
  * K个表示不同聚类中心的如下的数据结构
    * cluster id
    * cluster center
    * 属于该cluster center的数据点的个数

* Map阶段的处理

  * 在Map类的初始化方法setup()中读取全局的聚类中心信息
  * 对Map方法收到的每一个数据点p，计算p与所有聚类中心间的距离，并选择一个距离最小的中心作为所属的聚类，输出<clusterID,(p,1)>键值对
  * 对每个Map节点上即将传递到Reduce节点的每一个<clusterID,(p,1)>键值对，用Combiner进行数据优化，合并相同clusterID下的所有数据点并求取这些点的均值pm以及数据点个数n。

  ```java
  class Mapper{
    setup(...){
      读取全局的聚类中心数据 -> centers;
    }
    
    map(key, p) //p为一个数据点
    {
      minDis = Double.MAX VALUE;
      index = -1;
      for i=0 to Centers.length
      {
        dis = ComputeDist(p, Centers[i]);
        if dis < minDis
        {
          minDis = dis;
          index = i;
        }
      }
      emit(Centers[i], ClusterID, (p,1));
    }
  }
  ```

* Combiner伪代码

  ```java
  class Combiner{
    reduce(ClusterID, [(p1,1),(p2,1),...])
    {
      pm = 0.0;
      n = 数据点列表[(p1,1),(p2,1),...]中数据点的个数；
      for i=0 to n
      {
        pm += p[i];
      }
      pm = pm/n; //求得这些数据点的平均值
      emit(ClusterID,(pm,n));
    }
  }
  ```

* Reduce阶段的处理

  * 经过Map和Combiner后从Map节点输出的所有ClusterID相同的中间结果<ClusterID, [(pm1,n1),(pm2,n2),...]>，计算新的均值pm，输出<ClusterID, (pm,n)>
  * 所有输出的<ClusterID, (pm,n)>形成新的聚类中心，供下一次迭代计算

  ```java
  class Reducer{
    reduce(ClusterID, value=[(pm1,n1),(pm2,n2),...])
    {
      pm = 0.0;
      n = 0;
      k = 数据点列表中数据项的总个数；
      for i=1 to k
      {
        pm += p[i]*n[i];
        n += n[i];
      }
      pm = pm/n; //求得所有属于ClusterID的数据点的平均值
      emit(ClusterID, (pm,n)); //输出新的聚类中心的数据值
    }
  }
  ```



#### 朴素贝叶斯分类并行化算法

* MapReduce并行化算法设计思路

  * 用MapReduce扫描训练数据集，计算每个分类Yi出现的频度FYi、以及每个属性值出现在Yi中的频度FxYij
  * 在进行分类预测时，对每一个未标记的测试样本X，根据其包含的每个具体属性值xj，以及从训练数据集计算得出的FxYij进行求积得FXYi，再乘以FYi即可得到X在各个Yi中出现的频度P(X|Yi)P(Yi)，取得最大频度的Yi即为X所属的分类。

* MapReduce并行算法实现

  * 训练数据集Yi频度统计Mappper伪代码

  ```java
  class Mapper{
    map(key, tr) //tr为一个训练样本
    {
      tr -> trid, A, y; //k是属性值集合，y是分类
      emit(y,1);
      for i=0 to A.length
      {
        A[i] -> 属性名xni和属性值xvi；
        emit(<y, xni, xvi>,1);
      }
    }
  }
  ```

  * 训练数据集Yi频度统计Reducer伪代码
    * 输出结果为所有Yi出现的频度FTi，以及所有属性值xj在Yi中出现的频度
    * 进行未标记测试样本X的分类预测时，可以从这个输出数据文件中快速查找相应的频度值并最终计算出X在各个Yi中出现的频度P(X|Yi)P(Yi)，最后取得频度最大的Yi即为X所属的分类

  ```java
  class reducer{
    reduce(key, value_list) //key或为分类标记y，或为<y,xni,xvi>
    {
      sum = 0;
      while(value_list.hasNext())
        sum += value_list.next().get();
      emit(key,sum);
    }
  }
  ```

  * 测试样本分类预测Mapper伪代码

  ```java
  class Mapper{
    setup(...)
    {
      读取从训练样本中得到的频度数据；
      分类频度表FY = {(Yi, 每个Yi的频度FYi)};
      属性频度表FxY = {(<Yi,xnj,xvj),出现频度FxYij)};
    }
    
    map(key,ts) //ts为一个测试样本
    {
      ts -> tsid, A;
      MaxF = MIN_VALUE;
      idx = -1;
      for(i=0 to FY.length)
      {
        FXYi = 1.0;
        Yi = FY[i].Yi;
        FYi = FY[i].FYi;
        for(j=0 to A.length)
        {
          xnj = A[j].xnj;
          xvj = A[j].xvj;
          根据<Yi,xnj,xvj>扫描FxY表，取得FxYij;
          FXYi = FXYi * FxYij;
        }
        if(FXYi * FYi > MaxF)
        {
          MaxF = FXYi * FYi; idx = i;
        }
      }
      emit(tsid,FY[idx].Yi);
    }
  }
  ```



#### SVM短文本多分类并行算法

* 基本算法设计思路

  * 训练阶段，对于多类(480 类)问题，为了提高分类精度， 首先针对每个类做一个2-Class两类分类器
  * 预测阶段，分别用480 个分类器对每个待预测的样本进行分 类并打分，选择分类为“是”且打分最高的类别作为该样 本可能的预测类别；如打分低于最低阈值，则将该测试样 本判定为不属于480类的异类

* 分两步MapReduce处理完成

  * 用训练数据产生480个2-class分类器
    * Map: 将每个训练样本的分类标签ClassID作为主键，输出(ClassID, <true/false，特征向量>)
    * Reduce: 具有相同ClassID的键值对进入同一个Reduce，然后训练出一个2-Class SVM分类模型，共输出480个2-Class SVM分类模型

  ```java
  class mapper{
    
  }
  ```

  * 用480个2-Class分类器模型处理测试数据

    * Map:将每个测试样本，以SampleID作为主键，输出(SampleID, <LableID，评分Score>)

    * Reduce: 具有相同SampleID的键值对进入同一个Reduce，然后以最高评分者对应的标记作为该样

      本最终的标记;虽然是最高评分，但仍然低于最小阈值，则判定为不属于已知的480个类

  ```java
  
  
  ```

  

#### 频繁项数据挖掘算法

* PSON = Apriori + SON
  * Map tasks：一个Mapper节点计算分给他的chunk的局部频繁项集
  * Reduce tasks：
    * 具有相同长度的局部频繁项集由一个Reduce节点处理
    * 每个节点计算他收到的全局候选项集
    * 然后决定全局频繁项集
* 运行两个Mapreduce作业去获取所有的频繁项集
  * 第一次Mapreduce获取所有的候选项集
  * 第二次mapreduce从候选项集中获取频繁项集
* 第一次Mapreduce获取所有的候选项集
  * Map：每个节点通过Apriori 算法计算分给他的partition的局部频繁项集，对于每一个局部频繁项集F，发射<F, 1>。F表示该频繁项集在这个partition内是一个频繁项集。
  * Shuffle和Sort：同样的局部频繁项集放送到一个reduce节点
  * Reduce： 只发射一个键值对<F,1>
  * 最后将DFS中的所有键值对合并到一起
* 第二次mapreduce从候选项集中获取频繁项集
  * 每个节点都有一个所有全局候选项集的完全的复制
  * Map：每个节点计算他的分区的候选项集的频数，然后发射<C,v>，C是全局候选项集，v是他在这个partition的计数
  * Shuffle和Sort：同样的全局候选项集放送到一个reduce节点
  * Reduce：计算每一个全局候选集C的总计数，想DFS发送真正的全局频繁项集