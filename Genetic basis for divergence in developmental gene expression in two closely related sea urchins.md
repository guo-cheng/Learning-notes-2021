# Genetic basis for divergence in developmental gene expression in two closely related sea urchins

## Results

### Hybrids reveal gene regulatory divergence between species

Hybrids were generated in one direction only because the reciprocal cross arrests as gastrulae.

Coding sequences contained an average of 2.34% variation within species and 5.49% divergence between species.

Identified >230,000 species-informative sites (12.9% of all single-nucleotide differences) at each stage; on this basis, we could confidently assign 81.9% of reads in hybrid transcriptomes as originating from one or the other parental genome using **HyLiTE**.

Variation among biological replicates is smaller than stage and species effects, and indicates generally high accuracy in inferring parent-of-origin for transcripts in hybrids.

<img src="http://cdn.liguocheng.top/blog/20200422/fv2Gv91HBnt9.png?imageslim" alt="Development and gene expression in parents and hybrids" style="zoom:80%;" />

> ==**a.**== We collected mRNA from hybrids and parental crosses at the three time points shown, then carried out RNA-seq. For the hybrid samples, we used **fixed genetic differences between species** to assign reads to the H. erythrogramma or H. tuberculata genome to measure allele-specific expression.
>
> ==**b,**== Proportion of allele-specific reads in hybrids. At all three stages, most reads in coding sequences could be assigned to one parental genome or the other based on **species-informative sites**.
>
> ==**c,**== Frequency spectrum of allelic ratios by gene. RNA-seq reads derived from maternal alleles outnumber those from paternal alleles for most genes at the **blastula stage**, probably a consequence of maternal mRNA. The subsequent reduction in allelic bias at later stages probably reflects activation of the zygotic genome and turnover of maternal transcripts.
>
> ==**d,**== PCA of transcriptomes from parental crosses, hybrids and alleles within hybrids. The first principal component separates **sequential developmental stages** and the second separates **parental species with hybrids in between**. Alleles in hybrids cluster near the inferred parental transcriptomes, indicating correct parent-of-origin inference for most reads.



### Maternal effects dominate transcription in early embryos then rapidly decline

Most transcripts in hybrids at the blastula stage were assigned to the maternal genome.

33.9% of unambiguous cases classified as maternal-dominant in blastulae and dropping to 12.8% in gastrulae and 12.4% in larvae.

These results is that mRNA and protein molecules originally stored in the egg strongly influence transcription during early development but that these maternal effects decline rapidly, with maternal and paternal influences becoming nearly equal in larvae.



<img src="http://cdn.liguocheng.top/blog/20200422/DSqYsFnhbkD1.png?imageslim" alt="Developmental changes in genetic basis for expression divergence" style="zoom:80%;" />

> If expression in hybrids is not statistically distinguishable from He but is statistically distinguishable from Ht, mode of inheritance is classified as **He** dominant. The reverse case is classified as **Ht** dominant. If expression in hybrids is intermediate between He and Ht, the case is classified as **additive (codominant)**. If expression in hybrids is higher than both parents, the case is classified as **overdominant**, while if lower than either parent it is classified as **underdominant.** Genes not differentially expressed between the parental crosses and are classified as **conserved.**

### cis contributions moderately exceed trans contributions in expression divergence.

the hybrid trans environment has less influence on transcript abundance than the cis genetic differences between the two species.(Fig. 1d)

linkage disequilibrium：在群体遗传学中， 连锁不平衡性是指在两个或者多个位点上的非随机关联性，这些位点既可能在同一条染色体上，也可以在不同的染色体上。







### Activation of the zygotic genome is broadly delayed in H. erythrogramma.

<img src="http://cdn.liguocheng.top/blog/20200423/8nzEOhuuUQ4k.png?imageslim" alt="Inferred genetic basis for expression divergence among species" style="zoom:80%;" />





### The genetic architecture underlying expression divergence changes across development









### Changes in gene expression may underlie the evolution of derived life history traits









## Discussion









## Methods

### Library preparation and data production

Libraries were sequenced on an HiSeq 2000 (Illumina) as 101-base pair (bp) single-end reads.

### Quality control, trimming and alignment of reads

After examining the quality of raw reads using FastQC, the first 12 nucleotides of raw reads were clipped using seqtk. Reads were further trimmed to remove low-quality bases using Trimmomatic, with the parameters ‘trimmomatic SE -threads 8 -phred33 SLIDINGWINDOW:4:5 TRAILING:5’.



### Measuring allele-specific expression









