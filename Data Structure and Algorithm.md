# 数据结构和算法面试攻略

数据结构主要包括
1. 数组和字符串
2. 链表
3. 队列
4. 栈
5. 堆
6. 树
7. 哈希表

主要考察包括基本结构、基本用法、以及一些由这些结构衍生出来和其他算法结合的问题。队列和栈一般不单独考察，而是作为其他解法（尤其是回溯）中很重要的元素，因此不单独列出。

算法主要包括

1. 排序
2. 分治
3. 回溯
4. 动态规划
5. 贪心算法
6. 海量数据问题
7. 越界问题等。

其中分治、回溯、动态规划、贪心算法多和数据结构(尤其是数组)结合，因此不单独列出来。

---

数组和字符串
------

链表
------

堆
------

树
------

## 图

排序算法
------

大数查找
------

> 所谓海量数据处理，无非就是基于海量数据上的存储、处理、操作。
> 何谓海量，就是数据量太大，所以导致要么是无法在较短时间内迅速解决，要么是数据太大，导致无法一次性装入内存。
>
> 那解决办法呢？
>
> 针对时间，我们可以采用巧妙的算法搭配合适的数据结构，如Bloom filter/Hash/bit-map/堆/数据库或倒排索引/trie树。
>
> 针对空间，无非就一个办法：大而化小，分而治之（hash映射），你不是说规模太大嘛，那简单啊，就把规模大化为规模小的，各个击破不就完了嘛。
>
> 至于所谓的单机及集群问题，通俗点来讲，单机就是处理装载数据的机器有限(只要考虑cpu，内存，硬盘的数据交互)，而集群，机器有多辆，适合分布式处理，并行计算(更多考虑节点和节点间的数据交互)。

### 分而治之/hash映射 + hash统计 + 堆/快速/归并排序

- 分而治之/hash映射：针对数据太大，内存受限，只能把大文件化成(取模映射)小文件。
- hash_map统计：当大文件转化了小文件，那么我们便可以采用常规的hash_map(key，value)来进行频率统计。
- 堆/快速排序：统计完了之后，便进行排序(可采取堆排序)，得到次数最多的key。

#### 多层划分

多层划分，其实本质上还是分而治之的思想，重在“分”的技巧上！

- 适用范围：第k大，中位数，不重复或重复的数字。
- 基本原理及要点：因为元素范围很大，不能利用直接寻址表，所以通过多次划分，逐步确定范围，然后最后在一个可以接受的范围内进行。

#### Bloom filter/Bitmap

- Bloom filter

  适用范围：可以用来实现数据字典，进行数据的判重，或者集合求交集。

  基本原理：当一个元素被加入集合时，通过K个Hash函数将这个元素映射成一个位阵列（Bit array）中的K个点，把它们置为1。检索时，我们只要看看这些点是不是都是1就（大约）知道集合中有没有它了：如果这些点有任何一个0，则被检索元素一定不在；如果都是1，则被检索元素很可能在。

  Bloom Filter的这种高效是有一定代价的：在判断一个元素是否属于某个集合时，有可能会把不属于这个集合的元素误认为属于这个集合（false positive）。因此，Bloom Filter不适合那些“零错误”的应用场合。而在能容忍低错误率的应用场合下，Bloom Filter通过极少的错误换取了存储空间的极大节省。

- Bitmap

  Bitmap就是用一个bit位来标记某个元素对应的Value， 而Key即是该元素。由于采用了Bit为单位来表示某个元素是否存在，因此在存储空间方面，可以大大节省。

  Bitmap排序方法
  第一步，将所有的位都置为0，从而将集合初始化为空。
  第二步，通过读入文件中的每个整数来建立集合，将每个对应的位都置为1。
  第三步，检验每一位，如果该位为1，就输出对应的整数。

Bloom filter可以看做是对Bitmap的扩展。

#### Trie树/数据库/倒排索引

- Trie树
  适用范围：数据量大，重复多，但是数据种类小可以放入内存。
  基本原理及要点：实现方式，节点孩子的表示方式。
  扩展：压缩实现。
- 数据库索引
  适用范围：大数据量的增删改查。
  基本原理及要点：利用数据的设计实现方法，对海量数据的增删改查进行处理。
- 倒排索引(Inverted index)
  适用范围：搜索引擎，关键字查询。
  基本原理及要点：一种索引方法，被用来存储在全文搜索下某个单词在一个文档或者一组文档中的存储位置的映射。

#### 外排序

- 适用范围：大数据的排序，去重。
- 基本原理及要点：外排序的归并方法，置换选择败者树原理，最优归并树。

