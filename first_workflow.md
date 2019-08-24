# Example Workflow

This will follow the steps from importing the files, setting up metadata, and running initial analyses. 

## Importing data

We will start with sequence files that have already been demultiplexed and merged. The Qiime2 [**Moviing Pictures Tutorial**](https://docs.qiime2.org/2019.7/tutorials/moving-pictures/) starts with the initial file. 

To import a group of files, we need to create a *manifest* file. This is a simple tab-delimited file with the sample ID and path of each file:

```
sample-id     absolute-filepath
sample-1      $PWD/some/filepath/sample1_R1.fastq
sample-2      $PWD/some/filepath/sample2_R1.fastq
```



See the Qiime2 [**Importing Data Tutorial**](https://docs.qiime2.org/2019.7/tutorials/importing/) for more details, including importing paired end sequences. 

Once you have a manifest file, you can import the sequence files into a Qiime2 Artifact:

```
qiime tools import \
  --type 'SampleData[SequencesWithQuality]' \
  --input-path sample_manifest.txt \
  --output-path sample_demux.qza \
  --input-format SingleEndFastqManifestPhred33V2
```

## Denoise sample sequences

