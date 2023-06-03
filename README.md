# biol-4386-course-project-a-moy2
biol-4386-course-project-a-moy2 created by GitHub Classroom
# Variant Analysis of Sinistral Potomopyrgus antipodarum

## Introduction
[New Zealand Mud Snails](https://pbs.twimg.com/media/EuXJ7OKXIAA4_su.jpg:large) (Potomopyrgus *antipodarum*), or Potamos, are a type of freshwater snail that can be obligately sexual or obligately asexual. Genomes of sexual Potamos are diploid, while the genomes of asexual Potamos are triploid or tetraploid. Potamos are gaining popularity as a model for transitions to asexuality as different populations have seperate transitions to an obligately asexual state. This also allows us to generate multiple seperate estimates of *de novo* mutation, which is important as we lack pedigree-based estimates of *de novo* mutation rates. 

Typically, Potamos have a shell that spirals to the right. However, in 2019, the Neiman lab discovered the world's first ever Potamo with a shell that spirals to the left. What’s even more interesting is that she came from an obligately asexual mother. Therefore, she should be a clone. This snail has since been nicknamed Sinistra (meaning left in latin).
![snail comparison](https://pbs.twimg.com/media/EDFswI4XUAMlZ3M?format=jpg&name=4096x4096)
Sinistra has since produced sinistral offspring, indicating that the chirality phenotype is heritable, and therefore should be the result of a SNPor other gene mutation. 

The specific aims of the Potamomics research group are to generate annotated genome assemblies from multiple clonal families of Potamos, and infe the rate and spectrum of new mutations in clonal (asexual) lineages. My team specfically uses Sinistra and her family to accomplish these aims.
Sinistra and three of her dextral sisters, that are presumably clones of their mother, have had their triploid genomes sequenced via Illumina. My team has produced high-covergae (~100x) whole-genome sequencing data for these snails. This project seeks to use variant calling between Sinistra and her dextral sisters to view the spectrum of mutations between them and potentially identify candidate genes for changes in Potamo shell chirality.
 
More details about Sinistra can be found [here](https://legacybiostudios.com/wp-content/uploads/2020/04/Poster-01-1.jpg).

## Figure to Reproduce
From Sinclair et al, 2014:
![Figure 2 from Sinclair et al 2014](https://www.frontiersin.org/files/Articles/105600/fgene-05-00274-r2/image_m/fgene-05-00274-g002.jpg)
"FIGURE 2. HEAT MAP DEMONSTRATIONS OF LONG LINEAR CHROMOSOMAL DIFFERENTIAL TRANSCRIPTION OF A. PHAGOCYTOPHILUM-INFECTED HUMAN NEUTROPHILS VS. UNINFECTED NEUTROPHILS. The human chromosome number is shown at the top. The colors represent estimates of the SD of differential transcription of the window at each position compared to the overall differential transcription> for that chromosome; dark red = -2 SDs; dark green = +2 SDs."

The [paper](https://www.frontiersin.org/articles/10.3389/fgene.2014.00274/full) has a figure that shows a heat map of chromosomal locations of differential transcription. I plan to create a similar figure, but using nucleotide variation instead of transcription, as well as a specific table my PI wants me to create. 

## Methods
Each triploid sisters genome is in 8 different sequencing lanes (24 lanes total) that need to be combined to create one genome file for each sister. Then, the sisters can be compared to Sinistra via variant calling. Code examples shown will be for sister 1, but the process was repeated for all 3 sisters. 
1. Reference genome for variant calling will be created Sinistra’s sequencing data using GATK fasta alternate reference maker.
```
module load gatk/4.1.8.1_gcc-9.3.0
 gatk FastaAlternateReferenceMaker \
   -R ~/biol-4386-course-project-a-moy2/sequences/mudsnail_poly_assem_14032018.arrow12.pilon7.K55.renamed.fasta \
   -O ~/biol-4386-course-project-a-moy2/sequences/feb_sinistra_reference.fasta \
   -V ~/biol-4386-course-project-a-moy2/sequences/psin.sorted.RG.RD.RO.RC.SNP.filt.final.vcf
Bwa index feb_sinistra_reference.fasta
samtools faidx feb_sinistra_reference.fasta
``` 
2. Sister genomes will be assembled in reference to Sinistra using bwa and samtools. Job pipeline for each sister:
bwa mem - preforms local alignment, which can be multiple preliminary alignments to a reference sequence
samtools view - converts binary to text-based alignments 
samtools sort - aligns reads so that they are in genome order
```
bwa mem -t 24 ~/biol-4386-course-project-a-moy2/sequences/feb_sinistra_reference.fasta ~/reads/sister1/Sister1_lane1_20210213000_S30_L001_1.trim.fastq.gz ~/reads/sister1/Sister1_lane1_20210213000_S30_L001_2.trim.fastq.gz  > ~/biol-4386-course-project-a-moy2/data/A-S1L1.sam
samtools view -hb -F 260 ~/biol-4386-course-project-a-moy2/data/A-S1L1.sam > ~/biol-4386-course-project-a-moy2/data/A-S1L1.bam
samtools sort -@ 80 ~/biol-4386-course-project-a-moy2/data/A-S1L1.bam -o ~/biol-4386-course-project-a-moy2/data/A-S1L1.sort.bam
bwa mem -t 24 ~/biol-4386-course-prroject-a-moy2/sequences/feb_sinistra_reference.fasta ~/reads/sister1/Sister1_lane2_20210213000_S62_L002_1.trim.fastq.gz ~/reads/sister1/Sister1_lane2_20210213000_S62_L002_2.trim.fastq.gz  > ~/biol-4386-course-project-a-moy2/data/A-S1L2.sam
samtools view -hb -F 260 ~/biol-4386-course-project-a-moy2/data/A-S1L2.sam > ~/biol-4386-course-project-a-moy2/data/A-S1L2.bam
samtools sort -@ 80 ~/biol-4386-course-project-a-moy2/data/A-S1L2.bam -o ~/biol-4386-course-project-a-moy2/data/A-S1L2.sort.bam
bwa mem -t 24 ~/biol-4386-course-project-a-moy2/sequences/feb_sinistra_reference.fasta ~/reads/sister1/Sister1_lane3_20210213000_S94_L003_1.trim.fastq.gz ~/reads/sister1/Sister1_lane3_20210213000_S94_L003_2.trim.fastq.gz  > ~/biol-4386-course-project-a-moy2/data/A-S1L3.sam
samtools view -hb -F 260 ~/biol-4386-course-project-a-moy2/data/A-S1L3.sam > ~/biol-4386-course-project-a-moy2/data/A-S1L3.bam
samtools sort -@ 80 ~/biol-4386-course-project-a-moy2/data/A-S1L3.bam -o ~/biol-4386-course-project-a-moy2/data/A-S1L3.sort.bam
bwa mem -t 24 ~/biol-4386-course-project-a-moy2/sequences/feb_sinistra_reference.fasta ~/reads/sister1/Sister1_lane4_20210213000_S126_L004_1.trim.fastq.gz ~/reads/sister1/Sister1_lane4_20210213000_S126_L004_2.trim.fastq.gz  > ~/biol-4386-course-project-a-moy2/data/A-S1L4.sam
samtools view -hb -F 260 ~/biol-4386-course-project-a-moy2/data/A-S1L4.sam > ~/unc_sinistra_vc/data/A-S1L4.bam
samtools sort -@ 80 ~/biol-4386-course-project-a-moy2/data/S1L4.bam -o ~/biol-4386-course-project-a-moy2/data/A-S1L4.sort.bam
```
picard AddOrReplaceReadGroups - assigns all reads in a file to a single, new read group
samtools merge - merge together multiple sorted alignment files
``` 
picard AddOrReplaceReadGroups INPUT= ~/biol-4386-course-project-a-moy2/data/A-S1L1.sort.bam OUTPUT= ~/biol-4386-course-project-a-moy2/data/TagS1L1.sort.bam RGLB=library RGPL=illumina RGPU=barcode RGSM=sinistra
TagS1L1.sort.bam TagS1L2.sort.bam TagS1L3.sort.bam TagS1L4.sort.bam > S1bams.fofn
samtools merge -r -f -@ 80 -b ~/biol-4386-course-project-a-moy2/data/S1bams.fofn -O BAM ~/biol-4386-courrse-project/data/sis1_merged.bam
```
3. Variants can be called between the genomes using GATK HaplotypeCaller. The positions and candidate genes can be analyzed.
```
gatk --java-options -Xmx8g HaplotypeCaller -R ~/biol-4386-course-project-a-moy2/sequences/feb_sinistra_reference.fasta -I ~/biol-4386-course-project-a-moy2/sequences/Sis1merge.bam -ERC GVCF -O ~/biol-4386-course-project-a-moy2/vcfs/S1_tag.merged.g.vcf
```
4. Data Analysis:
Data was anlyzed using VCFstats to create graphs and VCFToolz and BCFtools to generate statistics.
```
vcfstats --vcf ~/biol-4386-course-project-a-moy2/sequences/vcfstats --vcf ~/biol-4386-course-project-a-moy2/sequences/S1_tag.merged.g.vcf --outdir ~/analysis --formula 'COUNT(1) ~ CONTIG' --title 'Number of variants on each chromosome'
```
```
vcftoolz compare ~/biol-4386-course-project-a-moy2/sequences/S1_tag.merged.g.vcf ~/biol-4386-course-project-a-moy2/sequences/S2_tag.merged.g.vcf ~/biol-4386-course-project-a-moy2/sequences/S3_tag.merged.g.vcf > summary.txt
vcftoolz count ~/biol-4386-course-project-a-moy2/sequences/S1_tag.merged.g.vcf
```
```
bcftools stats ~/biol-4386-course-project-a-moy2/sequences/S1_tag.merged.g.vcf
```
Job files will be uploaded to GitHub, but all other files are >10GB and will be stored on Argon or scratch due to their large size and unpublished nature. 
## Results
#### Statistics from BCFtools
##### Sister 1
```
number of samples:      1
number of records:      87208437
number of no-ALTs:      76159169
number of SNPs: 8123631
number of MNPs: 0
number of indels:       3165434
number of others:       0
number of multiallelic sites:   11049268
number of multiallelic SNP sites:       7883834

# ST, Substitution types:
# ST    [2]id   [3]type [4]count
ST      0       A>C     635456
ST      0       A>G     903119
ST      0       A>T     666840
ST      0       C>A     633433
ST      0       C>G     304857
ST      0       C>T     1008551
ST      0       G>A     1007755
ST      0       G>C     301016
ST      0       G>T     631953
ST      0       T>A     678983
ST      0       T>C     901057
ST      0       T>G     636381
```
##### Sister 2
```
number of samples:      1
number of records:      82896525
number of no-ALTs:      71856149
number of SNPs: 8107713
number of MNPs: 0
number of indels:       3189327
number of others:       0
number of multiallelic sites:   11040376
number of multiallelic SNP sites:       7851049

 ST, Substitution types:
# ST    [2]id   [3]type [4]count
ST      0       A>C     623550
ST      0       A>G     905302
ST      0       A>T     666495
ST      0       C>A     634578
ST      0       C>G     306806
ST      0       C>T     1013958
ST      0       G>A     1013992
ST      0       G>C     302514
ST      0       G>T     633083
ST      0       T>A     678250
ST      0       T>C     904449
ST      0       T>G     623864
```
##### Sister 3
```
number of samples:      1
number of records:      67476617
number of no-ALTs:      56541741
number of SNPs: 7921945
number of MNPs: 0
number of indels:       3264190
number of others:       0
number of multiallelic sites:   10934876
number of multiallelic SNP sites:       7257776

# ST    [2]id   [3]type [4]count
ST      0       A>C     576749
ST      0       A>G     903165
ST      0       A>T     651151
ST      0       C>A     606482
ST      0       C>G     303565
ST      0       C>T     1007030
ST      0       G>A     1007049
ST      0       G>C     299416
ST      0       G>T     606542
ST      0       T>A     658414
ST      0       T>C     903405
ST      0       T>G     577480
```
#### Statistics from VCFtoolz
##### Sister 1
```
1 samples
 3799668 positions
87208437 calls
 6540322 heterozygous calls
80668115 homozygous calls
77492871 reference calls
 7800670 snp calls
 2945703 indel calls
       0 other variant calls
   18173 missing calls
       0 calls PASS filter
       0 calls failing any filter
  ```
##### Sister 2
```
 1 samples
 3766386 positions
82896525 calls
 6611896 heterozygous calls
76284629 homozygous calls
73101796 reference calls
 7859962 snp calls
 2971378 indel calls
       0 other variant calls
   18138 missing calls
       0 calls PASS filter
       0 calls failing any filter
 ```
##### Sister 3
```
1 samples
 3631943 positions
67476617 calls
 6951904 heterozygous calls
60524713 homozygous calls
57206208 reference calls
 8093402 snp calls
 3421634 indel calls
       0 other variant calls
       0 missing calls
       0 calls PASS filter
       0 calls failing any filter
```
#### Graphs created with VCFstats
##### Sister 1 vs Sinistra
![Imgur](https://i.imgur.com/iLhl12q.png)
##### Sister 2 vs Sinistra
![Imgur](https://i.imgur.com/jxfEGPO.png)
##### Sister 3 vs Sinistra
![Imgur](https://i.imgur.com/JcuXGOs.png)
##### Combined Sisters vs Sinistra
![Imgur](https://i.imgur.com/QEDeRQ9.png)

## Challenges 
I found that many programs typically used to visiualize SNP data were unable to process my VCF files, as I am working with a non-model asexual triploid, and many programs (such as IGV, VIVA, and vcfR - which crashed my entire computer) that I tried would crash or kill the process. After reading the documentation, this is not unexpected since I am working with an unusal and novel dataset. However, this created a challenge for me to find a program to analyze the data, as I am not stong enough in coding yet to write one myself.

Another challenge is the amount of SNPs found. Since this is an unfolding project, my team did not know how many SNPs would be found, but the amount found far surpassed our exprectations. This makes the analysis much trickier than if there were only a handful of SNPs.

Due to both of these issues, the postdoc in charge of the project advised that if I could not find a suitable program to make a SNP heatmap, to not waste time on it, as it would likely highlight the entire genome anyway. I do not see this as a failure, but just a result of the experimental process and the fact that this is novel data from a novel snail. 

An additional challenge in data analysis is that we are not sure how closely related Sinistra is to her sequenced clonal sisters. We know that they are no more than 2-3 generations apart, but being unsure of the difference does create a challenge in analyzing the spectrum of variants, and my team is working on a plan to work around this issue.

An inherent issue with bioinformatic anaylsis, espeically working on Argon which my team mainly does, is wait times - both in the queue and waiting for the job to finish. This issue is exacerbated by the fact that the files I'm working with around all around 10GB. This just means that it takes a longer time for jobs to be run, versus working with a smaller dataset or not in a queue system.

## Discussion
Comparing the statistics generated by BCFtools and VCFtoolz, they are fairly similar, and both show that there are a great amount of both SNPs and substitutons between Sinistra and her sisters. For both sisters 1 and 2, BCFtools found a higher number of SNPs and indels, averaging 247,753 more SNPs and 218,840 more indels. For sister 3, BCFtools and VCFtoolz found the same number of indels, but VCFtoolz found a slightly higher number of SNPs (171,457 more). VCFtoolz shows the heterozygous and homozygous calls, while BCFtools shows multiallelic sites. I suspect the differneces between the two programs have to do with their parameters, as well as how well they handle polyploid genomes. Further statistical analysis will have to be done to veryify the exact number of SNPs and indels.

When looking at the graphs generated by VCFstats, both sister 1 and 2 have their highest peaks around 600,000 SNPs in a contig while sister 3 peaks at 400,000. This could indicate that sister 3 is more closely related to Sinistra than sisters 1 and 2, which could be a useful insight for further data analysis. It is also shown that there are a higher amount of SNPs called toward the middle of the genome, which makes sense since we assume the ends to be regulatory and telomeric. 