#### Map Reduce

Map Reduce是一种计算模型，简单的说就是将大批量的工作（数据）分解（MAP）执行，然后再将结果合并成最终结果（REDUCE）。这样做的好处是可以在任务被分解后，可以通过大量机器进行并行计算，减少整个操作的时间。

- 适用范围：数据量大，但是数据种类小可以放入内存。
- 基本原理及要点：将数据交给不同的机器去处理，数据划分，结果归约。

---

* #### 4G的long型整数中找到一个最大的，如何做？
  要找到最大的肯定要遍历所有的数的，而且不能将数据全部读入内存，可能不足。算法的时间复杂度肯定是O（n）
可以改进的地方，就是读入内存的时候，一次多读些
需要注意的就是每次从磁盘上尽量多读一些数到内存区，然后处理完之后再读入一批。减少IO次数，自然能够提高效率。而对于类快速排序方法，稍微要麻烦一些： 分批读入，假设是M个数，然后从这M个数中选出n个最大的数缓存起来，直到所有的N个数都分批处理完之后，再将各批次缓存的n个数合并起来再进行一次类快 速排序得到最终的n个最大的数就可以了。在运行过程中，如果缓存数太多，可以不断地将多个缓存合并，保留这些缓存中最大的n个数即可。由于类快速排序的时 间复杂度是O（N），这样分批处理再合并的办法，依然有极大的可能会比堆和败者树更优。当然，在空间上会占用较多的内存。

此题还有个变种，就是寻找K个最大或者最小的数。有以下几种算法：
  容量为K的最大堆/最小堆，假设K可以装入内存；
如果N个数可以装入内存，且都小于MAX，那么可以开辟一个MAX大的数组，类似计数排序。。。从数组尾部扫描K个最大的数，头部扫描K个最小的数。

------

* #### 有千万个string在内存怎么高速查找，插入和删除？
  对千万个string做hash，可以实现高速查找，找到了，插入和删除就很方便了。
-----


* #### 找出1-10w中没有出现的两个数字
  - 方法1：
    设这些数保存在数组A中，用一个10w的数组B标志某一个数是否出现，i出现则B[i]=1,没出现则B[i]=0；扫描数组B，查找缺失的两个数。
    时间复杂度：O(N)
    空间复杂度：O(N)

  - 方法2：
    设确实的两个数为X和Y，则我们可以通过累加1到10W的和减去数组A的和，得到X+Y的值S1：
    X+Y = S1
    同理，我们可以得到1到10W的平方和 减去 数组A中每个数的平方和，从而得到X^2 + Y^2的值：
    X^2 + Y^2 = S2
    根据两个方程可以解出X和Y。
    这里需要注意的是，计算平方和的时候有可能会越界，可以使用unsigned long long，或者一边加(1到10W的平方和)一边减(数组A中每个数)。
-----


* #### 判断数字是否出现在40亿个数中？给40亿个不重复的unsignedint的整数，没排过序的，然后再给几个数，如何快速判断这几个数是否在那40亿个数当中？
  ​        unsigned int 的取值范围是0到232-1。我们可以申请连续的232/8=512M的内存，用每一个bit对应一个unsigned int数字。首先将512M内存都初始化为0，然后每处理一个数字就将其对应的bit设置为1。当需要查询时，直接找到对应bit，看其值是0还是1即可。

  将这40亿个数分成两类: 最高位为0、最高位为1
  并将这两类分别写入到两个文件中，其中一个文件中数的个数<=20亿，而另一个>=20亿（这相当于折半了）；与要查找的数的最高位比较并接着进入相应的文件再查找。
  再然后把这个文件为又分成两类: 次最高位为0、次最高位为1
  并将这两类分别写入到两个文件中，其中一个文件中数的个数<=10亿，而另一个>=10亿（这相当于折半了）；> 与要查找的数的次最高位比较并接着进入相应的文件再查找
  …
  以此类推，就可以找到了,而且时间复杂度为O(logn)
-----

