![Dicerna](https://github.com/dicerna/rnaseq/blob/dicerna/assets/dicerna.logo.svg)


[![Nextflow](https://img.shields.io/badge/nextflow%20DSL2-%E2%89%A521.10.3-23aa62.svg?labelColor=000000)](https://www.nextflow.io/)
[![run with conda](http://img.shields.io/badge/run%20with-conda-3EB049?labelColor=000000&logo=anaconda)](https://docs.conda.io/en/latest/)
[![run with docker](https://img.shields.io/badge/run%20with-docker-0db7ed?labelColor=000000&logo=docker)](https://www.docker.com/)
[![run with singularity](https://img.shields.io/badge/run%20with-singularity-1d355c.svg?labelColor=000000)](https://sylabs.io/docs/)

# Introduction

We are using **nf-core/rnaseq** pipeline to analyse RNA sequencing data obtained from organisms with a reference genome and annotation.

The pipeline is built using [Nextflow](https://www.nextflow.io), a workflow tool to run tasks across multiple compute infrastructures in a very portable manner. It uses Docker/Singularity containers making installation trivial and results highly reproducible. The [Nextflow DSL2](https://www.nextflow.io/docs/latest/dsl2.html) implementation of this pipeline uses one container per process which makes it much easier to maintain and update software dependencies. Where possible, these processes have been submitted to and installed from [nf-core/modules](https://github.com/nf-core/modules) in order to make them available to all nf-core pipelines, and to everyone within the Nextflow community!

## Online videos

A short talk about the history, current status and functionality on offer in this pipeline was given by Harshil Patel ([@drpatelh](https://github.com/drpatelh)) on [8th February 2022](https://nf-co.re/events/2022/bytesize-32-nf-core-rnaseq) as part of the nf-core/bytesize series.

You can find numerous talks on the [nf-core events page](https://nf-co.re/events) from various topics including writing pipelines/modules in Nextflow DSL2, using nf-core tooling, running nf-core pipelines as well as more generic content like contributing to Github. Please check them out!

## Pipeline summary
# The nextflow tower public hosted server is used to run this pipeline.

To run the pipeline, go to [tower.nf](https://tower.nf).

# Files prepared to run the pipeline.

1. Transfer all the fastq.gz files to aws s3 use aws s3 cp command line.
2. generate sample sheet as describe in the [nf-core rnaseq usage document](https://nf-co.re/rnaseq/3.3/usage). In the samplesheet, you provde the sample name, the path to fastq file(s), and the strandness information. transfer this file to aws s3 bucket.
3. Now you are all set to run the pipeline at nf-tower: tower.nf
4. In the nf-tower, you will need to inut the following. Most other parameters have defaults, but you can change.

- inut: the path to sample sheet
- outdir: the results directory.  **don't forget the last slash /**
- genome.

**Genomes names you can use**

- **humuan:** ensembl: 'human_ensembl_v104_2021_March', refseq: 'human_refseq_release_109_2021_May', or GRCh38', 'GRCh37' from iGenome
- **Tested 40 human samples with star_rsem, some samples run very slow at the 2nd pass. This leads to huge increase of AWS computing cost.** I googled the reason. I found [this](https://github.com/alexdobin/STAR/issues/733). Shoudl monitor the running for the public data.

- **mouse:** ensembl: 'GRCm38_v102'

- **crab eating monkey mf6:** ensembl: 'mf6_ens_v104'

- **crab eating monkey mf5: ensembl:** 'mf5_ucsc_refseq', 'mf5_ucsc_ensGene', 'mf5_ens_v102', refseq: 'mf5_refseq_r101', 'Macaca_fascicularis_MFA1912RKSv2_ERCC92_refseq_v102'

- **The mouse GRCm39 ensembl v104: GRCm39_v104 was also added. It was tested in GA01, but not being tested in AWS batch environment. Use with your own risk.**


```
{
    "input":"s3://dicerna-etl/test/monkey/sample_sheet_full.csv",
    "outdir":"s3://dicerna-etl/test/monkey_dicerna/results_star/",  
    "genome":"mf5_ens_v102",
    "aligner":"hisat2"
}
```
Those customized genomes were hosted in aws s3 for dicerna use. We can upgrade as we want. The GRCm38_v102, mf6_ens_v104, ... can be used in the genome field.
Currently, there is no working human Refseq reference. The stringtie fails because of the GTF format issues.

```
      // added this by Zhe, requested by Chris
      'Macaca_fascicularis_MFA1912RKSv2_ERCC92_refseq_v102' {
        fasta = 's3://dicerna-genomes/Macaca_fascicularis_MFA1912RKSv2_ERCC92_refseq_v102/GCF_012559485.2_MFA1912RKSv2_genomic_ERCC92.fna.gz'
        gtf  = 's3://dicerna-genomes/Macaca_fascicularis_MFA1912RKSv2_ERCC92_refseq_v102/GCF_012559485.2_MFA1912RKSv2_genomic_ERCC92_fixed.gtf.gz'
      }
      
      'rn7_ucsc_refseq' {
        fasta = 'ftp://hgdownload.soe.ucsc.edu/goldenPath/rn7/bigZips/rn7.fa.gz'
        gtf  = 'ftp://hgdownload.soe.ucsc.edu/goldenPath/rn7/bigZips/genes/ncbiRefSeq.gtf.gz'
      }
      'mf5_ucsc_refseq' {
        fasta = 'ftp://hgdownload.cse.ucsc.edu/goldenPath/macFas5/bigZips/macFas5.fa.gz'
        gtf  = 'ftp://hgdownload.cse.ucsc.edu/goldenPath/macFas5/bigZips/genes/macFas5.ncbiRefSeq.gtf.gz'
      }
      'mf5_ucsc_ensGene' {
        fasta = 'ftp://hgdownload.cse.ucsc.edu/goldenPath/macFas5/bigZips/macFas5.fa.gz'
        gtf  = 'ftp://hgdownload.cse.ucsc.edu/goldenPath/macFas5/bigZips/genes/macFas5.ensGene.gtf.gz'
      }
      'hg38_ensGene' {
       fasta = 's3://dicerna-genomes/hg38_ncbiRefSeq/hg38.fa.gz'
       gtf  = 's3://dicerna-genomes/hg38_ncbiRefSeq/hg38.ensGene.gtf.gz'
      }
      'hg38_refseq' {
       fasta = 's3://dicerna-genomes/hg38_ncbiRefSeq/hg38.fa.gz'
       gtf  = 's3://dicerna-genomes/hg38_ncbiRefSeq/hg38.ncbiRefSeq.gtf.gz'
      }
      'mm39_refseq' {
       fasta = 's3://dicerna-genomes/mm39_ucsc/mm39.fa.gz'
       gtf  = 's3://dicerna-genomes/mm39_ucsc/mm39.ncbiRefSeq.gtf.gz'
      }

      'human_ensembl_v104_2021_March' {
        fasta = 's3://dicerna-genomes/human_ensembl_v014_2021_March/Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz'
        gtf  = 's3://dicerna-genomes/human_ensembl_v014_2021_March/Homo_sapiens.GRCh38.104.gtf.gz'
      }

     // removed gene line
     //  zcat GCF_000001405.39_GRCh38.p13_genomic.gtf.gz | awk '$3 != "gene" ' | gzip > GCF_000001405.39_GRCh38.p13_genomic.fixed.gtf.gz
     'human_refseq_release_109_2021_May' {
        fasta = 's3://dicerna-genomes/refseq_GRCh38_major_release_seqs_for_alignment_pipelines/GCA_000001405.15_GRCh38_full_analysis_set.fna.gz'
        gff  = 's3://dicerna-genomes/refseq_GRCh38_major_release_seqs_for_alignment_pipelines/GCA_000001405.15_GRCh38_full_analysis_set.refseq_annotation.gff.gz'
        hisat2_index = 's3://dicerna-genomes/refseq_GRCh38_major_release_seqs_for_alignment_pipelines/GCA_000001405.15_GRCh38_full_analysis_set.fna.hisat2_index.tar.gz'
      }
      'GRCm38_v102' {
        fasta = 's3://dicerna-genomes/GRCm38/Mus_musculus.GRCm38.dna.primary_assembly.fa.gz'
        gtf  = 's3://dicerna-genomes/GRCm38/Mus_musculus.GRCm38.102.gtf'
      }
      'GRCm39_v104' {
        fasta = 's3://dicerna-genomes/GRCm39_v104/Mus_musculus.GRCm39.dna.primary_assembly.fa.gz'
        gtf  = 's3://dicerna-genomes/GRCm39_v104/Mus_musculus.GRCm39.104.gtf.gz'
      }
      'mf6_ens_v104' {
        fasta = 's3://dicerna-genomes/Macaca_fascicularis_6.0/Macaca_fascicularis.Macaca_fascicularis_6.0.dna.toplevel.fa.gz'
        gtf = 's3://dicerna-genomes/Macaca_fascicularis_6.0/Macaca_fascicularis.Macaca_fascicularis_6.0.104.gtf.gz'
      }
      'mf5_ens_v102' {
        fasta = 's3://dicerna-genomes/Macaca_fascicularis_5.0_ensembl_release_102/Macaca_fascicularis.Macaca_fascicularis_5.0.dna.toplevel.fa.gz'
        gtf = 's3://dicerna-genomes/Macaca_fascicularis_5.0_ensembl_release_102/Macaca_fascicularis.Macaca_fascicularis_5.0.102.gtf.gz'
      }
      'mf5_ens_v102_ga01' {
        fasta = 's3://dicerna-genomes/Macaca_fascicularis_5.0_ensembl_release_102/Macaca_fascicularis.Macaca_fascicularis_5.0.dna.toplevel.fa.gz'
        hisat2_index = 's3://dicerna-genomes/Macaca_fascicularis_5.0_ensembl_release_102/feiran_hisat2_index/'
        splicesites = 's3://dicerna-genomes/mf5_ensembl_ga01/Macaca_fascicularis.ensembl.5.0.96_splicesites.txt'
        gtf = 's3://dicerna-genomes/Macaca_fascicularis_5.0_ensembl_release_102/Macaca_fascicularis.Macaca_fascicularis_5.0.102.gtf.gz'
      }
      'mf5_refseq_r101' {
        fasta = 's3://dicerna-genomes/Macaca_fascicularis_5.0_refseq_release_101/GCF_000364345.1_Macaca_fascicularis_5.0_genomic.fna.gz'
        gtf  = 's3://dicerna-genomes/Macaca_fascicularis_5.0_refseq_release_101/GCF_000364345.1_Macaca_fascicularis_5.0_genomic.fixed.gtf.gz'
      }
```
 
Again, this is the check list for the important things to check:
```
Input/output options
  input          : s3://dicerna-etl/test/monkey/sample_sheet_full.csv
  outdir         : s3://dicerna-etl/test/monkey_dicerna/results_star/
Reference genome options
  genome         : mf5_ens_v102
  aligner        : "star_salmon"  or "hisat2"
```
  
# Pipeline summary

![nf-core/rnaseq metro map](docs/images/nf-core-rnaseq_metro_map_grey.png)

The SRA download functionality has been removed from the pipeline (`>=3.2`) and ported to an independent workflow called [nf-core/fetchngs](https://nf-co.re/fetchngs). You can provide `--nf_core_pipeline rnaseq` when running nf-core/fetchngs to download and auto-create a samplesheet containing publicly available samples that can be accepted directly as input by this pipeline.

1. Merge re-sequenced FastQ files ([`cat`](http://www.linfo.org/cat.html))
2. Read QC ([`FastQC`](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/))
3. UMI extraction ([`UMI-tools`](https://github.com/CGATOxford/UMI-tools))
4. Adapter and quality trimming ([`Trim Galore!`](https://www.bioinformatics.babraham.ac.uk/projects/trim_galore/))
5. Removal of genome contaminants ([`BBSplit`](http://seqanswers.com/forums/showthread.php?t=41288))
6. Removal of ribosomal RNA ([`SortMeRNA`](https://github.com/biocore/sortmerna))
7. Choice of multiple alignment and quantification routes:
   1. [`STAR`](https://github.com/alexdobin/STAR) -> [`Salmon`](https://combine-lab.github.io/salmon/)
   2. [`STAR`](https://github.com/alexdobin/STAR) -> [`RSEM`](https://github.com/deweylab/RSEM)
   3. [`HiSAT2`](https://ccb.jhu.edu/software/hisat2/index.shtml) -> **NO QUANTIFICATION**
8. Sort and index alignments ([`SAMtools`](https://sourceforge.net/projects/samtools/files/samtools/))
9. UMI-based deduplication ([`UMI-tools`](https://github.com/CGATOxford/UMI-tools))
10. Duplicate read marking ([`picard MarkDuplicates`](https://broadinstitute.github.io/picard/))
11. Transcript assembly and quantification ([`StringTie`](https://ccb.jhu.edu/software/stringtie/))
12. Create bigWig coverage files ([`BEDTools`](https://github.com/arq5x/bedtools2/), [`bedGraphToBigWig`](http://hgdownload.soe.ucsc.edu/admin/exe/))
13. Extensive quality control:
    1. [`RSeQC`](http://rseqc.sourceforge.net/)
    2. [`Qualimap`](http://qualimap.bioinfo.cipf.es/)
    3. [`dupRadar`](https://bioconductor.org/packages/release/bioc/html/dupRadar.html)
    4. [`Preseq`](http://smithlabresearch.org/software/preseq/)
    5. [`DESeq2`](https://bioconductor.org/packages/release/bioc/html/DESeq2.html)
14. Pseudo-alignment and quantification ([`Salmon`](https://combine-lab.github.io/salmon/); _optional_)
15. Present QC for raw read, alignment, gene biotype, sample similarity, and strand-specificity checks ([`MultiQC`](http://multiqc.info/), [`R`](https://www.r-project.org/))

> - **NB:** Quantification isn't performed if using `--aligner hisat2` due to the lack of an appropriate option to calculate accurate expression estimates from HISAT2 derived genomic alignments. However, you can use this route if you have a preference for the alignment, QC and other types of downstream analysis compatible with the output of HISAT2.

# Quick Start

1. Install [`Nextflow`](https://www.nextflow.io/docs/latest/getstarted.html#installation) (`>=21.10.3`)

2. Install any of [`Docker`](https://docs.docker.com/engine/installation/), [`Singularity`](https://www.sylabs.io/guides/3.0/user-guide/) (you can follow [this tutorial](https://singularity-tutorial.github.io/01-installation/)), [`Podman`](https://podman.io/), [`Shifter`](https://nersc.gitlab.io/development/shifter/how-to-use/) or [`Charliecloud`](https://hpc.github.io/charliecloud/) for full pipeline reproducibility _(you can use [`Conda`](https://conda.io/miniconda.html) both to install Nextflow itself and also to manage software within pipelines. Please only use it within pipelines as a last resort; see [docs](https://nf-co.re/usage/configuration#basic-configuration-profiles))_. Note: This pipeline does not currently support running with Conda on macOS if the `--remove_ribo_rna` parameter is used because the latest version of the SortMeRNA package is not available for this platform.

3. Download the pipeline and test it on a minimal dataset with a single command:

   ```console
   nextflow run nf-core/rnaseq -profile test,YOURPROFILE --outdir <OUTDIR>
   ```

   Note that some form of configuration will be needed so that Nextflow knows how to fetch the required software. This is usually done in the form of a config profile (`YOURPROFILE` in the example command above). You can chain multiple config profiles in a comma-separated string.

   > - The pipeline comes with config profiles called `docker`, `singularity`, `podman`, `shifter`, `charliecloud` and `conda` which instruct the pipeline to use the named tool for software management. For example, `-profile test,docker`.
   > - Please check [nf-core/configs](https://github.com/nf-core/configs#documentation) to see if a custom config file to run nf-core pipelines already exists for your Institute. If so, you can simply use `-profile <institute>` in your command. This will enable either `docker` or `singularity` and set the appropriate execution settings for your local compute environment.
   > - If you are using `singularity`, please use the [`nf-core download`](https://nf-co.re/tools/#downloading-pipelines-for-offline-use) command to download images first, before running the pipeline. Setting the [`NXF_SINGULARITY_CACHEDIR` or `singularity.cacheDir`](https://www.nextflow.io/docs/latest/singularity.html?#singularity-docker-hub) Nextflow options enables you to store and re-use the images from a central location for future pipeline runs.
   > - If you are using `conda`, it is highly recommended to use the [`NXF_CONDA_CACHEDIR` or `conda.cacheDir`](https://www.nextflow.io/docs/latest/conda.html) settings to store the environments in a central location for future pipeline runs.

4. Start running your own analysis!

   ```console
   nextflow run nf-core/rnaseq --input samplesheet.csv --outdir <OUTDIR> --genome GRCh37 -profile <docker/singularity/podman/shifter/charliecloud/conda/institute>
   ```

   - An executable Python script called [`fastq_dir_to_samplesheet.py`](https://github.com/nf-core/rnaseq/blob/master/bin/fastq_dir_to_samplesheet.py) has been provided if you would like to auto-create an input samplesheet based on a directory containing FastQ files **before** you run the pipeline (requires Python 3 installed locally) e.g.

     ```console
     wget -L https://raw.githubusercontent.com/nf-core/rnaseq/master/bin/fastq_dir_to_samplesheet.py
     ./fastq_dir_to_samplesheet.py <FASTQ_DIR> samplesheet.csv --strandedness reverse
     ```

# Documentation

The nf-core/rnaseq pipeline comes with documentation about the pipeline [usage](https://nf-co.re/rnaseq/usage), [parameters](https://nf-co.re/rnaseq/parameters) and [output](https://nf-co.re/rnaseq/output).

# Credits

These scripts were originally written for use at the [National Genomics Infrastructure](https://ngisweden.scilifelab.se), part of [SciLifeLab](http://www.scilifelab.se/) in Stockholm, Sweden, by Phil Ewels ([@ewels](https://github.com/ewels)) and Rickard Hammar??n ([@Hammarn](https://github.com/Hammarn)).

The pipeline was re-written in Nextflow DSL2 and is primarily maintained by Harshil Patel ([@drpatelh](https://github.com/drpatelh)) from [Seqera Labs, Spain](https://seqera.io/).

The pipeline workflow diagram was designed by Sarah Guinchard ([@G-Sarah](https://github.com/G-Sarah)) and James Fellows Yates ([@jfy133](https://github.com/jfy133)).

Many thanks to other who have helped out along the way too, including (but not limited to):
[@Galithil](https://github.com/Galithil),
[@pditommaso](https://github.com/pditommaso),
[@orzechoj](https://github.com/orzechoj),
[@apeltzer](https://github.com/apeltzer),
[@colindaven](https://github.com/colindaven),
[@lpantano](https://github.com/lpantano),
[@olgabot](https://github.com/olgabot),
[@jburos](https://github.com/jburos).
