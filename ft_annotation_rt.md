---
title: "f.annotation_rt"
author: "Brenda KIage"
date: "7/14/2022"
output: html_document
---

### FUNCTIONAL ANNOTATION WORKFLOW

Functional annotation is carried out to depict the roles of expressed genes.
This tutorial is a continuation from [here](https://github.com/Kiage24/EANBIT_RT/blob/main/mt_workflow.Rmd)
Documentation: [here](https://training.galaxyproject.org/training-material/topics/metagenomics/tutorials/metatranscriptomics/tutorial.html)

## Conversion of clean fastq to fasta files
Tool - seqkit
Documentation: [here](https://bioinf.shenwei.me/seqkit/usage/)

```{bash}
seqkit fq2fa *_rev/fwd.fq >> ../mrna_rev/fwd.fasta
```

## Merge the forward and reverse reads.
Tool- fastp

```{bash}
fastp -m -i ./sortmerna/sortme3_other/TIA_othr_fwd.fq -I ./sortmerna/sortme3_other/TIA_othr_rev.fq --merged_out mrna_merge.fastq
```

## Download the swissprot database
from [here](https://ftp.uniprot.org/pub/databases/uniprot/current_release/knowledgebase/complete/uniprot_sprot.fasta.gz)

## Build a diamond database 
Tool - diamond

```{bash}
 diamond makedb  --in uniprot_sprot.fasta -d uniprot_sprot #-d is the prefix to the downloaded swissprot database. 
```

## Blast the merged sequences to the built diamond database

```{bash}
diamond blastx -d ./f_annotationdb/uniprot_sprot -q ./metatranscriptomics/merged_mrna.fasta --outfmt 5 -o mrna_diam.xml --max-target-seqs 1 

```

## Gene annotation(functional analysis) with Omics box:

Omics box carries out visualization of blasted sequences, Gene ontology mapping, GO annotation, functional analysis, visualization of graphs.

## De-novo assembly

tool- Trinity

```{bash}
Trinity --seqType fa --max_memory 10G --left ./mrna_fwd.fasta --right ./mrna_rev.fasta  --CPU 4  --output ./trinity-results

```

## Repeat the blast and assembly using the assembled sequences.

```{bash}
diamond blastx -d ../f_annotationdb/uniprot_sprot -q ./trinity-results/Trinity.fasta --outfmt 5 -o assembled_mrna_diam.xml --max-target-seqs 1 

```

## Visualise the blast and annotate using omics box