* #### 在一个文件中有10G个整数，乱序排列，要求找出中位数。内存限制为2G
  ​        假设整数用32bit来表示。
  第一步：要表示10G个整数，最少需要一个64位的数据空间。（10G = 5 * 2^31 > 2^32 )
  第二步：分区间
  2G的内存，能够表示多少个64bit，就能分多少个区间。（一个区间 就表示 一个64bit的数据空间）
  区间数位：2G / 64bit = 256M 个区间。
  第三步：求区间表示范围
  32bit的整数最大值为232-1,所以区间的范围是232 / 256M = 16.
  即0 ~ 15 ，16 ~ 31，32 ~ 47，…（总共256M个)
  此时我们有 256M个区间，大小总共为256M * 64bit = 2G内存。
  第四步：遍历10G个整数。每读取一个整数就将此整数对应的区间+1。
  第五步：找出中位数所在的区间
  统计每个区间中整数的值。然后从第一个区间的整数值开始累加。当累加到5G时，停止。此时的区间便包含中位数。记下此区间所表示的范围，设为[a,a+15].并且记下此区间之前所有区间的累加和，设为m。释放掉除包含中位数区间的其他所有区间的内存。
  第六步：再次遍历10G个整数，统计出现在区间[a,a+15]中每个值的计数，有16个数值，按照a到a+15排序。设为n0,n1,n2,…n15
  第七步：当m+n0+n1+…+nx首次大于5G时，此时的 a+x 就是所求的中位数。

-----

* #### 统计论坛在线人数分布。求一个论坛的在线人数，假设有一个论坛，其注册ID有两亿个，每个ID从登陆到退出会向一个日志文件中记下登陆时间和退出时间，要求写一个算法统计一天中论坛的用户在线分布，取样粒度为秒。
  ​    一天总共有 3600*24 = 86400秒。
  定义一个长度为86400的整数数组int delta[86400]，每个整数对应这一秒的人数变化值，可能为正也可能为负。开始时将数组元素都初始化为0。
  然后依次读入每个用户的登录时间和退出时间，将与登录时间对应的整数值加1，将与退出时间对应的整数值减1。
  这样处理一遍后数组中存储了每秒中的人数变化情况。
  定义另外一个长度为86400的整数数组int online_num[86400]，每个整数对应这一秒的论坛在线人数。
  假设一天开始时论坛在线人数为0，则第1秒的人数online_num[0] = delta[0]。第n+1秒的人数online_num[n] = online_num[n-1] + delta[n]。
  这样我们就获得了一天中任意时间的在线人数。
  另外，如果只知道IP，为了方便查找，可以采用哈希表进行记录，较数组更为方便。

-----

* #### 需要多少只小白鼠才能在24小时内找到毒药有1000瓶水，其中有一瓶有毒，小白鼠只要尝一点带毒的水24小时后就会死亡，至少要多少只小白鼠才能在24小时时鉴别出那瓶水有毒？
    ​       每个老鼠只有死或活2种状态，因此每个老鼠可以看作一个bit，取0或1
  N个老鼠可以看作N个bit，可以表达2^N种状态（其中第i个状态代表第i个瓶子有毒）
  例如：当N＝2时，可以表达4种状态，假设有A、B、C、D四个瓶子分别编号入下
  
  0， 0
  
  0， 1
  
  1， 0
  
  1， 1
  
  一号老鼠喝第一位为1的，二号老鼠喝第二位为1的，将老鼠活着记为0，死了记为1，则
  如果 一号老鼠活，二号老鼠活，状态为（0, 0）说明是A有毒
  如果 一号老鼠活，二号老鼠死 ,  状态为（0, 1）说明是B有毒 
  如果 一号老鼠死，二号老鼠活 ，状态为（1, 0）说明是C有毒 
  如果 一号老鼠死，二号老鼠死 ，状态为（1, 1）说明是D有毒
  
  ​    对N个瓶子也是一样的处理：让第一只老鼠喝所有第一位为1的瓶子，第二只喝第二位，依次喝下去。状态位和瓶子编号可以刚好对应，一次完成。

-----

