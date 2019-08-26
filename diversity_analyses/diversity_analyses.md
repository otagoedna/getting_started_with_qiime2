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

## Alpha and beta diversity analysis


### Determine appropriate sampling depth

[**VISUALISATION: feature table summary**](../feature_table_summary/index.html)

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









