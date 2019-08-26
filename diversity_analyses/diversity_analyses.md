# Diversity Analyses



## Generate tree for phylogenetic diversity analysis

```
qiime phylogeny align-to-tree-mafft-fasttree \
  --i-sequences {REP-SEQS}.qza \
  --o-alignment {ALIGNED-REP-SEQS}.qza \
  --o-masked-alignment {MASKED-ALIGNED-REP-SEQS}.qza \
  --o-tree {UNROOTED-TREE}.qza \
  --o-rooted-tree {ROOTED-TREE}.qza
```

## Alpha and beta diversity analyses


### Determine appropriate sampling depth

It is necessary to set the rarefaction or sampling depth for the analyses (see [**here**](https://docs.qiime2.org/2019.7/tutorials/moving-pictures/#alpha-and-beta-diversity-analysis) for an explanation). This is set with the `--p-sampling-depth` parameter in the core-metrics plugin. You can look at the feature table summary to examine the number of reads for each sample to help set the sampling depth:

[**VISUALISATION: feature table summary**](../combined_feature_table_summary/index.html)

### Run core metrics 

Now we will run the core-metrics-phylogenetic pipeline, which runs a range of alpha and beta diversity tests on the data. This utilises the tree produced in the previous step in order to measure diversity based on phylogenetic distance. 

```
qiime diversity core-metrics-phylogenetic \
  --i-phylogeny {ROOTED-TREE}.qza \
  --i-table {COMBINED-TABLE}.qza \
  --p-sampling-depth {NUMBER} \
  --m-metadata-file sample-metadata.tsv \
  --output-dir {CORE-METRICS-RESULTS}
```

Several statistical and visual outputs are produced, including PCOA plots. Here is one:

**VISUALISATION: bray-curtis emperor plot**



## Importing from other programs

You can see that the two main components that you need to run the diversity analyses is a frequency table and representative sequences. This means we should be able to import results from other other programs, such as OBITools, into Qiime2 to run the statistics. We will try this using the table and ZOTUs from the previous workshop. The only other file you will need is a sample metadata file, which can be as simple or complicated as you need.

The program biom, which comes bundled with the Qiime2 installation, is used to convert to its format:

### Import frequency table

```
biom convert -i zotutab_10_changed.txt -o belarus.biom --to-hdf5 
```

The table in biom format can then be imported into Qiime2:

```
qiime tools import \
  --input-path belarus.biom \
  --type 'FeatureTable[Frequency]' \
  --input-format BIOMV210Format \
  --output-path belarus_feature-table.qza
```

### Import Rep seqs (ZOTUs)

```
qiime tools import \
  --type 'FeatureData[Sequence]' \
  --input-path zotus_10.fasta  \
  --output-path belarus_zotu_rep_seqs.qza
```

### Generate table stats so you can determine --p-sampling-depth

```
qiime feature-table summarize \
  --i-table belarus_feature-table.qza \
  --o-visualization belarus_feat-table.qzv \
  --m-sample-metadata-file fish_samples.tsv

qiime feature-table tabulate-seqs \
  --i-data belarus_zotu_rep_seqs.qza \
  --o-visualization belarus_zotu_rep-seqs.qzv
```

from examining feature table vis, interactive sample detail, can set minimum to 20000

### Run core-metrics on samples

You could run the core-metrics-phylogenetics in order to use diversity (depends on the gene used), but for the sake of this example, the regular core-metrics will suffice.

```
qiime diversity core-metrics \
  --i-table belarus_feature-table.qza \
  --p-sampling-depth 20000 \
  --m-metadata-file fish_samples.tsv \
  --output-dir belarus-core-metrics-results
```

## Going further with analyses

For more advanced statistical analyses, and to produce publication-quality figures (and have more control over visual details), there is a plug-in called Qiime2R:

[**Qiime2R Github page**](https://github.com/jbisanz/qiime2R)

There is also a tutorial on the Qiime2 Forum:

[**Qiime2R Tutorial**](https://forum.qiime2.org/t/tutorial-integrating-qiime2-and-r-for-data-visualization-and-analysis-using-qiime2r/4121)