* #### 给定a、b两个文件，各存放50亿个url，每个url各占64字节，内存限制是4G，让你找出a、b文件共同的url？
  
  方案1：可以估计每个文件安的大小为50G×64=320G，远远大于内存限制的4G。所以不可能将其完全加载到内存中处理。考虑采取分而治之的方法。
  
  s 遍历文件a，对每个url求取[![clip_image002](https://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image002_thumb.png)](http://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image002_2.png)，然后根据所取得的值将url分别存储到1000个小文件（记为[![clip_image004](https://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image004_thumb.png)](http://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image004_2.png)）中。这样每个小文件的大约为300M。
  
  s 遍历文件b，采取和a相同的方式将url分别存储到1000各小文件（记为[![clip_image006](https://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image006_thumb.png)](http://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image006_2.png)）。这样处理后，所有可能相同的url都在对应的小文件（[![clip_image008](https://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image008_thumb.png)](http://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image008_2.png)）中，不对应的小文件不可能有相同的url。然后我们只要求出1000对小文件中相同的url即可。
  
  s 求每对小文件中相同的url时，可以把其中一个小文件的url存储到hash_set中。然后遍历另一个小文件的每个url，看其是否在刚才构建的hash_set中，如果是，那么就是共同的url，存到文件里面就可以了。
  
  方案2：如果允许有一定的错误率，可以使用Bloom filter，4G内存大概可以表示340亿bit。将其中一个文件中的url使用Bloom filter映射为这340亿bit，然后挨个读取另外一个文件的url，检查是否与Bloom filter，如果是，那么该url应该是共同的url（注意会有一定的错误率）。

-----

* #### 有10个文件，每个文件1G，每个文件的每一行存放的都是用户的query，每个文件的query都可能重复。要求你按照query的频度排序

  方案1：

  s 顺序读取10个文件，按照hash(query)%10的结果将query写入到另外10个文件（记为[![clip_image010](https://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image010_thumb.png)](http://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image010_3.png)）中。这样新生成的文件每个的大小大约也1G（假设hash函数是随机的）。

  s 找一台内存在2G左右的机器，依次对这十个文件用hash_map(query, query_count)来统计每个query出现的次数。利用快速/堆/归并排序按照出现次数进行排序。将排序好的query和对应的query_cout输出到文件中。这样得到了10个排好序的文件（记为[![clip_image012](https://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image012_thumb.png)](http://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image012_3.png)）。

  s 对这10个文件进行归并排序（内排序与外排序相结合）。

  方案2：

  一般query的总量是有限的，只是重复的次数比较多而已，可能对于所有的query，一次性就可以加入到内存了。这样，我们就可以采用trie树/hash_map等直接来统计每个query出现的次数，然后按出现次数做快速/堆/归并排序就可以了。

  方案3：

  与方案1类似，但在做完hash，分成多个文件后，可以交给多个文件来处理，采用分布式的架构来处理（比如MapReduce），最后再进行合并。

-----

* #### 有一个1G大小的一个文件，里面每一行是一个词，词的大小不超过16字节，内存限制大小是1M。返回频数最高的100个词

  方案1：顺序读文件中，对于每个词x，取[![clip_image014](https://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image014_thumb.png)](http://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image014_2.png)，然后按照该值存到5000个小文件（记为[![clip_image016](https://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image016_thumb.png)](http://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image016_2.png)）中。这样每个文件大概是200k左右。如果其中的有的文件超过了1M大小，还可以按照类似的方法继续往下分，知道分解得到的小文件的大小都不超过1M。对每个小文件，统计每个文件中出现的词以及相应的频率（可以采用trie树/hash_map等），并取出出现频率最大的100个词（可以用含100个结点的最小堆），并把100词及相应的频率存入文件，这样又得到了5000个文件。下一步就是把这5000个文件进行归并（类似与归并排序）的过程了。

---

* #### 海量日志数据，提取出某日访问百度次数最多的那个IP。

  方案1：首先是这一天，并且是访问百度的日志中的IP取出来，逐个写入到一个大文件中。注意到IP是32位的，最多有[![clip_image018](https://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image018_thumb.png)](http://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image018_3.png)个IP。同样可以采用映射的方法，比如模1000，把整个大文件映射为1000个小文件，再找出每个小文中出现频率最大的IP（可以采用hash_map进行频率统计，然后再找出频率最大的几个）及相应的频率。然后再在这1000个最大的IP中，找出那个频率最大的IP，即为所求。

---

* ##### 在2.5亿个整数中找出不重复的整数，内存不足以容纳这2.5亿个整数。

  方案1：采用2-Bitmap（每个数分配2bit，00表示不存在，01表示出现一次，10表示多次，11无意义）进行，共需内存[![clip_image020](https://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image020_thumb.png)](http://images.cnblogs.com/cnblogs_com/youwang/WindowsLiveWriter/1340f2e6fa8e_D5D1/clip_image020_2.png)内存，还可以接受。然后扫描这2.5亿个整数，查看Bitmap中相对应位，如果是00变01，01变10，10保持不变。所描完事后，查看bitmap，把对应位是01的整数输出即可。

  方案2：也可采用上题类似的方法，进行划分小文件的方法。然后在小文件中找出不重复的整数，并排序。然后再进行归并，注意去除重复的元素。

---

* #### 海量数据分布在100台电脑中，想个办法高校统计出这批数据的TOP10。

  方案1：

  s 在每台电脑上求出TOP10，可以采用包含10个元素的堆完成（TOP10小，用最大堆，TOP10大，用最小堆）。比如求TOP10大，我们首先取前10个元素调整成最小堆，如果发现，然后扫描后面的数据，并与堆顶元素比较，如果比堆顶元素大，那么用该元素替换堆顶，然后再调整为最小堆。最后堆中的元素就是TOP10大。

  s 求出每台电脑上的TOP10后，然后把这100台电脑上的TOP10组合起来，共1000个数据，再利用上面类似的方法求出TOP10就可以了。

---

* #### 怎么在海量数据中找出重复次数最多的一个？

  方案1：先做hash，然后求模映射为小文件，求出每个小文件中重复次数最多的一个，并记录重复次数。然后找出上一步求出的数据中重复次数最多的一个就是所求（具体参考前面的题）。

---

越界问题
-----
* #### 两个数相乘，小数点后位数没有限制，请写一个高精度算法。
  输入 string a, string b； 计算string c=ab; 返回 c;
1，纪录小数点在a,b中的位置l1,l2，则需要小数点后移动位置数为l=length(a)+length(b)-l1-l2-2;
2, 去掉a,b中的小数点，（a,b小数点后移，使a,b变为整数）
3, 计算c=ab; （同整数的大数相乘算法）
4，输出c,（注意在输出倒数第l个数时，输出一个小数点。若是输出的数少于l个，就补0）
-----
* #### 整数反转 ：给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。（leet code 7)
  边界条件的考虑而已，记住就好。对接近最大正数和最小负数单独考虑，其他随意。
  
  ```cpp
  class Solution 
  {
  public:
      int reverse(int x) 
      {
          int rev = 0;
          while (x != 0) 
          {
              int pop = x % 10;
              x /= 10;
              if (rev > INT_MAX/10 || (rev == INT_MAX / 10 && pop > 7)) return 0;
              if (rev < INT_MIN/10 || (rev == INT_MIN / 10 && pop < -8)) return 0;
              rev = rev * 10 + pop;
          }
          return rev;
      }
  };
  ```

-----
* #### 两数相除：给定两个整数，被除数 dividend 和除数 divisor。将两数相除，要求不使用乘法、除法和 mod 运算符
  既然不能用乘除法和mod，那使用减法是理所当然的，唯一需要考虑的是边界溢出情况。**但是这种做法会遇到超时问题，因此需要思考更优秀的做法：每次让除数翻倍，多的部分递归去继续从原始除数处理**
  
  1. 避免溢出和对于被除数和除数一些特殊判定以加快运算速度和避免出错
  2. 采用被除数减除数来做到除的效果
  3. 采用除数自增翻倍的思想加快减法的速度
  4. 考虑到符号和可能的溢出，这里将除数和被除数转换为负数进行运算，同时传递运算结果的符号
  
  ```cpp
  class Solution {
  public:
  	int subDivide(int dividend, int divisor, bool minus)
  	{
  		int i = 0, times = 1;
  		int originDivisor = divisor;
  		while (dividend <= divisor && dividend <= 0)
  		{
  			dividend -= divisor;
   			i += times;           
              if (dividend >= divisor)
              {
                  break;
              }
  			divisor += divisor;
  			times += times;
  		}
  
  		if (dividend > originDivisor)
  		{
  			if (minus)
  				return -i;
  			else
  				return i;
  		}
  		else
  		{
  			if (minus)
  				return -i + subDivide(dividend, originDivisor, minus);
  			else
  				return i + subDivide(dividend, originDivisor, minus);
  		}
  	}
  
  	int divide(int dividend, int divisor) {
  		int i = 0, times = 1, left = 0;
  		bool minus = false;
  		
  		if (dividend == INT_MIN)
  		{
  			if (divisor == -1)
  				return INT_MAX;
  			else if (divisor == 1)
  				return INT_MIN;
  			else if (divisor == INT_MIN)
  				return 1;
  		}
  		else if (divisor == INT_MIN)
  			return 0;
          else if (divisor == 1)
              return dividend;
          else if (divisor == -1)
              return -dividend;
  
  		if (dividend < 0 && divisor > 0)
  		{
  			minus = true;
  			divisor = -divisor;
  		}
  		else if (dividend > 0 && divisor < 0)
  		{
  			minus = true;
  			dividend = -dividend;
  		}
          else if (dividend > 0 && divisor > 0)
          {
   			divisor = -divisor;
  			dividend = -dividend;                        
          }
  
  		return subDivide(dividend, divisor, minus);
  	}
  };
  ```
