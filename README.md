# ![nfcore/test-datasets](docs/images/test-datasets_logo.png)
Test data to be used for automated testing with the nf-core pipelines

## Introduction

nf-core is a collection of high quality Nextflow pipelines. This repository contains various files for CI and unit testing of nf-core pipelines and infrastructure.

The principle for nf-core test data is as small as possible, as large as necessary. Always ask for guidance on the [nf-core slack](https://nf-co.re/join) before adding new test data.

## Documentation

nf-core/test-datasets comes with documentation in the `docs/` directory:

01. [Add a new  test dataset](https://github.com/nf-core/test-datasets/blob/master/docs/ADD_NEW_DATA.md)
02. [Use an existing test dataset](https://github.com/nf-core/test-datasets/blob/master/docs/USE_EXISTING_DATA.md)

## Downloading test data

Due the large number of large files in this repository for each pipeline, we highly recommend cloning only the branches you would use.

```bash
git clone <url> --single-branch --branch <pipeline/modules/branch_name>
```

To subsequently clone other branches[^1]

```bash
git remote set-branches --add origin [remote-branch]
git fetch
```

## Metagenome test dataset: specific information
### Publication
One wastewater dataset published by [Che et al. (2019)](https://doi.org/10.1186/s40168-019-0663-0), SRX5027541, activated sludge, SWH plant,
Illumina HiSeq 4000, Paired, WGS was used to generate the test datasets.

### Assembly and tools tested

Assembly was done with nf-core/mag pipeline (v2.1.1), default settings. The file from SPAdes Assembly SRX5027541_T1_contigs.fasta was used to test the following tools:

1. Fargene
2. Macrel
3. deepARG
4. Antismash
5. Ampir (on faa file produced by prodigal)
This resulted in a list of hits with 120 unique contig names.

### Commands to generate the test dataset
Filter the assembly fasta for the list of hits

```bash
awk '{ if ((NR>1)&&($0~/^>/)) { printf("\n%s", $0); } else if (NR==1) { printf("%s", $0); } else { printf("\t%s", $0); } }' in.fa | grep -Ff patterns.txt - | tr "\t" "\n" > out.fa
```

Filter for all reads except list of hits

```bash
awk '{ if ((NR>1)&&($0~/^>/)) { printf("\n%s", $0); } else if (NR==1) { printf("%s", $0); } else { printf("\t%s", $0); } }' in.fa | grep -v patterns.txt - | tr "\t" "\n" > out.fa
Get a subsample of a fasta file (specify number of contigs)

```bash
seqtk sample nohits.fa 1200 >sub_nohits.fa
Join the hits.fa and sub_nohits.fa to the dataset

1. dataset 01 contains all 120 hits plus 1200 non-hit contigs
2. dataset 02 contains only 60 hits plus 3000 non-hit contigs

In addition prokka annotated files were added for both test-datasets.

## Support

For further information or help, don't hesitate to get in touch on our [Slack organisation](https://nf-co.re/join/slack) (a tool for instant messaging).

[^1]: From [stackoverflow](https://stackoverflow.com/a/60846265/11502856)
