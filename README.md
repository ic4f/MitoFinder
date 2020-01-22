# MitoFinder version 1.1		
Allio, R., Schomaker-Bastos, A., Romiguier, J., Prosdocimi, F., Nabholz, B., & Delsuc, F.

<p align="center">
  <img src="/image/logo.png" alt="Drawing" width="250"/>
</p>

Mitofinder is a pipeline to assemble mitochondrial genomes and extract mitochondrial genes from trimmed 
sequencing read data.

# Requirements

This software is suitable for all linux-like systems with gcc installed (Unfortunately, not MAC and Windows < v.10).

# Table of content

1. [Installation guide for MitoFinder](#installation-guide-for-mitofinder)
2. [How to use MitoFinder](#how-to-use-mitofinder)
3. [Detailed options](#detailed-options)
4. [INPUTS](#inputs)
5. [OUTPUTS](#outputs)
6. [UCE annotation](#uce-annotation)
7. [Associated publication](#associated-publication)
8. [How to get reference mitochondrial genomes from ncbi](#how-to-get-reference-mitochondrial-genomes-from-ncbi)


# Installation guide for MitoFinder

## Get MitoFinder

Clone mitofinder from [GitHub](https://github.com/RemiAllio/MitoFinder)

```shell 
git clone https://github.com/RemiAllio/MitoFinder.git
cd MitoFinder
./install.sh

PATH/TO/MITOFINDER/mitofinder -h  
```

or download [master.zip](https://github.com/RemiAllio/MitoFinder/archive/master.zip)  

```shell
wget https://github.com/RemiAllio/MitoFinder/archive/master.zip
unzip master.zip
mv MitoFinder-master MitoFinder
cd MitoFinder
./install.sh

PATH/TO/MITOFINDER/mitofinder -h  
```

## Add mitofinder to your path -> linux

```shell
cd PATH/TO/MITOFINDER/
p=$(pwd)
echo -e "\n#Path to mitofinder \nexport PATH=$PATH:$p" >> ~/.bashrc 
source ~/.bashrc  
```

# How to use MitoFinder

### Assembler
First, you can choose the assembler using the following options:  
-- megahit 				(default: faster)  
-- metaspades			(recommended: a bit slower but more efficient (see associated paper). WARNING: Not compatible with single-end reads)  
-- idba  

## Mitochondrial genome assembly  

TIP: use mitofinder --example to print usual examples of use  

### Trimmed paired-end reads
```shell
mitofinder -j [jobname] -1 [left_reads.fastq.gz] -2 [right_reads.fastq.gz] -r [genbank_reference.gb] -o [genetic_code] -p [threads] -m [memory]   
```

### Trimmed single-end reads
```shell
mitofinder -j [jobname] -s [SE_reads.fastq.gz] -r [genbank_reference.gb] -o [genetic_code] -p [threads] -m [memory]
```

### MitoFinder can be used with your own assembly (one or several contig.s in fasta format)
```shell
mitofinder -j [jobname] -a [assembly.fasta] -r [genbank_reference.gb] -o [genetic_code] -p [threads] -m [memory]
```

### Restart
Use the same command line.  
WARNING: If you want to make the assembly again (for example because it failed) you have to remove the result assembly directory. If not, MitoFinder will skip the assembly step.  

# Detailed options

```shell
usage: mitofinder [-h] [--megahit] [--idba] [--metaspades] [-j PROCESSNAME]
                  [-1 PE1] [-2 PE2] [-s SE] [-a ASSEMBLY] [-m MEM]
                  [-l SHORTESTCONTIG] [-p PROCESSORSTOUSE] [-r REFSEQFILE]
                  [-e BLASTEVAL] [-n NWALK] [--out_gb]
                  [--blastidentitynucl BLASTIDENTITYNUCL]
                  [--blastidentityprot BLASTIDENTITYPROT]
                  [--blastsize ALIGNCUTOFF] [--circularsize CIRCULARSIZE]
                  [--circularoffset CIRCULAROFFSET] [-cove COVECUTOFF]
                  [-o ORGANISMTYPE] [-v] [--example]

Mitofinder is a pipeline to assemble and annotate mitochondrial DNA from
trimmed sequencing reads.

optional arguments:
  -h, --help            show this help message and exit
  --megahit             Use Megahit for assembly. (Default)
  --idba                Use IDBA-UD for assembly.
  --metaspades          Use MetaSPAdes for assembly.
  -j PROCESSNAME, --jobname PROCESSNAME
                        Sequence ID to be used throughout the process
  -1 PE1, --Paired-end1 PE1
                        File with forward paired-end reads
  -2 PE2, --Paired-end2 PE2
                        File with reverse paired-end reads
  -s SE, --Single-end SE
                        File with single-end reads
  -a ASSEMBLY, --assembly ASSEMBLY
                        File with your own assembly
  -m MEM, --max-memory MEM
                        max memory to use in Go (Megahit or MetaSPAdes)
  -l SHORTESTCONTIG, --length SHORTESTCONTIG
                        Shortest contig length to be used in scaffolding.
                        Default = 100
  -p PROCESSORSTOUSE, --processors PROCESSORSTOUSE
                        Number of threads Mitofinder will use at most.
  -r REFSEQFILE, --refseq REFSEQFILE
                        Reference mitochondrial genome in GenBank format
                        (.gb).
  -e BLASTEVAL, --blaste BLASTEVAL
                        e-value of blast program used for contig
                        identification and annotation. Default = 0.000001
  -n NWALK, --nwalk NWALK
                        Maximum number of codon steps to be tested on each
                        size of the gene to find the start and stop codon
                        during the annotation step. Default = 20 (60 bases)
  --out_gb              Do not create annotation output file in GenBank
                        format.
  --blastidentitynucl BLASTIDENTITYNUCL
                        Nucleotide identity percentage for a hit to be
                        retained. Default = 50
  --blastidentityprot BLASTIDENTITYPROT
                        Amino acid identity percentage for a hit to be
                        retained. Default = 40
  --blastsize ALIGNCUTOFF
                        Percentage of overlap in blast best hit to be
                        retained. Default = 30
  --circularsize CIRCULARSIZE
                        Size to consider when checking for circularization.
                        Default = 45
  --circularoffset CIRCULAROFFSET
                        Offset from start and finish to consider when looking
                        for circularization. Default = 200
  -cove COVECUTOFF, --covecutoff COVECUTOFF
                        Cove cutoff for tRNAscan-SE. Default = 7
  -o ORGANISMTYPE, --organism ORGANISMTYPE
                        Organism genetic code following NCBI table (integer):
                        1. The Standard Code 2. The Vertebrate Mitochondrial
                        Code 3. The Yeast Mitochondrial Code 4. The Mold,
                        Protozoan, and Coelenterate Mitochondrial Code and the
                        Mycoplasma/Spiroplasma Code 5. The Invertebrate
                        Mitochondrial Code 6. The Ciliate, Dasycladacean and
                        Hexamita Nuclear Code 9. The Echinoderm and Flatworm
                        Mitochondrial Code 10. The Euplotid Nuclear Code 11.
                        The Bacterial, Archaeal and Plant Plastid Code 12. The
                        Alternative Yeast Nuclear Code 13. The Ascidian
                        Mitochondrial Code 14. The Alternative Flatworm
                        Mitochondrial Code 16. Chlorophycean Mitochondrial
                        Code 21. Trematode Mitochondrial Code 22. Scenedesmus
                        obliquus Mitochondrial Code 23. Thraustochytrium
                        Mitochondrial Code 24. Pterobranchia Mitochondrial
                        Code 25. Candidate Division SR1 and Gracilibacteria
                        Code
  -v, --version         Version 1.1
  --example             Print getting started examples
```

# INPUTS

Mitofinder needs several files to run depending on the method you have choosen (see above):  
- [x] **Reference_file.gb**				containing at least one mitochondrial genome of reference extracted from [NCBI](https://www.ncbi.nlm.nih.gov/)
- [ ] **left_reads.fastq.gz**				containing the left reads of paired-end sequencing  
- [ ] **right_reads.fastq.gz**				containing the right reads of paired-end sequencing  
- [ ] **SE_reads.fastq.gz** 				containing the reads of single-end sequencing  
- [ ] **assembly.fasta**				containing the assembly on which MitoFinder have to find and annotate mitochondrial contig.s   

# OUTPUTS 
### Result folder  

Mitofinder returns several files for each mitochondrial contig found:  
- [x] **[job_name]_final_genes.fasta**				containing the final genes selected from all contigs by MitoFinder   
- [x] **[job_name]_mtDNA_contig.fasta**				containing a mitochondrial contig  
- [x] **[job_name]_mtDNA_contig_final.gff**				containing the final annotation for a given contig  
- [x] **[job_name]_mtDNA_contig_final.tbl**				containing the final annotation for a given contig (Genbank submission format)
- [x] **[job_name]_mtDNA_contig.gb** 				containing the final annotation for a given contig 
- [x] **[job_name]_mtDNA_contig_genes.fasta** 				containing the annoted genes for a given contig    
- [x] **[job_name]_mtDNA_contig.png** 				schematic representation of the annotation of the mtDNA contig    


# UCE annotation
MitoFinder starts by assembling both mitochondrial and nuclear reads. It is only in a second step that mitochondrial contigs are identified and extracted.
MitoFinder thus provides UCE contigs already assembled and the annotation can be done from the following file:  
- **[job_name]link.scafSeq** 	containing all assembled contigs from raw reads. 

To do so, we recommend the PHYLUCE pipeline, which is specifically designed to annotate ultraconserved elements (Faircloth  2015; Tutorial: https://phyluce.readthedocs.io/en/latest/tutorial-one.html#finding-uce-loci).  
You can thus use the file **[job_name]link.scafSeq** and start the pipeline at the **"Finding UCE"** step.  
  
# Associated publication

Allio, R., Schomaker-Bastos, A., Romiguier, J., Prosdocimi, F., Nabholz, B., & Delsuc, F. (2019). MitoFinder: efficient automated large-scale extraction of mitogenomic data in target enrichment phylogenomics. BioRxiv, 685412. https://doi.org/10.1101/685412


# HOW TO GET REFERENCE MITOCHONDRIAL GENOMES FROM NCBI

1. Go to [NCBI](https://www.ncbi.nlm.nih.gov/)  
2. Select "Nucleotide" in the search bar  
3. Search for mitochondrion genomes:  
- [x] RefSeq (if available)  
- [x] Sequence length from 12000 to 20000  
4. Download complete record in GenBank format  

![](/image/NCBI.png)





