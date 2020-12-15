# ViralRecall v. 2.0
ViralRecall is a flexible command-line tool for detecting signatures of giant viruses (NCLDV) in genomic data.
Version 2 has been updated to focus more on NCLDV compared to version 1, but the earlier options are still available. 

### Dependencies
ViralRecall is written in Python 3.5.6 and requires biopython, matplotlib, numpy, and pandas. 
ViralRecall uses Prodigal and HMMER3 for protein prediction and HMM searches, respectively. Please ensure these tools are installed in your PATH before using. 
One a Unix system you should be able to install these tools with: 

> sudo apt install prodigal

and

> sudo apt install hmmer

or if you don't have sudo priveleges, you can try with conda:

>conda install prodigal -c bioconda

and

>conda install hmmer -c bioconda

### Installation
Please ensure you are using > Python 3.5.2 and have the appropriate python modules installed. If this is an issue please create a Python environment using conda (see here https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html) 
ViralRecall was tested on Ubuntu 16.04 and should work on most Unix-based systems. To see the help menu use:
> python viralrecall.py -h

### Databases
Viralrecall can be run using either of two viral HMM databases: 1) VOGDB, which contains a wide collection of viral orthologous groups and is useful for broad characterization of viral signatures, or 2) GVOGs, a custom set of Giant Virus Orthologous Groups that are fairly specific to Nucleo-Cytoplasmic Large DNA Viruses (NCLDV). The GVOGs database has replaced the NCVOG database used in a prevous version. In addition, ViralRecall matches proteins against the Pfam database (Pfam v. 32), which is a broad-specificity database that detects many protein families that are common in the genomes of cellular organisms.


The database files are available for download from the Virginia Tech library system. To download and unpack, navigate to the folder that contains the viralrecall.py script and type:
> wget -O hmm.tar.gz https://data.lib.vt.edu/downloads/6h440s637

and then

> tar -xvzf hmm.tar.gz

### Basic Usage
To test if ViralRecall will run properly type:
> python viralrecall.py -i examples/arm29B.fna -p test_outdir -t 2 -f

Results should be located in the test_outdir folder. 
The output folder will contain:

*.faa:                   The proteins predicted from the input file using Prodigal

*.full_annot.tsv:        A full annotation table of the predicted ORFs. 

*.vregion.annot.tsv:    An annotation of only the viral regions (only present if some viral regions found)

*summary.tsv             Summary statistics for the predicted viral regions. 

*.pfamout                Raw output of the Pfam HMMER3 search

*.vogout                 Raw output of the VOGDB HMMER3 search


Additionally, for each viral region viralrecall will print out .faa and .fna files for the proteins and nucleotide sequences for the regions found. 
Please be sure to use only .fna files as input. 


### Options

There are several parameters you can change in viralrecall depending on your preferences and the data you're analyzing. The important parameters that will influence the results are:


**-db, --database**
This is the database usef for viral detection. Use "VOG" for the VOGDB, "GVOG" for GVOGs, and "marker" to only search against 10 NCLDV marker genes. GVOGs are more useful for NCLDV-specific searches, and this is the default. The "marker" option is much faster and may be useful for quickly screening large datasets. 

**-s, --minscore**
This is the mean score that a genomic regions needs to have in order to pass the filter and get reported as a viral region. The score is calculated from the HMMER3 scores, with higher scores indicating more and better matches to the VOG database, and lower scores indicating more and higher matches to the Pfam database. The default is 10. 

**-w, --window**
Size of the sliding window to use for calculating moving averages. A smaller window may help predict short viral regions, but may split large viral regions into several pieces. 

**-m, --minsize**
Minimum size, in kilobases, of the viral regions to report. 

**-v, --minvog**
Minimum number of hits against the VOG database that must be recorded in a region in order for it to be reported (larger values == higher confidence). 

**-c, --contiglevel**
If this option is used, mean ViralRecall scores will be provided for the input contigs rather than viral regions. This is useful for screening contigs for viral signatures.

**-r, --redo**
If you have already run ViralRecall and you want to re-run it with different parameters, you can use the -r flag to avoid re-running Prodigal and HMMER, which are the most time-consuming steps. 

**-b, --batch**
Use this flag if the input is a folder of .fna files to search, rather than a single .fna file. 


For example, if we wanted to recover viral regions from a eukaryotic contig we could use the following command:
> python viralrecall.py -i examples/arm29B.fna -p testout -s 15 -m 30 -v 10

Here we are asking for only regions that have a mean score >= 15, are at least 30 kilobases long, and have at least 10 VOG hits.

If we want to quickly re-do the above analysis with different parameters, but without re-doing gene predictions and HMMER3 searches, we can use the -r flag:

> python viralrecall.py -i examples/arm29B.fna -p testout -s 15 -m 30 -v 10 -r

This should re-calculate the results quickly and allow you to identify the most appropriate ones for your analysis. 


### Batch mode
If you have many sequences you wish to test you can put them all in a folder and use batch mode. Here the input (-i) should point to a folder with .fna files in it. 
Basic usage is:

> python viralrecall.py -i examples/testfolder -p folderout -b

All of the output files should have their own folder in the folderout directory. You can also use the -b flag with the -r flag for quick re-calculations. 


## Citation

For questions or comments feel free to email Frank Aylward at faylward _at_ vt dot edu

This tool requires Prodigal and HMMER3. Their citations are:

Hyatt et al. “Prodigal: prokaryotic gene recognition and translation initiation site identification”. BMC bioinformatics, 2010. 
Eddy, "A new generation of homology search tools based on probabilistic inference". Genome Informatics, 2009. 









