# 从头学习转录组之**FastQC数据质控报告的详细解读**

接下来将会更新几期转录组分析的内容，包括无参转录组，有参转录组。感兴趣可以关注一下。

1. **Basic Statistics（基本信息）** 

![Basic Statistics](https://cdn.liguocheng.top//uPic/geBOmr.png)

- Encoding: 测序平台编号，现在Sanger/ Illumina 1.8以上都是Phred 33编码
- Total sequences: reads数量
- Sequence length: 测序长度
- **%GC: GC含量：**  需要**重点关注**，可以帮助区别物种以及污染等，动物40%-60%都可以。

**2. Per base sequence  quility**：每个测序read上各碱基质量

![Per base sequence  quility**](https://cdn.liguocheng.top//uPic/E347Ae.png)

- 横轴：测序序列的1-40个碱基；正常为100，200或者250bp
- 纵轴：质量得分，score = -10 * log10（error），例如错误率error为1%，那么算出的score就是20
- **箱线图boxplot**：对每一个碱基的质量的统计。箱子上面的须（up bar）为90%分位数，下面的须（down  bar）为10%分位数，箱子中的红线为中位数即50%分位数，箱子顶（upside）为75%分位数，箱子低（downside）为25%分位数。这个boxplot的意义：一是看数据是否具有对称性；二是看数据分布差异，这里主要利用了第二点。bar的跨度越大，说明数据越不稳定。
- 蓝色的线将各个碱基的质量平均值连接起来
- **解释一下：图中蓝线的走势为何先高后低？**因为目前采用的边合成边测序使用的是化学方法促使链由5'向3'延伸，也就是利用了DNA聚合酶。刚开始测序，合成反应还不是很稳定，但是酶的质量还很好，所以会在高质量区域内有一定的波动（这里的1-30bp），后来稳定了，但是随着时间的推移，酶的活力逐渐下降，特异性也变差，所以越往后出错几率越大。
- 一般能用的数据都要求至少Q20，也就是下四分位（10%分位数）的质量值要大于20。
- 二代测序，最好是达到**Q20的碱基要在95%以上（最差不低于90%），Q30要求大于85%（最差也不要低于80%）**

**3. Per sequence quility scores**：每条序列质量统计

![Per sequence quility scores](https://cdn.liguocheng.top//uPic/BQWpCi.png)

- 横轴：质量值0-40，也即是Q值
- 纵轴：每个质量值对应的read数
- 例子中一条read有40bp，  那么其中任意一条的40bp的质量平均值就是这条read的质量值。**只要大部分都高于20说明比较正常**

**4. Per base sequence content**：read各个位置碱基比例分布

![Per base sequence content](https://cdn.liguocheng.top//uPic/BHF4Wa.png)

- 横轴：各碱基位置；纵轴：碱基百分比
- 四条线四种颜色代表四种碱基在每个位置的平均含量（一个位置会测很多reads，然后求一个平均）
- 一般来讲，A=T， C=G， 但是刚开始测序仪不稳定可能出现波动，这是正常的。一般不是波动特别大的，像这里cut掉前5bp就够了。另外如果A、T 或  C、G间出现偏差，只要在**1%以内都是可以接受的**

**5. Per sequence GC content**： 序列平均GC分布

![Per sequence GC content](https://cdn.liguocheng.top//uPic/CorkJk.png)

- 横轴为平均GC含量； 纵轴为每个GC含量对应的序列数量
- 蓝线为系统计算得到的理论分布；红线为测量值，二者越接近越好
- 这里**不相符可能有两个原因：**

1. 前面提到了，GC可以作为物种特异性根据，这里出现了其他的峰有可能**混入了其他物种的DNA**；
2. 目前二代测序基本都会有序列偏向性(所说的  bias)，也就是某些**特定区域会被反复测序**，以至于高于正常水平，变相说明测序过程不够随机。这种现象会对以后的变异检测以及CNV分析造成影响

- 如果出现怎么办？-- 把和我们使用物种GC-content有差异的reads拿出来做blast，来确认是否为某些杂菌 

**6. Per base N content**： N碱基含量分布

![Per base N content](https://cdn.liguocheng.top//uPic/ilK9DY.png)

- N是指仪器不能识别ATCG时给出的结果，一般不会出现。但是如果出现并且量还很大，应该就是**测序系统或者试剂的问题**
- 任意位置的N的比例超过5%，报"WARN"；任意位置的N的比例超过20%，报"FAIL"

**7. Sequence length distribution**： 序列长度统计

![Sequence length distribution](https://cdn.liguocheng.top//uPic/jvtYkU.png)

- 理想情况下，测得的序列长度应该是相等的。实际上总有些偏差
- 当reads长度不一致时报"WARN"；当有长度为0的read时报“FAIL”
- 这里显示大部分都落在40bp这个测序长度上，如果偏差很大就不可信了

**8. Sequence duplication level**：统计序列相同reads的频率

![Sequence duplication level](https://cdn.liguocheng.top//uPic/dgMZz3.png)

- 横坐标是duplication的次数；纵坐标是duplicated reads的数目（红线）

- **解释下横坐标为何会有>10,  >50等出现：**测序的原始数据很大，如果每一条reads都统计，将耗时很久。这里软件只采用了数据的前200,000条reads统计其在全部数据中的重复数目,另外大于75bp的reads只取50bp进行比较。重复数大于10的reads被合并统计成了>10，以此类推...

- unique reads总数（蓝线）作为100%，上图中可以看出，大概仅有2%的uniqe  reads可以观察到两次重复。也就是说，我们这里的**非unique reads占总数比例仅有2%左右**。

- 正常情况下的确，**测序深度越高，越容易产生一定程度的duplication**。高程度的duplication  level，提示我们可能有bias的存在（如建库过程中的PCR duplication）。

  另外和做的项目也有关，一般**转录组测序的结果中duplication  level都比较高**，60-70%都正常，这是因为转录组测的是基因的覆盖深度，各个基因表达量不同，如果某个基因覆盖度较高，那么测的部分就越多，相对应的duplication也会更高；对于**外显子组测序**来说，一般**覆盖度比较一致**，这里出现了duplication就不太正常。

- 当非unique的reads占总数的比例大于20%时，报"WARN"；当非unique的reads占总数的比例大于50%时，报"FAIL“

**9. Overrepresented sequences**：大量重复序列

![Overrepresented sequences](https://cdn.liguocheng.top//uPic/uUOpDz.png)

- 和第8个duplication计算一样，也是取前200,000进行统计，大于75bp只取50bp。
- 发现超过总reads数0.1%的reads时报”WARN“，当发现超过总reads数1%的reads时报”FAIL“

**10. Adapter content**: 接头含量 

![Adapter content](https://cdn.liguocheng.top//uPic/qXhkBk.png)

- 表示序列中两端adapter的情况
- 可以用fastqc自带的去除adapter的程序，也可以用cutadapt或者fastp等去除。

> 对于样本数量较多的项目，可以考虑使用multiqc来批量进行质控。



