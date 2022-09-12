---
title: "Sleepy Doormouse assembly project"
date: "`r Sys.Date()`"
output: bookdown::gitbook
site: bookdown::bookdown_site
---
# Background  
## The garden dormouse  
![](4-garden-dormouse-m-watson.jpg){width=70%}

# Data acquisition  


## PacBio Sequencing  
In contrary to short-read sequencing, the [PacBio](https://www.pacb.com/technology/hifi-sequencing/) sequencers are able to produce long reads of about and around 15Kb. The problem 
with these long reads are that due to the nature of the method of sequencing the 
reads are of low accuracy. This shortcoming calls for a method of reaching a 
higher depth.  
  
The method used by PacBio is borrowed by looking at the [phage Lamda rolling circle replication](https://www.youtube.com/watch?v=CLkPpqMKd6U). 
Where the DNA is circularized and replicated with DNA-Polimerase. 
This creates a circular concatomere with between the repetitions an adapter and 
a primer sequence. This allows for a depth per cononcatomered piece of genome of 
about 3x. After about 5x this allows for an accuracy allike to short sequence 
sequencing.


# Assembly   
## Step 1: Cleaning the raw reads
  
### Steps and cleanup in raw sequence product using HiFi sequencing  
Before assembly of the final genome, it is encouraged the raw reads are rid of the 
adapter sequences remnant of the HiFi sequencing method. These fragments in a final assembly create a partially inaccurate representation. This can create problems with the 
annotation of the genome.  
  
Therefore these adapter sequences need to be removed from the raw reads.  
  
### Kinds of adapter fragments possible in the raw reads.  
![Errorneous contigs](https://media.springernature.com/full/springer-static/image/art%3A10.1186%2Fs12864-022-08375-1/MediaObjects/12864_2022_8375_Fig1_HTML.png?as=webp).  
  
Note that the adapter regions filtered by end-trimming are not present in the 
list of possible vector contaminations.  
  
### Suggested tools for read-quality checking  
* [AlignQC](https://github.com/jason-weirather/AlignQC)  
"AlignQC is a pure-python solution designed to analyze BAM format alignment data and report useful information about the composition and quality of the data including: Mappability, Error rates, Error Patterns, Transcriptome Coverage, Full/Partial-length Detection of Isoforms, Rarefraction Curves, 5' to 3' bias. AlignQC is free to use and modify under the Apache 2.0 license."  
* [longQC](https://github.com/yfukasawa/LongQC)  
"LongQC is a tool for the data quality control of the PacBio and ONT long reads, and it has two functionalities: sample qc and platform qc."
  
### Suggested tools for removing adapter sequences from HiFi sequencing  
Trimming adapter regions from sequence data can be done with a number of tools.  
A few of which are:  
  
* [Trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic)(*3'/5' - trimming*)  
  
* [Cutadapt](https://github.com/marcelm/cutadapt/)(*BLAST-like*)  
  
* [HiFiAdapterFilt](https://github.com/sheinasim/HiFiAdapterFilt)(*BLAST-like*)  
  
The last of which claims to be more memory efficient than Cutadapt although it 
takes longer to process. The last two seem perfectly suited with the latter maybe 
taking the overhand because it claims to be specifically developed for PacBio 
HiFi reads.  
  
### Validating the data after trimming/filtering adapter regions  
It is recommended to check the trimmed data-set with a tool specifically designed 
to find adapter regions in sequence data. [VecScreen](https://www.ncbi.nlm.nih.gov/tools/vecscreen/) is a tool that tests the supplied sequence against 
the [UniVec](https://www.ncbi.nlm.nih.gov/tools/vecscreen/univec/) database. This database contains all known adapter vectors.  
  
It is advised to perform these screening steps before investing resources in further 
completing the genome.  
  
## Step 2: Assembly of the genome  
With the newly polished long reads, the next step in the assembly process can 
begin.
  
During this part of process the genome the will be no reference genome because 
a lot is still unkown about the Garden Doormouse. So *de novo* assembly will be 
performed.  
  
### De novo assemblers for HiFi reads.  
There are a few tools specifically developed or modified for the assembly of long 
read sequence data. Two of which are:  
  
* [hifiasm](https://github.com/chhylp123/hifiasm)  
  
* [HiCANU](https://bioinformaticshome.com/tools/wga/descriptions/HiCANU.html)  
  
* [miniasm](https://github.com/lh3/miniasm) *(BETA)*
  
*The benchmarks seem to indicate that `hifiasm` generally performs a bit better than `HiCANU`.*  

### Metrics and tools for checking the assembly on completeness and quality  
* [N50 & L50 (and others)](https://en.wikipedia.org/wiki/N50,_L50,_and_related_statistics)  
"In computational biology, N50 and L50 are statistics of a set of contig or scaffold lengths. The N50 is similar to a mean or median of lengths, but has greater weight given to the longer contigs. It is used widely in genome assembly, especially in reference to contig lengths within a draft assembly."  
  
* [Busco](https://busco.ezlab.org/)  
"Based on evolutionarily-informed expectations of gene content of near-universal single-copy orthologs, BUSCO metric is complementary to technical metrics like N50."  
*This tool is probably **well** suited for the goal of checking the quality of the 
assembly with this project *
  
* [asmgene](https://github.com/lh3/minimap2)  
Works simularly to busco, but works better with a high quality reference genome.  
*This in our case is not applicable since this is a species has none yet.*  
  
* [QUAST](https://github.com/ablab/quast)  
This is a tool that compares assemblies with each other and performs different 
metrics to them. This works with or without a reference genome. In the case of the 
garden dormouse, a close ancestor has to be selected for some of the metrics to make 
any sense.
  
## Step 3: Marking Transposons  
