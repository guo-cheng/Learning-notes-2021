## Corset转录本聚类分析

我们做完Trinity转录本无参拼接后，发现拼接出来的转录本达到几十万或上百万条，这时候可能就需要Corset这个软件进行转录本聚类。

一般拼接出来的转录本过多可能是由于低表达的基因过多或者是小RNA测序。

### 软件工作原理

> Corset, a method that hierarchically clusters contigs using shared reads and expression, then summarizes read counts to clusters, ready for statistical testing.

![mark](http://cdn.liguocheng.top/blog/20200409/qJcs7PzX0wYF.png?imageslim)

> Corset performs the clustering and counting (dashed box) in a single step
>
> 该软件将组装出来的转录本序列根据reads的比对情况进行聚类，然后再计算per gene reads counts。



### 举个栗子

![mark](http://cdn.liguocheng.top/blog/20200409/dlHugANBuQ4t.png?imageslim)



ATP5J 和 GABPA 两个基因的UTR有一段重叠的部分，当使用无参拼接时,会得到8条转录本,其中3条最长的转录本为拼接引起的假阳性转录本（Cluster b中的转录本）。若使用unigene的方法,根据unigene最长转录本原则,会选取假阳性转录本进行后续分析,这并不准确。而使用Corset聚合“Gene”的方法,可以将这些真实的转录本分离出来（Cluster a 和 Cluster d）。

B图中contig2/contig3的表达量比例在不同处理中表达量相同，所以可以聚类到一起，而contig3/contig4则不能聚到一起。

#### 软件安装

```bash
conda install -c bioconda corset
```

#### 具体参数

 **corset (version 1.07)：**

```
Usage: corset [options] <input bam files>

Input bam files:
	 The input files should be multi-mapped bam files. They can be single, paired-end or mixed
	 and do not need to be indexed. A space separated list should be given.
	 e.g. corset sample1.bam sample2.bam sample3.bam
	 or just: corset sample*.bam

	 If you want to combine the results from different transcriptomes. i.e. the same reads have
	 been mapped twice or more, you can used a comma separated list like below:
	 corset sample1_Trinity.bam,sample1_Oases.bam sample2_Trinity.bam,sample2_Oases.bam ...

Options are:

	 -d <double list> A comma separated list of distance thresholds. The range must be
	                  between 0 and 1. e.g -d 0.4,0.5. If more than one distance threshold
	                  is supplied, the output filenames will be of the form:
	                  counts-<threshold>.txt and clusters-<threshold>.txt
	                  Default: 0.3

	 -D <double>      The value used for thresholding the log likelihood ratio. The default
	                  value will depend on the number of degrees of freedom (which is the
	                  number of groups -1). By default D = 17.5 + 2.5 * ndf, which corresponds
	                  approximately to a p-value threshold of 10^-5, when there are fewer than
	                  10 groups.

	 -m <int>         Filter out any transcripts with fewer than this many reads aligning.
	                  Default: 10

	 -g <list>        Specifies the grouping. i.e. which samples belong to which experimental
	                  groups. The parameter must be a comma separated list (no spaces), with the
	                  groupings given in the same order as the bam filename. For example:
	                  -g Group1,Group1,Group2,Group2 etc. If this option is not used, each sample
	                  is treated as an independent experimental group.

	 -p <string>      Prefix for the output filenames. The output files will be of the form
	                  <prefix>-counts.txt and <prefix>-clusters.txt. Default filenames are:
	                  counts.txt and clusters.txt

	 -f <true/false>  Specifies whether the output files should be overwritten if they already exist.
	                  Default: false

	 -n <string list> Specifies the sample names to be used in the header of the output count file.
	                  This should be a comma separated list without spaces.
	                  e.g. -n Group1-ReplicateA,Group1-ReplicateB,Group2-ReplicateA etc.
	                  Default: the input filenames will be used.

	 -r <true/true-stop/false>
	                  Output a file summarising the read alignments. This may be used if you
	                  would like to read the bam files and run the clustering in seperate runs
	                  of corset. e.g. to read input bam files in parallel. The output will be the
	                  bam filename appended with .corset-reads.
	                  Default: false

	 -i <bam/corset/salmon_eq_classes>  The input file type. Use -i corset, if you previously ran
	                  corset with the -r option and would like to restart using those
	                  read summary files. Use salmon_eq_classes, if you aligned with salmon with
	                  the flag --dumpEq and are passing corset the equivalent class files.
	                  Running with either -i corset or salmon_eq_classes will switch off the -r option.
	                  Default: bam

	 -l <int>         If running with -i corset or salmon_eq_classes, this will filter out a link between contigs
	                  if the link is supported by less than this many reads. Default: 1 (no filtering)

	 -x <int>         If running with -i corset or salmon_eq_classes, this option will filter out reads that
	                  align to more than x contigs. Default: no filtering

Citation: Nadia M. Davidson and Alicia Oshlack, Corset: enabling differential gene expression
          analysis for de novo assembled transcriptomes, Genome Biology 2014, 15:410
```

### 官方示例

#### Raw Data

SRR453566 to SRR453571。推荐使用这个网站下载sra数据[Sra-Explorer](https://sra-explorer.info/)，并用ascp下载。

ascp脚本

```bash
ascp -QT -l 300m -P33001 -i $HOME/.aspera/connect/etc/asperaweb_id_dsa.openssh era-fasp@fasp.sra.ebi.ac.uk:vol1/fastq/SRR453/SRR453568/SRR453568_1.fastq.gz . && mv SRR453568_1.fastq.gz SRR453568_Batch_rep1_1.fastq.gz
```

####   Prepare the reads

> 推荐使用trimmomatic进行数据质控
> P means paired and U means unpaired

**批量处理**

```bash
for filename in *_1.fastq.gz
do
#对于filename（%）向右匹配_的全部内容(*),然后这部分去掉，留下这之前的
base=${filename%_*}
trimmomatic PE -threads 8 -trimlog logfile \
${base}_1.fastq.gz ${base}_2.fastq.gz \
${base}_P1.fastq.gz ${base}_U1.fastq.gz \
${base}_P2.fastq.gz ${base}_U2.fastq.gz \
ILLUMINACLIP:/pub/anaconda3/share/trimmomatic-0.39-1/adapters/TruSeq3-PE.fa:2:30:10 \
SLIDINGWINDOW:5:20 \
LEADING:20 TRAILING:20 \
MINLEN:50
done
```



#### Perform the de novo assembly

在运行trinity之前需要修改reads的header.

fixes the read IDs for Trinity by adding a \1 to the end of the first end's name and a \2 to the end of the 2nd ends name.

```bash
zcat *_P1.fastq.gz | sed 's/ HWI/\/1 HWI/g' > all_1.fastq.gz
zcat *_P2.fastq.gz | sed 's/ HWI/\/2 HWI/g' > all_2.fastq.gz
```

```bash
Trinity --max_memory 10G --seqType fq --left all_1.fastq.gz --right all_2.fastq.gz --full_cleanup --CPU 10
```

The result should be a fasta file of transcripts called `Trinity.fasta`

![trinity.fa](http://cdn.liguocheng.top/blog/20200411/J7asUDrO7eVQ.png?imageslim)

#### Map reads back to the transcriptome

现在需要将clean reads比对回参考转录组上，可以使用salmon或bowtie2



####  Using salmon

To run salmon for corset, start by indexing the assembled transcriptome

```bash
salmon index --index Trinity --transcripts Trinity.fasta
```

Then quantify the reads (it's very important to specify the --dumpEq flag, so the equivalence classes are output)

```bash
FILES=`ls SRR*_P1.fastq.gz | sed 's/_P1.fastq.gz//g'`
for F in $FILES
do
        R1=${F}_P1.fastq.gz
        R2=${F}_P2.fastq.gz
        salmon quant --index Trinity --libType IU --dumpEq -1 $R1 -2 $R2 --output ${F}.out
done
```

**输出结果：**

> quant.sf

![quant.sf](http://cdn.liguocheng.top/blog/20200411/jPxPlmP16JQp.png?imageslim)



> we can use one of salmon's outputs called equivalence classes to cluster transcripts and provide gene-level abundances

![eq_classes.txt](http://cdn.liguocheng.top/blog/20200411/GhKCF6yfVVIl.png?imageslim)

#### Using bowtie2

Build the bowtie index:

```bash
bowtie2-build trinity_out_dir.Trinity.fasta Trinity_bowtie2
```

Map each file to the transcriptome. Here is a short bash script for doing that:

```bash
FILES=`ls SRR*_P1.fastq.gz | sed 's/_P1.fastq.gz//g'`
for F in $FILES ; do
        R1=${F}_P1.fastq.gz
        R2=${F}_P2.fastq.gz
        bowtie2 --all -S Trinity_bowtie2 -1 $R1 -2 $R2 > ${F}.sam  
        samtools view -S -b ${F}.sam > ${F}.bam
done
```



#### Run corset

> The first three sample files (in numeric order) belong to one experimental group in our example while the final three belong to another



```bash
corset -g 1,1,1,2,2,2 -n A1,A2,A3,B1,B2,B3 *.bam
```

or, if we aligned using salmon:

```bash
corset -g 1,1,1,2,2,2 -n A1,A2,A3,B1,B2,B3 -i salmon_eq_classes SRR*/aux_info/eq_classes.txt
```

`clusters.txt` is a tab delimited table with one line for each transcript. The first column contains the transcript ids and the second column is the cluster id it has been assigned to.

![clusters.txt](http://cdn.liguocheng.top/blog/20200411/tPvAntUl1h7M.png?imageslim)



`counts.txt` is also a tab delimited table. It lists the number of reads assigned to each cluster, one per row. There is one columns for each sample.

![counts.txt](http://cdn.liguocheng.top/blog/20200411/5LRGizDKttCQ.png?imageslim)



#### Run edgeR

> [egdeR](http://www.bioconductor.org/packages/release/bioc/html/edgeR.html) is a bioconductor package in R which will be used to test the count data for significant differential expression.

```R
library(edgeR)

count_data<-read.delim("counts.txt",row.names=1)
group<-factor(c(1,1,1,2,2,2))

dge<-DGEList(counts=count_data,group=group)

#Optional: Exclude rows with fewer than 1 read per million in at least 3 samples
#and re-populate dge with filtered records + update library sizes:
keep <- rowSums(cpm(dge)>1) >= 3
dge <- dge[keep,,keep.lib.sizes=FALSE]

dge<-calcNormFactors(dge)
dge<-estimateCommonDisp(dge)
dge<-estimateTagwiseDisp(dge)

results<-exactTest(dge)
topTags(results)
```

> output all the differentially expressed clustered found using edgeR

```R
allTags <- topTags(results,n=dim(results$table)[1])[[1]]
deClusters = rownames(allTags)[allTags$FDR<0.05]
write.table(deClusters,"clusters_of_interest.txt",quote=F,row.names=F,col.names=F)
```



> Extract the contig sequences
>
> It will take the output from corset (clusters.txt), the assembly fasta file, and a file containing a list of clusters of interest, and generate a new fasta file containing only the contigs of interest (with their cluster ID appended to the contig ID)
>
> 这个python脚本用于从cluster.txt中提取感兴趣的序列，软件得github上就有。

```bash
./fetchClusterSeqs.py -i Trinity.fasta -t clusters_of_interest.txt -o contigs_of_interest.fasta -c clusters.txt
```

The cluster naming is of the form `Clusters-X.Y`. The `X` is the *super-cluster* ID. Any transcript which shares even a single read with another transcript will have the same *super-cluster* ID. The `Y` indicates the cluster number within the *super-cluster*

![contigs_of_interest.fasta](http://cdn.liguocheng.top/blog/20200411/KwPnlUXCQh2Q.png?imageslim)



### 参考：

https://github.com/Oshlack/Corset/wiki

https://genomebiology.biomedcentral.com/articles/10.1186/s13059-014-0410-6