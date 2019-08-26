# More on Taxonomy

## Alternatives to Naive Bayes

Qiime2 has plugins to use both BLAST and vsearch to assign taxonomy. We will go through these now.

### Using BLAST to assign taxonomy

```
qiime feature-classifier classify-consensus-blast \
  --i-query {REP-SEQS}.qza \
  --i-reference-reads /var/DB/greengenes/gg_99_reference_seqs.qza \
  --i-reference-taxonomy /var/DB/greengenes/gg_99_reference_taxonomy.qza \
  --p-perc-identity 0.97 \
  --o-classification {BLAST-TAXONOMY}.qza \
  --verbose
```

tabulating and visulising the results is the same as for NB

```
qiime metadata tabulate \
  --m-input-file {BLAST-TAXONOMY}.qza \
  --o-visualization {BLAST-TAXONOMY_VIZ}.qzv
```

```
qiime taxa barplot \
  --i-table {TABLE}.qza \
  --i-taxonomy {BLAST-TAXONOMY}.qza \
  --m-metadata-file sample-metadata.tsv \
  --o-visualization {BLAST-TAXA-BAR-PLOTS_VIZ}.qzv
```

### Using vsearch

You can also use vsearch to assign taxonomy. This is optional as it takes a bit longer than the others

```
qiime feature-classifier classify-consensus-vsearch \
  --i-query {REP-SEQS}.qza \
  --i-reference-reads /var/DB/greengenes/gg_99_reference_seqs.qza \
  --i-reference-taxonomy /var/DB/greengenes/gg_99_reference_taxonomy.qza \
  --p-perc-identity 0.97 \
  --o-classification {VSEARCH-TAXONOMY}.qza \
  --verbose
```

## Filtering feature tables

It is possible to filter the feature tables to remove samples or features. There is a [full tutorial](https://docs.qiime2.org/2019.7/tutorials/filtering/) showing many examples. Here we will go over just one.

From the FMT full table, we observe that there are many taxa in the phylum Firmicutes across all samples. If we want to explore this group in more depth, we can create a new table with just these taxa.

```
qiime taxa filter-table \
  --i-table {TABLE}.qza \
  --i-taxonomy {TAXONOMY}.qza \
  --p-include "k__Bacteria; p__Firmicutes" \
  --o-filtered-table {FIRMICUTES-TABLE}.qza

qiime taxa barplot \
  --i-table {Firmicutes-TABLE}.qza \
  --i-taxonomy {TAXONOMY}.qza \
  --m-metadata-file sample-metadata.tsv \
  --o-visualization {FIRMICUTES-TAXA-BAR-PLOTS_VIZ}.qzv
```

## Grouping tables by feature

Another handy plug-in allows you to group samples in an analysis by a feature. In the FMT taxonomy, there are many samples, and it might be better to look at them as a group. For example, we can group by week to see how the taxonomic composition changes 

```
qiime feature-table group \
  --i-table {TABLE}.qza \
  --p-axis sample \
  --m-metadata-file sample-metadata.tsv \
  --m-metadata-column week \
  --p-mode sum \
  --o-grouped-table {GROUP_BY_WEEK_TABLE}.qza
```

The only thing is that because the samples are now a feature, a new metadata file is needed to create the bar plots. I have made a very simple one for this example.

```
qiime taxa barplot \
  --i-table {GROUP_BY_WEEK_TABLE}.qza \
  --i-taxonomy {TAXONOMY}.qza \
  --m-metadata-file week_metadata.tsv \
  --o-visualization {GRP_BY_WEEK_TAXA-BAR-PLOTS}.qzv
```








