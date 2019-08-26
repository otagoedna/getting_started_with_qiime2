# Example Workflow

This will follow the steps from importing the files, setting up metadata, and running initial analyses. 

The example dataset we will be using has been taken from the [Qiime2 Fecal microbiota transplant tutorial (FMT)](https://docs.qiime2.org/2019.7/tutorials/fmt/). We will be following some of the steps there, but here we will be combining from a few different tutorials to present a complete example. 

## Importing data

We will start with sequence files that have already been demultiplexed and merged. The Qiime2 [**Moving Pictures Tutorial**](https://docs.qiime2.org/2019.7/tutorials/moving-pictures/) starts with the initial file. In many cases, such as dual-index barcodes, it is not possible to use Qiime for demultiplexing. In this example we will use sequence files that have already been demultiplexed.

To import a group of files, we need to create a *manifest* file. This is a simple tab-delimited file with the sample ID and path of each file:

```
sample-id     absolute-filepath
sample-1      $PWD/some/filepath/sample1_R1.fastq
sample-2      $PWD/some/filepath/sample2_R1.fastq
```


See the Qiime2 [**Importing Data Tutorial**](https://docs.qiime2.org/2019.7/tutorials/importing/) for more details, including importing paired end sequences. 

Once you have a manifest file, you can import the sequence files into a Qiime2 Artifact. We will start with the FMT run 2 sequences, as it is smaller and will go a little quicker. There is already a manifest file in your main folder called *run_2_manifest.txt*. Change the --input-path parameter in the following command, as well as the name of the output file (--output-path)

```
qiime tools import \
  --type 'SampleData[SequencesWithQuality]' \
  --input-path {MANIFEST_FILE} \
  --output-path {QIIME_DEMUX}.qza \
  --input-format SingleEndFastqManifestPhred33V2
```

## Evaluate data quality

Before we go on, we need to examine the sequence quality to determine how we should trim the sequences. If you have run [**FastQC**](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/) on your raw sequence files, you can examine the *per base sequence quality* category. Qiime also provides a plugin for graphing sequence quality. Add the input and output names to the following commands and run:

```
qiime demux summarize \
  --i-data {QIIME_DEMUX.qza} \
  --o-visualization {DEMUX_SEQ_SUMMARY_VIZ.qzv}
```

You can download the visualisation file to your computer and open it using [Qiime2View](https://view.qiime2.org/), or run `qiime tools view {DEMUX_SEQ_SUMMARY_VIZ}.qzv` on the file if you have Qiime2 installed. For the sake of time, click here to view the visualisation:

[**VISUALISATION: Run 2 demux summary**](run2_demux_summary/index.html)



## Denoise sample sequences

Now we are ready to [**denoise**](https://docs.qiime2.org/2019.7/tutorials/overview/#denoising-and-clustering) the samples. In Qiime2, you can use either [**Dada2**](https://benjjneb.github.io/dada2/) or [**Deblur**](https://github.com/biocore/deblur), with most options. (Later we will show you how to import samples that have been denoised using other programs--or with additional parameters not found in the plugins)

From evaluating the sequence quality, we can observe that the quality is lower in the first ten or so bases, and stays relatively high right to the end. Therefore, we will trim the first 13 bases using the *--p-trim-left argument*, and truncate the sequences at 150 bp using the *--p-trun-len argument*. Change the names of input and output files as before. 

```
qiime dada2 denoise-single \
  --p-trim-left 13 \
  --p-trunc-len 150 \
  --i-demultiplexed-seqs {QIIME_DEMUX}.qza \
  --o-representative-sequences {REP-SEQS}.qza \
  --o-table {TABLE}.qza \
  --o-denoising-stats {STATS}.qza
```

You can then create visualisations from the output files to examine the results of denoising: 

```
qiime metadata tabulate \
  --m-input-file {STATS}.qza \
  --o-visualization {STATS_VIZ}.qzv

qiime feature-table summarize \
  --i-table {TABLE}.qza \
  --o-visualization {TABLE_VIZ}.qzv \
  --m-sample-metadata-file sample-metadata.tsv
```

Again, download the visualisations and compare them to here:

[**VISUALISATION: denoising stats**](denoising_stats/index.html)

[**VISUALISATION: feature table summary**](feature_table_summary/index.html)

## Taxonomy assignment

Now that we have representative sequences from the denoising process (e.g. ZOTUs, ASVs, ESVs), we can make a taxonomic assignment of them. There are several methods to do this. See the [**Qiime2 Overview**](https://docs.qiime2.org/2019.7/tutorials/overview/#taxonomy-classification-and-taxonomic-analyses) for a discussion of them (Also see this [**paper**](https://microbiomejournal.biomedcentral.com/articles/10.1186/s40168-018-0470-z)). We will start with the machine-learning based classification method, as that is generally favoured by the Qiime group. 


### Use Naive Bayes (machine learning) to classify

In order to use the Naive Bayes (NB) method to assign taxonomy, it is necessary to train the sequence database first. Because this can take a great deal of time, a pre-trained classifier has been made available for you. The [Qiime2 Data Resources page](https://docs.qiime2.org/2019.7/data-resources/) provides some pre-trained classifiers for common primer combinations, as well as links to the Greengenes and Silva databases for 16S and 18S gene studies. For additional primer combinations, or other gene references, there is a [tutorial for training feature classifiers](https://docs.qiime2.org/2019.7/tutorials/feature-classifier/).

Use the command below, changing the name of the rep-seqs artifact that you have created:

```
qiime feature-classifier classify-sklearn \
  --i-classifier /var/DB/greengenes/gg-13-8-99-515-806-nb-classifier.qza \
  --i-reads {REP-SEQS}.qza \
  --o-classification {TAXONOMY}.qza
```

You can then create a visualisation of the classification:

```
qiime metadata tabulate \
  --m-input-file {TAXONOMY}.qza \
  --o-visualization {TAXONOMY_VIZ}.qzv
```

[**VISUALISATION: taxonomy table**](taxonomy_tabulation/index.html)


### Visualise the taxonomy assignment

You can now use the frequency table created from the denoising, along with the taxonomy output, to create a barplot graph for the taxonomy assignment. 

```
qiime taxa barplot \
  --i-table {TABLE}.qza \
  --i-taxonomy {TAXONOMY}.qza \
  --m-metadata-file sample-metadata.tsv \
  --o-visualization {TAXA-BAR-PLOTS_VIZ}.qzv
```

[**VISUALISATION: taxonomy bar plots**](taxonomy_bar_plots/index.html)


## Run through steps again with Run 1 data

Now that you have seen the process through to taxonomic classification, you can repeat these steps for run 1. Every separate sequencing run has to be run through denoising separately, so this is not a redundant step. Do all the steps up to Taxonomy assignment, and then you will merge the tables and rep-seqs from both runs into combined files. Then run the NB taxonomy assignment with the combined files. 


### merge tables from both runs

```
qiime feature-table merge \
  --i-tables {TABLE-1}.qza \
  --i-tables {TABLE-1}.qza\
  --o-merged-table {COMBINED-TABLE}.qza

qiime feature-table merge-seqs \
  --i-data {REP-SEQS-1}.qza \
  --i-data {REP-SEQS-2}.qza \
  --o-merged-data {COMBINED-REP-SEQS}.qza

qiime feature-table summarize \
  --i-table {COMBINED-TABLE}.qza \
  --o-visualization {COMBINED-TABLE_VIZ}.qzv \
  --m-sample-metadata-file sample-metadata.tsv
```










