# biol-4386-course-project-a-moy2
biol-4386-course-project-a-moy2 created by GitHub Classroom
# Variant Analysis of Sinistral Potomopyrgus antipodarum

## Introduction
[New Zealand Mud Snails](https://pbs.twimg.com/media/EuXJ7OKXIAA4_su.jpg:large) (Potomopyrgus antipodarum), or Potamos, are a type of freshwater snail that can be obligately sexual or obligately asexual. Genomes of sexual Potamos are diploid, while the genomes of asexual Potamos are triploid or tetraploid. 

Typically, Potamos have a shell that spirals to the right. However, in 2019, the Neiman lab discovered the first ever Potamo with a shell that spirals to the left. What’s even more interesting is that she came from an obligately asexual mother. Therefore, she should be a clone. This snail has since been nicknamed Sinistra (meaning left in latin).
![snail comparison](https://pbs.twimg.com/media/EDFswI4XUAMlZ3M?format=jpg&name=4096x4096)

Sinistra and three of her dextral sisters, that are presumably clones of their mother, have had their triploid genomes sequenced via Illumina. This project seeks to use variant calling between Sinistra and her dextral sisters to identify candidate genes for changes in Potamo shell chirality. Variant calling in the process in which variations in sequence data are identified. 

More details about Sinistra can be found [here](https://legacybiostudios.com/wp-content/uploads/2020/04/Poster-01-1.jpg).

## Figure to Reproduce
From Sinclair et al, 2014:
![Figure 2 from Sinclair et al 2014](https://www.frontiersin.org/files/Articles/105600/fgene-05-00274-r2/image_m/fgene-05-00274-g002.jpg)
"FIGURE 2. HEAT MAP DEMONSTRATIONS OF LONG LINEAR CHROMOSOMAL DIFFERENTIAL TRANSCRIPTION OF A. PHAGOCYTOPHILUM-INFECTED HUMAN NEUTROPHILS VS. UNINFECTED NEUTROPHILS. The human chromosome number is shown at the top. The colors represent estimates of the SD of differential transcription of the window at each position compared to the overall differential transcription> for that chromosome; dark red = -2 SDs; dark green = +2 SDs."

The [paper](https://www.frontiersin.org/articles/10.3389/fgene.2014.00274/full) has a figure that shows a heat map of chromosomal locations of differential transcription. I plan to create a similar figure, but using nucleotide variation instead of transcription, as well as a specific table my PI wants me to create. 

## Methods
Each triploid sisters genome is in 8 different sequencing lanes (24 lanes total) that need to be combined to create one genome file for each sister. Then, the sisters can be compared to Sinistra via variant calling.
1. Reference genome for variant calling will be created Sinistra’s sequencing data using GATK fasta alternate reference maker.
2. Sister genomes will be assembled in reference to Sinistra using bwa and samtools. Job pipeline for each sister:
bwa mem - preforms local alignment, which can be multiple preliminary alignments to a reference sequence
samtools view - converts binary to text-based alignments 
samtools sort - aligns reads so that they are in genome order
picard AddOrReplaceReadGroups - assigns all reads in a file to a single, new read group
samtools merge - merge together multiple sorted alignment files
3. Variants can be called between the genomes using GATK HaplotypeCaller. The positions and candidate genes can be analyzed.
4. Table and chromosomal heat map will be made based on variant positions.

Job files will be uploaded to GitHub, but all other files are/will be >10GB and will be stored on Argon or scratch. 
