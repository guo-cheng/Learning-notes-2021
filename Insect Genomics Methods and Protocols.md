## Insect Genomics Methods and Protocols

<img src="http://cdn.liguocheng.top/blog/20200419/76t679sXqEQL.png?imageslim" alt="mark" style="zoom:80%;" />

### Chapter 1：Arthropod Genome Sequencing and Assembly Strategies

#### Key words :

Genome project strategy, Genome assembly, Genome sequencing, Genomics, Insect
genomes, Oxford nanopore, Pacific biosciences, 10×Genomics

#### 1 Introduction

we have far to go for the remainder of the greater than 1.5 million described species on planet Earth. As
sequencing costs have decreased, researchers focusing on non-model organisms are jumping into genome projects themselves.

(路漫漫其修远兮，吾将上下而求索)

#### 2 Major Steps in a Genome Project

- Strategy Choice.
- Appropriate DNA isolation and DNA sequencing to feed the decided genome assembly choice. 
- Genome assembly, annotation, and submission to public databases.
-  Analysis and possible publication.

1. ==Strategy Choice.==

   **genome size**, which affects final cost, **genome polymorphism**, which affects the probability of high quality assembly or requires methods that address this, and **individual size**, which determines the minimum number of individuals and thus different haplotypes required for DNA isolation and sequencing.

举例子：

一般哺乳动物基因组大小为2.5–3.5 Gb，通常有很低的多态性(1 SNP per 1000 bp in humans—below the sequencing error rate)，而且从单一个体血液中可以获取足够量的DNA。鸟类的基因组大小一般为~1–1.5 Gb，很容易测序和组装。爬行类和两栖类动物的基因组很大，测序的很少。海胆或其他海洋生物有很高的多态性(海胆–2% SNP)，所以它们容易产生短的contigs 以及 scaffold gaps，这就容易导致注释出现不全或错误。

昆虫的体型小，测序时可能需要多个体混合测序，种群数量大意味着个体间有很高的genome sequence polymorphism。而传统的测序组装方式很可能会产生 low contiguity (i.e., contig N50s) and more gaps 产生 poor gene model annotation。

2. ==DNA isolation and sequencing==

- **10X Genomics** assembly strategy：**10-100Kb**,   **55–60×fold** genome coverage with **150 bp paired Illumina reads**, and assembles using the company’s **Supernova** assembly software
- **PB long read** genome assembly strategy:  **20–30 kb**, **50–70×fold** genome coverage using a PB single  **Canu** by **first error correcting** the longest reads using shorter PB reads, and then using **15–25×error corrected long read** data for the final assembly.
- **Illumina De Bruijn** genome assembly strategy:  **multiple sized insert Illumina paired end libraries** (e.g., 500 bp, 2 kb, 5 kb, and 8–10 kb insert sizes) each sequenced to 20–40×genome coverage, fed to a De Bruijn short read genome assembler such as **soap de-novo or allpaths LG**. The final genome coverage might be **150×**. **polymorphism and repeat content** of the species genome.



