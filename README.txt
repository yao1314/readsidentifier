Pipeline for read based identification of Next Generation sequencing data based on raw data directly. Refer to "Comparing the effectiveness of metagenomics and metabarcoding for diet analysis of a leaf-feeding monkey (Pygathrix nemaeus)" by Srivathsan et al. for details. It uses BLAST outputs for further processing of data. 

The output is then filtered, first a hit length cutoff is implemented after which only sequences with best identity to reference sequence are retained. Lastly it summarises the taxanomic profile of the data. It uses an identity cutoff to then filter the results to give read based identifications

PREPARATION OF INPUT FILES

Input files are Blast output files generated using the NCBI-blast+ under outfmt 6 (tabular)

A typical blast command would look like
blastn -query ngsrawreads.fasta -db databasename -out blastoutputfilename -outfmt 6

Other parameters (such as evalue based filtering, wordsize) are left to users discretion. 

The database used here can be built using 
makeblastdb -in databasefastafile.fasta -out databasename -dbtype nucl

Note 1: Do not use -parse_seqids command, especially if dealing with genbank downloaded data.

Note 2: Blast ignores anything after a space in the sequence header. If you databasefastfile.fasta contains these, in blastoutput you will lose all information beyond the space. 

Note 3: currently these scripts have been tested on Ubuntu LTS 12.04/13.04. Please inform if incompatible with Mac.

INSTALLATION

3 parts to this:

1) The repository is in github and can be downloaded like any other, as follows:

git clone https://github.com/asrivathsan/readsidentifier-1.0

2) Download ftp://ftp.ncbi.nlm.nih.gov/pub/taxonomy/taxdump.tar.gz. Extract data to a new folder in the filesystem

3) Create a third directory, ex. gi_tax. This will contain files that link your sequenceID to taxon id.

There are 2 scenarios:
Scenario 1: If your database is locally created (does not have gi|123124|gb syntax)
>SequenceID
ATGCGAGACGGGAAA

Create text file(s) in this folder, the format of file(s) is/are to be as follows: 
SequenceID<TAB>taxid<return>


Scenario 2: If databasefastafile.fasta is downloaded from Genbank, such that your sequence headers are in format

>gi|123124|gb...
ATGCGAGACGGGAAA

the ginumber to taxid information is already available at ftp://ftp.ncbi.nlm.nih.gov/pub/taxonomy/gi_taxid_nucl.dmp.gz. One can extract file into the gi_tax folder

Unfortunately gi_taxid_nucl.dmp is a massive file which will use up ~50GB of memory currently if read into memory. You may want to split this file into multiple (I would recommend 20 for a 4gb system) in the gi_tax folder prior to running the script.

Couple of commands to help with the splitting:
In terminal type:
split gi_taxid_nucl.dmp -l <number of lines per file>

Total number of lines in file can be obtained by:
wc -l gi_taxid_nucl.dmp

USAGE

To use the script, prepare config file (example sample-config.txt)...

then run 
python readidentifier.py config.txt

OUTPUT

A number of output files are generated, for result please look at outputprefix.final files. When paired-end analyses is done, 3 .final files are generated. outputprefix.final will contain the paired end analyses results, while blastout1.XXX.final and blastout2.XXX.final contain the single end analyses results
Briefly,
.withtaxid : contains the initial blastout with taxid information as final column
.withtaxid.bylen** : post-filtering by hit length, this file contains sequences with best identity to reference
.withtaxid.bylen**.byid** : post filtering by identity threshold
.withtaxid.bylen**.byid**.cat: the previous file along with all the parent taxid information in various columns
.withtaxid.bylen**.byid**.cat.con: retains only taxid & parent taxid corresponding to species,genus,family,order,class,phylum. Creates a consistency profile such that sequences with ambiguous matches to different taxids from now termed ambiguous
.final: converts the taxid information to scientific names


CITATION

If using these scripts, cite:

Srivathsan A., Sha J.C.M., Vogler, A.P. and Meier, R. (2014). "Comparing the effectiveness of metagenomics and metabarcoding for diet analysis of a leaf-feeding monkey (Pygathrix nemaeus)". Molecular Ecology Resources. Submitted.





