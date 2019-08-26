# Basic tools 

We will start with the basic tools that you will need for this workshop. 

## Starting up Qiime2

Qiime2 is installed as a conda environment, so you will have to activate this to use it. Once you have logged in to the server, load the conda module and activate the environment:

```
module load conda

source activate qiime2-2019.7
```

In order to enable tab completion for Qiime commands:

```

```

This should only need to be done once

## Basic Qiime2 commands

For getting help with any qiime command, you add --help after any command:

`qiime tools import --help`


For this workshop, template commands will be provided initially. It is recommended that you copy these and paste them in a text editor, then change the input/output file names and any other parameters as needed. For the most part, the file names/variables that need to be changed will be in uppercase within curly brackets, like this:

```
qiime tools import \
  --type 'SampleData[SequencesWithQuality]' \
  --input-path {MANIFEST_FILE} \
  --output-path {QIIME_DEMUX}.qza \
  --input-format SingleEndFastqManifestPhred33V2
```

In the example above, you would change the name the **{MANIFEST_FILE}** and **{QIIME_DEMUX}** names. The suffixes **.qza** and **.qzv** always need to be used. 


## Viewing Qiime2 visuals

All visuals in Qiime2 are shown using a web browser. Because we are running this workshop on a server, you will need to download the visual files (any file ending in *.qzv*) to your laptop. Here is a quick primer on transferring files to/from the server.

To move a file to the server from your computer:

```
scp FILENAME username@boros.otago.ac.nz:qiime_workshop/
```

To move a file from the server to your computer, you switch the order. You have to provide a path to where on the computer you want the file. 

```
scp username@boros.otago.ac.nz:FILENAME /PATH/TO/TARGET/FOLDER/
```

Let's move the file *example_viz.qzv* from the qiime_workshop folder on boros. First we'll create a directory on the Desktop for qiime files, then move into that directory to make it easier:

```
mkdir Desktop/qiime_files

cd Desktop/qiime_files
```

Now we can move the files from boros into the folder with just a **.**

```
scp username@boros.otago.ac.nz:qiime_workshop/example_viz.qzv .
```

If you have Qiime2 installed and running on your computer, you can view the visual files like this:

```
qiime tools view example_viz.qzv
```

Fortunately, you do not need to have Qiime2 installed to view the visuals. Go to this link:

[Qiime2view](https://view.qiime2.org/)

And you can open any visualisations on your computer. The website has some examples you can check out. Keep this website open as we proceed through the workshop.

## Qiime2 metadata file

For most downstream analyses in Qiime2, you need a metadata file. This is essentially a tab-delimited file that lists samples in the first column; additional columns can be used for other fields, and the state of each for each sample.

You can view the sample metadata for this data set here:

[**sample metadata FMT**](https://data.qiime2.org/2019.7/tutorials/fmt/sample_metadata)

Qiime2 provides a metadata tutorial to help prepare the metadata file, but you can start very simply with a list of samples.