![GemCode single cell platform](http://cdn.liguocheng.top/blog/20200418/7pmkpFzDCDpG.png?imageslim)

> **10X Genomics原理：**
>
> 通过将来自相同DNA片段（10-100kb）的reads加上相同的barcode，然后在illumina平台上进行测序，从而实现长片段的测序。其基本原理是同一长片段的reads会具有同样的标签，称为linked-reads，利用这些barcode的信息，可将短reads拼接为长reads。这样的linked-reads可进行结构变异检测及单倍型定相的分析。
>
> 从本质上看，10X Genomics只是将Illumina短片段变长，并没有彻底消除Illumina平台GC偏好的问题。归根结底，是否进行PCR扩增是影响10X Genomics和PacBio长片段覆盖度的主要因素。
>
> 应用：单细胞表达；基因组结构变异：基因组*de novo* assembly

3. ==Bacterial contaminants==

Thus it is best to simply **keep interesting symbiont information**. Some amount of **starvation** prior to DNA isolation to allow the digestion of gut contents can be a good idea.(研究肠道微生物共生体时，要注意保留细菌微生物信息)

4. ==Genome assembly==

**RAM and CPU** is really important. Due to the new assemblers merge, assembly times have dramatically decreased over time.

5. ==It doesn’t count unless it is in a public database.==

The potential to be **scooped on publication** of a new genome is low today, instead **collaboration and data sharing** help to understand the mass of data that is a genome and prevent duplication of work.

6. ==Automated annotation==

**Maker, AUGUSTUS and “Just Annotate My genome”** (http://jamg.sourceforge.net) to complete automated annotation.

Quality control using a set of known sequences such as BUSCO or CEGMA is key to measure progress.

7. ==Research group manual curation==

The most common automated annotation errors are the **erroneous merging or splitting of gene models**.

The number of genes in a family of interest needs to be confirmed and can be particularly difficult in gene families with **high sequence diversity such as olfactory receptors**.

8. ==Analysis and publication==

The focus of journals has rightly returned to **biological analysis** and better understanding of **the life style and phenotype** of the species of interest.

Time spent on incremental genome improvement freezes and new annotations is time not investigating biological questions.(要注重生物学问题而不是基因组本身)



### Chapter 2: Genome Size Estimation and Quantitative Cytogenetics in Insects

With care, it is possible using **flow cytometry**(流式细胞仪分析) to create a precise and accurate estimate of the genome size of an insect.



### Chapter 3: Isolation of High Molecular Weight DNA from Insects

For insects, **the least complex tissue is the best**. **Late stage embryos** contain hundreds-thousands of nuclei, but have not completed organogenesis. **Newly hatched larvae or pupa** may also work. Adults and late larva are usually too complex. If possible, remove any food substrates from sample prior to grinding.(成虫最好去除肠道以及生殖器官).

### Chapter 4: Long Range Sequencing and Validation of Insect Genome Assemblies

#### 1 Introduction

The prevalence of complex gene families like **olfactory receptors and P450 genes**, **DNA polymorphisms**
**and transposable elements** in arthropod genomes further obfuscates resolution of the assembly using only short reads.

![Genome assembly and validation workflow](http://cdn.liguocheng.top/blog/20200418/JuCwSxyFyjoB.png?imageslim)



#### 2 Materials

##### Miniasm assembly

The **Miniasm** assembler **does not correct reads** before assembly unlike most other long read assemblers.

It takes as input an all-to-all alignment from **Minimap** and identifies all raw read overlaps. The mappings are then trimmed and modeled into an assembly graph before unitigs are determined from paths through the graph.

We use Miniasm to rapidly generate candidate assemblies from uncorrected raw reads as well as corrected reads.(三代测序的软件一般都采用overlap-layout-consensus算法)

##### Canu assembly

(a) Self-correct the raw long Pacbio reads using the shorter Pacbio reads.

(b) Trim the corrected reads based on the overlap with other corrected reads so that erroneous regions of reads are excised.

(c) Finally assemble the contigs from the corrected and trimmed Pacbio reads.

##### Redundans duplication removal and scaffolding

Highly heterozygous insect genome assemblies may contain a large number of duplicates even after careful genome assembly. **Redundans** is a pipeline to remove duplicated contigs from an assembly, followed by iterative gap closing and scaffolding using paired-end, mate-pair or long reads.



> Although the assembly process includes read correction during the assembly process, it is advisable to perform multiple rounds of error correction after the final assembly is created.

##### Pilon

**Pilon** uses high quality Illumina sequences to **polish Pac-Bio and Oxford Nanopore long reads** assemblies. Illumina reads are aligned to the genome assembly and supplied to Pilon as a BAM file . Illumina reads from unpaired and paired-end libraries can be used as input. The correct base at a position is inferred by consensus and the reference allele is corrected if required. Pilon can also fix small insertion and deletion errors, fill gaps and perform local reassembly in regions where many errors are identified.

#### 3 Notes

- PacBio requires at least **a few micrograms** of high molecular weight DNA before size selection. it is preferable to use individuals from a colony that has been **inbred for many generations**. Another choice is to use **haploid individuals**, if available.(比如一些膜翅目的雄虫)

- Memory and disk space requirements will vary according to genome size and specific tool. Minimum RAM required for most assemblers is **500 Gb–1 Tb**.

- **Coverage** of the genome is another important factor that determines quality of assembly. De novo assemblies require **50–80X coverage** in most cases. **Canu** can be optimized for a high coverage genome assembly.

  > **测序深度**是指测序得到的总碱基数与待测基因组大小的比值，可以理解为基因组中每个碱基被测序到的平均次数，比如100×。
  >
  > **测序覆盖度**是指测序获得的序列占整个基因组的比例。指的是基因组上至少被检测到1次的区域，占整个基因组的比例，比如98%。

- The high error rates have been offset by applying self-correction based on information from shorter reads for PacBio data sets.

- **HaploMerger2** can be used to build haploid sub-assemblies from a heterozygous diploid genome assembly.



### Chapter 5 Using BUSCO to Assess Insect Genomic Resources

> This chapter details the use of the Benchmarking Universal Single-Copy Orthologue (BUSCO) assessment tool to estimate the completeness of **transcriptomes, genome assemblies, and annotated gene sets** in terms of their expected gene content.

- For transcriptomes **the longest open reading frames** are assessed, while for genome assessments, gene models are first built using **ab initio gene prediction with AUGUSTUS** for the potential matches identified using **TBLASTN** searches.
- Matches that meet the BUSCO HMM score cutoffs are classified as **“complete”** if their lengths fall within BUSCO profile length expectations, and if found more than once they are classified as **“duplicated”**. Those that do not meet the length requirements are considered as partial matches and are classified as **“fragmented”**, and BUSCOs without matches that pass the thresholds are classified as **“missing”**.







## Insect genomes: progress and challenges

![mark](http://cdn.liguocheng.top/blog/20200419/o1Iui5EkEp7f.png?imageslim)

### Abstract

1. In 2011, Robinson and colleagues proposed the **‘i5k’** initiative to sequence the genomes of **5000 insects** and other arthropods with **important biological significance or economic value** **before 2017**

2. At the time of preparing this paper, **1219 insect genome-sequencing projects** have been registered
   with the National Center for Biotechnology Information (NCBI): **401 insect species have complete genome** assemblies with varied quality; **the genome annotations of 155 insects** have been publicly released; and over **100 insect genomes** have been published in peer-reviewed journals.

### Insect genome assembly and annotation

#### Insect genome assembly

目前，基因组的测序策略一般为whole genome shotgun strategy (WGS)，它会产生大量短的片段，需要组装。

**组装有两种策略：*de novo* or mapping assembly**

1. De novo genome assembly depends entirely on **overlapping information** between the reads.

**Three de novo assembly algorithms:**

- The first category is based on **overlap/layout/-consensus** between long sequences(OLC)

**CABOG , NEWBLER, SHORTY, EDENA , CELERA**这些软件适合组装中等长度的reads，比如Sanger测序或者三代长reads，不适合二代Illumina短reads

- The second category of assembler uses **De Bruijn graph algorithms**, which are well suited for short reads produced by second-generation sequencing techniques such as the Illumina sequencing platform.

​        包括**SOAPDENOVO, EULER VELVET,and WTDBG**等软件(计算不同的K-mers size是耗时步骤).

- The third category includes software implementing **greedy graph algorithms**.

   **SSAKE, SHARCGS and VCAKE.** 
   
   Many published insect genomes were assembled by CABOG , SOAPdenovo, ALLPATH-LGor ABYSS.

> **Scaffolding method** that can assist assembly: **Hi-C technology**. It is a sequencing-based approach for determining how a genome is folded by measuring **the frequency of contact between pairs of loci**.
>
> It can assist genome assembly to the chromosome level without additional genetic map information.
> Though it **does not generate or improve existing contigs**, this technology is useful for obtaining information with **chromosome-length scaffolds.**

2. mapping assembly first determines the position of reads relative to the reference genome and then assembles the reads into contigs or scaffolds.

***

**影响基因组组装质量的因素：**

**repetitive sequences and heterozygosity**

- A large number of **repetitive sequences** in the genome can cause substantial **ambiguity** in the process of assembling contigs and scaffolds.
  
- **Heterozygosity**, or **allelic variation** in the sequenced individual(s), also greatly **complicates** the problem of genome assembly. 

- **inbred homozygous** individuals (or **haploid males** in the case of Hymenoptera).

- **PLATANUS** and **REDUNDANS**, are reported to improve the assembly quality of heterozygous genomes.

- novel ‘long read’ sequencing technologies, such as Pacific Biosciences (PacBio) and Oxford Nanopore,
  are also contributing to major advances in the quality of genome assembly. some assembly programs
  such as **FALCON , CANU and WTDBG2**.

***

**评估draft genome质量**(需要多种方法评估基因组组装质量)

1. **The size of the genome assembly**

- estimates of genome size: **flow cytometry** or **K-mer analysis**.

- If the assembly is smaller than expected, it is likely because it is **incomplete** or due to **repeat collapse**.
- If the assembly is larger than expected, this often reflects the fact that independent assembly of haplotypes has resulted in **redundancies**.

2. **The correctness of the genome assembly**

- **previously assembled** from independent data (eg BAC sequencing), these can be used to evaluate the correctness of the assembled genome.

- the **congruence** of paired-end or mate-pair reads when mapped to the assembly can inform the quality of genome assembly.

- the distance between the mapped reads should be consistent with the insertion size when constructing the library. This congruence mapping approach has been implemented in software such as **QUAST and REAPR.**

3. **N50 length statistic**

> all scaffolds or contigs are sorted from longest to shortest, then the sequence lengths of each scaffold or contig are sequentially summed. When the accumulated value reaches half of the total assembly length, the length of the corresponding scaffold or contig is defined as the N50.

- ==in some special cases, high N50 might be due to **aggressive misassembly**, which is worthy of further research.==

4. **Single-copy orthologues**

- The **Core Eukaryotic Genes Mapping Approach (CEGMA)** pipeline is one widely used method for implementing this approach (Parra et al., 2007). CEGMA identifies 458 core genes that are highly conserved in eukaryotes and searches for these genes in the assembled scaffolds(**May 18th 2015 - CEGMA is no longer being supported**).

- **Benchmarking Universal Single-Copy Orthologs (BUSCO)**, BUSCO uses a set of **2647 1 : 1 orthologous** genes for arthropods. The quality of genome assembly is reflected by the percentage of these orthologous genes that can be found in the assembled scaffolds.

#### Genome annotation

**structural annotation and functional annotation**

Structural annotation comes first, identifying which regions of the assembly correspond to specific features, such as **genes** (including intron–exon boundaries) and **transposable elements (TEs)**.

Once the structural features are delineated, functional annotation aims to infer the function and identity of genes and other elements, based on **sequence similarities**.

1. **Identifying repeat sequence**

**homology searching** 

Homology searching identifies homologous repeat sequences based on **sequence similarity**. The software **REPEATMASKER** is widely used for this task, in conjunction with the **RepBase** collection of characterized TEs.

***ab initio* prediction**

The ab initio prediction method uses **structural features** of the repetitive sequence to identify novel
repeat sequences. This method has great advantages in predicting **repetitive sequence with distinct structural features**, such as miniature inverted-repeat TEs and long terminal repeats.

**RECON, PILER , REPEATSCOUT , LTR-FINDER and REPEATMODELER** .

2. **Identification of noncoding RNA**

Noncoding RNA is a class of RNA genes that **do not produce protein products** such as transfer RNA
(tRNA), ribosomal RNA, piwi-interacting RNA, micro- RNA (miRNA), small nucleolar RNA, or repeat associated
small interfering RNA.

Software: **MIRDEEP, TRNASCAN, INFERNAL and RNASTRUCTURE**. 

Database: **RNAdb, NONCODE, Rfam, miRBase and snoRNABase.**

3. **Prediction of protein-coding genes**

 (1) identifying homologues of known protein coding genes through **sequence similarity.**

 (2) de novo predicting the protein-coding genes with software developed via **machine learning of protein-coding gene structures.**

 (3) determining the **exonic regions by direct transcriptome sequencing** [eg RNA sequencing (RNAseq)
or expressed sequence tags (ESTs)] and aligning to the assembled scaffolds.

**strengths and weaknesses**

- the protein-coding genes found by homologue searching are typically robust to **false positives**, but **only known protein genes** can be found. 

- De novo prediction can find more candidates but may have **high false-positive rates**.

- Expression evidence by RNA-seq data is typically **the most definitive approach**, but relies heavily on the **quality and quantity** of the transcriptome and the samples chosen for RNA-seq.

AUGUSTUS, EVIDENCEMODELER, GLEAN, EVIGAN, MAKER, JIGSAW and EVIGENE/EVIDENTIALGENE, OMIGA and NCBI’s eukaryotic genome annotation pipeline.



## A chromosome‐level genome assembly reveals the genetic basis of cold tolerance in a notorious rice insect pest, Chilo suppressalis

![mark](http://cdn.liguocheng.top/blog/20200419/UvkOJpmHa8ko.png?imageslim)



### Abstract

二化螟是一种全球性危害水稻的害虫，本研究通过结合 Illumina 以及 PacBio 测序技术.得到二化螟基因组大小为 824.35 Mb, contig N50 为 307 kb，scaffold N50 为 1.75 Mb. 通过Hi-C scaffolding将99.2%的碱基分配到29条染色体上，注释到了15,653 蛋白编码基因。

通过特定基因家族的进化分析，从基因组的角度阐明了二化螟抗寒性(cold tolerance)的分子机制。具体包括以下代谢通路：glucose‐originated glycerol biosynthesis, triacylglycerol‐originated glycerol biosynthesis, fatty acid synthesis and trehalose transport‐intermediate cold tolerance.

### INTRODUCTION

昆虫主要通过两种方式越冬：一种是迁徙，逃离寒冷区域；另外一种是昆虫自身的抗寒机制。

对于后者同样有两种机制：Freeze avoidance and Freeze tolerance

**Freeze avoidance** involves the accumulation of extremely **high levels of cryoprotectants** (chemicals that
prevent/minimize freezing damage at the cellular level) and the synthesis of antifreeze proteins (AFPs). 

**Freeze tolerance** involves specific **ice‐nucleating agents** or proteins (INAs or INPs) that trigger the **freezing of extracellular water**, and **cryoprotectants** that protect the cell membranes of desiccated cells by maintaining a **liquid intracellular space.**

Cryoprotective **dehydration and vitrification** (extreme dehydration) have also been proposed as insect winter survival strategies.

C. suppressalis overwintering strategy involves the accumulation of **glycerol**, the most common insect cryoprotectant.

Enzyme activities associated with glycerol biosynthesis in C. suppressalis have also been reported to be dependent on seasonal changes.



 C. suppressalis draft genome (sequenced solely using the Illumina Solexa platform) was reported, the assembly was hampered by **high heterozygosity** and its contig N50 was only 5.2 kb.

 RNA sequencing (RNA‐seq) analysis of larval responses to low temperatures under diapause (DA)/nondiapause (ND) and cold acclimation (CA)/noncold acclimation (NC).



### MATERIALS AND METHODS

#### Insects

**Pupae(female)** were used to obtain DNA samples for both Illumina and PacBio sequencing, and **newly hatched larvae** were used to obtain DNA samples for Hi‐C sequencing.

#### Genome sequencing

**Illumina sequencing**

Illumina pair-end and mate-paired sequencing was performed

**Pacific Biosciences SMRT sequencing**

One library with insert sizes of approximately 5,000-bp was constructed for the PacBio RS II sequencing system.

<img src="http://cdn.liguocheng.top/blog/20200419/CxnsJeu7IwBp.png?imageslim" alt="mark" style="zoom:80%;" />

**K-mer analysis and estimation of the genome size**
K-mer counts were determined using on the pair-end sequencing data (insert size of the library <2 kb) with Jellyfish and a K-mer setting = 23. a predicted genome size was 788,873,227 bp with 121-fold coverage and a heterozygosity of **0.75%**

<img src="http://cdn.liguocheng.top/blog/20200420/GlS61b4KXN0z.png?imageslim" alt="mark" style="zoom:80%;" />



#### Genome assembly

- de novo assemble
- Scaffold improvements
- Gap closing
- Assembly polishing and filtering

#### Quality assessment

- Mapping rate

- BUSCO assessment

![de novo assembly](http://cdn.liguocheng.top/blog/20200420/qrECjMuCwItL.png?imageslim)

#### Hi‐C scaffolding

- Library preparation and sequencing

30 μg DNA; Newly hatched larvae

- Mapping to draft genome

Unmapped paired‐end reads, singleton reads, multiple mapped reads and PCR duplication were  filtered by an optimized and flexible pipeline **(HiC‐Pro)**.

- Scaffolding

**lachesis** was used to cluster the scaffold into 29 groups according to the agglomerative hierarchical clustering algorithm with default parameters.

#### Genome annotation

Protein coding genes were predicted based on three approaches: ***ab initio* prediction**, **homology-based prediction**, and **transcript-based prediction**

(1) ***ab initio* prediction**. Augustus and GeneMark-ES were used for *ab initio* gene prediction.
GeneMark-ES is a **self-training** algorithm that has been used for gene identification of novel eukaryotic genomes. In order to improve the accuracy of de novo training, Augustus (parameters: --strand = both --species = heliconius_melpomene1 --extrinsicCfgFile = extrinsic.E.XNT.cfg --alternatives-from-evidence = true --hintsfile = hints.gff --allow_hinted_splicesites = atac) was trained using **homologous protein sequences from Bombyx mori and Amyelois transitella along with RNA-Seq data.**
(2) **Homology-based prediction**. The annotated gene sets from ten species, Drosophila melanogaster, Drosophila pseudoobscura, Aedes aegypti, Anopheles gambiae, Bombyx mori, Amyelois transitella, Danaus plexippus, Papilio machaon, Helicoverpa armigera, and Spodoptera litura were aligned to the C.suppressalis genome utilizing **exonerate**  protein2genome with a “--percent 50” parameter to identify exact intron/exon positions.
(3) **Transcript-based prediction.** Hisat2  was used to align the transcriptomic data to the genome and gene information was predicted using Stringtie. The PASA annotation pipeline, which uses GMAP and BLAT  to align Trinity transcript assemblies to the genome. The PASA alignment assemblies can then be used to automatically extract protein coding regions for use in TransDecoder.



> The combined gene structures from the three approaches were integrated via **EvidenceModeler** and filtered for sequences that lacked homologs in the other species or were absent from the RNA-Seq data

<img src="http://cdn.liguocheng.top/blog/20200419/8jC2LX9BGADp.png?imageslim" alt="mark" style="zoom:80%;" />

1. **Annotation assessment**

(1) First, a search of **regions 2 Kb upstream and downstream of the 15,658 genes** found that the vast majority of genes (14,722, 94.02%) lacked ambiguous bases (Ns), indicating that these gene models **are not located near an assembly gap** and thus the gene models are unlikely to be fragments.
(2) Second, we used **BUSCO** software to assess the completeness of genome annotations. The presence of
95.5% of the single copy BUSCO genes suggests that the predicted genes are complete.

2. **Annotation of repetitive elements**
   To identify repeat elements in the C. suppressalis genome, a *de novo* self-specificity repeat library was constructed using **RepeatModeler with RECON** , **RepeatScout , TRF , and NSEG**. Then an *ab initio* repeat library was generated that combined **RepeatMasker** and the **Repbase** database, following the suggested parameter values.

3. **Functional annotation**
   To obtain putative functions of the annotated genes, protein sequences were aligned to the NCBI **NR , NT, and TrEMBL databases** with an E-value cutoff of **1E-10**. We also annotated **Motifs and domains using InterProScan** with publicly available databases including **Gene3D, PRINTS, Pfam, CDD, SMART, MobiDBLite and PROSITE**. Descriptions of gene function, which were retrieved from the InterProScan results, were classified with **Gene Ontology** .

> UniProtKB数据库分Swiss-Prot和TrEMBL两个子库。Swiss-Prot子库中50多万条序列均由人工审阅和注释，而TrEMBL子库中1.4亿多条序列是由核酸序列数据库EMBL中的蛋白质编码序列翻译所得。
>

#### RNA‐seq for larval cold‐response

In both treatments**(DA and ND)**, the fifth‐instar larvae were again divided into two groups, respectively: (a) to **simulate seasonal cooling**, CA larvae were generated by gradually decreasing temperatures 5°C per day over a 5‐day period from the beginning of the final instar to a final temperature of 5°C; (b) NC larvae were maintained at 25°C and collected on the second day of the final instar. 

All CA larvae (DA‐CA and ND‐CA) were then exposed to cold shock at 0 or −5°C for 2 hr. NC larvae (DA‐NC and ND‐NC) were likewise exposed to cold shock at 5, 0 or −5°C for 2 hr.

> DA = diapause; ND = non-diapause; CA = cold training; NC =  non-cold training

<img src="http://cdn.liguocheng.top/blog/20200421/kkv9Ij3naqYF.png?imageslim" alt="mark" style="zoom:80%;" />

#### RNA‐seq analysis

**Quality control and alignment**: **Trimmomatic** to remove adapter and low‐quality sequences, and then aligned to the C. suppressalis genome generated above using **hisat2.** 

**Quantification**: The total number of genome‐mapped reads to gene was quantified using **htseq2** (‐t exon, ‐i gene_id, mode = union) with the annotated C. suppressalis GFF file. 

**Differential expression**: The mapped reads count was used to test differential expression with **DEseq2**. Genes with an **adjusted p value <.05 and |log2 ratio| ≥ 1** in at least one temperature condition were considered to be differentially expressed.



#### Orthology and evolution

Protein sequence data sets from 10 species. 

Redundant alternative splicing events were filtered to generate **a single transcript for each protein set**,
and then all‐against‐all protein comparisons were performed using blastp with a cutoff of E < 10−5.

High‐scoring segment pair (HSP) segments between the same pair of proteins were processed using **orthomcl**, which was followed by homology identification among protein sequences based on bit‐scores to identify final **orthologues, inparalogues and co‐orthologues**.

直系同源基因(orthologues)是通过**物种形成(speciation)**事件产生的; 而旁系同源基因(paralogues)是通过**基因重复(gene duplication)**事件产生的。

如果在物种形成事件之后发生基因重复事件, 那么, 就会出现一对多或者多对多的直系同源关系, 称其为共同直系同源基因 (co-orthologues)。所以在两个物种的基因组中, 最相似的序列仅仅意味着它们可能是直系同源基因, 但是并不能充分证明它们肯定就是直系同源基因。

物种外旁系同源基因(outparalogues) 指由发生在物种形成之前的基因重复事件产生的旁系同源基因；物种内旁系同源基因(inparalogues) 指由发生在物种形成之后的基因重复事件产生的旁系同源基因，有助于我们区分更古老
的旁系同源基因和近期产生的旁系同源基因。

<img src="http://cdn.liguocheng.top/blog/20200419/p8vLQ2qvdWoi.png?imageslim" alt="mark" style="zoom:80%;" />

To estimate **species phylogeny**, protein sequences for 500 single copy conserved orthologues were selected. Multiple protein sequence alignments for each orthologous group were then performed using **muscle** and the conserved blocks were extracted using **gblocks**. Conserved protein blocks with single copies in all species were concatenated to 11 super genes of 199,150 amino acids and used to construct maximum likelihood phylogenetic trees with the JTT model in **phyml**. Statistical support was obtained with **100 bootstrap** replicates.

**mcmctree** (paml 4.8 package) with parameters “‐clock 2 ‐alpha 0.5 ‐model 3” and known time divergence
data in **timetree** (http://www.timetree.org/) were used to estimate **divergence times among species.**

 To examine **gene family expansion and contraction** among species, we used **cafe**  with p < .05 and "‐s" to automatically search for the birth and death parameter (λ) of genes. 

The gene family results from the orthomcl pipeline and the estimated divergence times between species were used as inputs.



### RESULTS AND DISCUSSION

#### Genome assembly



<img src="http://cdn.liguocheng.top/blog/20200420/Tip3s5GDlPQN.png?imageslim" alt="mark" style="zoom:80%;" />

#### Karyotype and chromosome synteny

Hi‐C long‐range scaffolding to obtain chromosomal information. Hi‐C linking information **anchored, ordered and oriented** 25,944 scaffolds to 29 chromosomes by **lachesis** with default parameters.

<img src="http://cdn.liguocheng.top/blog/20200420/IjQvxK6tD5vn.png?imageslim" alt="mark" style="zoom:80%;" />

> The map indicates that **intra-chromosome interactions** were strong while **inter-chromosome interactions** were weak.



Synteny analysis results for C. suppressalis suggest two **fusion events**: Chr1 arising from fusion of T. ni Chr0, Chr13 and Chr14, and Chr2 from fusion of T. ni Chr1, Chr26 and some segments of Chr29.





<img src="http://cdn.liguocheng.top/blog/20200420/XPXMTKa8Tnf3.png?imageslim" alt="Chromosomes involved in major fusion/fission events and translocations" style="zoom:80%;" />



The phylogenetic relationship of Chilo suppressalis was estimated using a maximum likelihood analysis of **a concatenation of 500 single‐copy orthologous protein sequences over 100 bootstrap replicates**.



![Species phylogenetic tree and gene orthology](http://cdn.liguocheng.top/blog/20200420/Rrm5PDRKDszl.png?imageslim)

1:1:1 indicates **absolute single‐copy genes** (absence or duplication in a single genome is not tolerated); N:N:N indicates **multicopy gene paralogues** found across all 11 species (absence of one in a single genome is not tolerated); SS indicates **species‐specific genes** (excluded from other species genomes); SD indicates **multiple copies of species‐specific genes**; ND indicates **species‐specific genes in single copies**.





<img src="http://cdn.liguocheng.top/blog/20200420/GgnR58H5cbgX.png?imageslim" alt="Gene family expansion and contraction" style="zoom:80%;" />

We also analysed gene‐family expansion and/or contraction events in the 11 species and found a **higher frequency of gene contraction** in the Lepidoptera.

#### Genomic basis for cold tolerance





<img src="http://cdn.liguocheng.top/blog/20200420/qSLgnwHRATyT.png?imageslim" alt="Expression profile of transcripts" style="zoom:80%;" />



- two **α‐amylases**(α‐淀粉酶), which catalyse the catabolism of long‐chain carbohydrates to yield maltose, glucose or “limit dextrin”(极限糊精) from amylopectin(支链淀粉).

- a single **β‐1,3‐glucanase**(葡聚糖酶) that breaks down β‐1,3‐glucans(葡聚糖)  to yield glucose or polysaccharides(多糖) made of multiple glucose subunits；and a maltase that catalyses the hydrolysis of maltose to glucose.

- **fructose‐bisphosphate aldolase(FBA)** (二磷酸果糖酶) is essential for producing glycerol biosynthetic **precursors** from glucose by catalysing the reversible reaction that splits fructose‐1,6‐bisphosphate
  (F1,6P2) into the triose phosphates dihydroxyacetone phosphate (DHAP)(磷酸二羟基丙酮) and glyceraldehyde phosphate (GAP)(磷酸甘油醛).

- two glycerol biosynthesis‐related gene homologues: glycerol‐3‐phosphate dehydrogenase **(G3PDH)**(三磷酸甘油醛脱氢酶), which catalyses the reversible redox conversion(可逆氧化还原反应) of DHAP to glycerol 3‐phosphate (G3P)(甘油三磷酸); and the glycerol kinase **(GK)**(甘油激酶) that catalyses the reverse reaction of G3Pase to generate G3P by transferring a phosphate from ATP to glycerol.

> G3PDH transcription is dependent on cold acclimation more than diapause state, and that the GK gene is activated in response to early cold

- Some **lipases** catalyse the hydrolysis of glycerol esters to yield free fatty acids and glycerol.

> glycerol from triacylglycerols requires cold acclimation and a state of diapause similar to that observed for glucose‐dependent glycerol biosynthesis.

- one of the most highly expressed transcripts in DA‐CA larvae was a putative **fatty acid synthase (FAS)**. FAS catalyses the synthesis of the palmitoleic acid precursor, palmitic acid(软脂酸).

- trehalose metabolic pathway‐related include a putative **trehalose transporter (TRET)** orthologue.
- **Trehalase(TREH)** catalyses the conversion of trehalose back to glucose ; given that energy storage is critical for survival in cold environments.
- heat shock protein (Hsp), transferrin(转铁蛋白), catalase(过氧化氢酶), arylphorin(芳基贮存蛋白) and methionine‐rich storage protein(蛋氨酸丰富贮存蛋白). All were significantly upregulated at low temperatures.

## Reference

1. Brown, S.J., Pfrender, M.E. (Eds.), 2019. Insect Genomics: Methods and Protocols, Methods in Molecular Biology. Springer New York, New York, NY. https://doi.org/10.1007/978-1-4939-8775-7
2. Li, F., Zhao, X., Li, M., He, K., Huang, C., Zhou, Y., Li, Z., Walters, J.R., 2019. Insect genomes: progress and challenges. Insect Mol. Biol. 28, 739–758. https://doi.org/10/ggsjwv

3. Ma, W., Zhao, X., Yin, C., Jiang, F., Du, X., Chen, T., Zhang, Q., Qiu, L., Xu, H., Joe Hull, J., Li, G., Sung, W., Li, F., Lin, Y., 2020. A chromosome‐level genome assembly reveals the genetic basis of cold tolerance in a notorious rice insect pest, *Chilo suppressalis*. Mol Ecol Resour 20, 268–282. https://doi.org/10/ggn4kk