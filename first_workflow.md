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
  --input-path *MANIFEST_FILE* \
  --output-path *QIIME_DEMUX.qza* \
  --input-format SingleEndFastqManifestPhred33V2
```

## Look at data quality

Before we go on, we need to examine the sequence quality to determine how we should trim the sequences. If you have run [**FastQC**](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/) on your raw sequence files, you can examine the *per base sequence quality* category. Qiime also provides a plugin for graphing sequence quality. Add the input and output names to the following commands and run:

```
qiime demux summarize \
  --i-data *QIIME_DEMUX.qza* \
  --o-visualization *DEMUX_SEQ_SUMMARY_VIZ.qzv*
```

You can download the visualisation file to your computer and open it using [Qiime2View](https://view.qiime2.org/), or run `qiime tools view *DEMUX_SEQ_SUMMARY_VIZ.qzv*` on the file if you have Qiime2 installed. For the sake of time, click here to view the visualisation:

[Run 2 demux summary](run2_demux_summary/index.html)



## Denoise sample sequences

Now we are ready to [**denoise**](https://docs.qiime2.org/2019.7/tutorials/overview/#denoising-and-clustering) the samples. In Qiime2, you can use either [**Dada2**](https://benjjneb.github.io/dada2/) or [**Deblur**](https://github.com/biocore/deblur), with most options. (Later we will show you how to import samples that have been denoised using other programs--or with additional parameters not found in the plugins)

From evaluating the sequence quality, we can observe that the quality is lower in the first ten or so bases, and stays relatively high right to the end. Therefore, we will trim the first 13 bases using the --p-trim-left argument, and truncate the sequences at 150 bp using the --p-trun-len argument. Change the names of input and output files as before. 

```
qiime dada2 denoise-single \
  --p-trim-left 13 \
  --p-trunc-len 150 \
  --i-demultiplexed-seqs *QIIME_DEMUX.qza* \
  --o-representative-sequences *REP-SEQS.qza* \
  --o-table *TABLE*.qza \
  --o-denoising-stats *STATS.qza*
```

You can then create visulations from the output files to examine the results of denoising. 

```
qiime metadata tabulate \
  --m-input-file *STATS.qza* \
  --o-visualization *STATS_VIZ.qzv*

qiime feature-table summarize \
  --i-table table-run2.qza \
  --o-visualization table_run2.qzv \
  --m-sample-metadata-file sample-metadata.tsv
```

## Taxonomy assignment




