Trimming and Filtering ddRAD data
================

**Reserve a general machine at BioHPC and connect to this machine**

In order to compute we will need to reserve a machine at the BioHPC for
our computing. For ddRAD analysis we can use the general machines
(cheapest, but there are only 2 available and so they are often hard to
get) or the medium gen1 machines. Connect to this machine:

``` bash
ssh USER_NAME@MACHINE_NAME.biohpc.cornell.edu
```

**navigate to the working directory and make directory for the
analysis**

``` bash
cd /workdir
mkdir /workdir/USER_NAME
```

**copy data from your home directory to the the working directory**

``` bash
cp /home/USER_NAME/RAW_DATA/*.gz /workdir/USER_NAME &
```

# Check quality of data using FastQC

We will use FastQC
(<https://www.bioinformatics.babraham.ac.uk/projects/fastqc/>) to check
the quality of the data. Check the website above for an overview of the
output and what it means.

To do this we can either run fastQC on each file or we can write a
script to run fastQC on all of our data.

Create script - fastqc.sh (e.g see below)

    fastqc FILENAME1.fastq.gz &
    fastqc FILENAME2.fastq.gz &
    fastqc FILENAME3.fastq.gz 

You will need to transfer this script file to /workdir on the cluster
using Filezilla. Make sure that the script is in the same directory as
the data. Run the script.

``` bash
bash fastqc.sh &
```

This should output an html file with results that can be transfered back
to your computer and viewed in a browser.

# Trim and Filter the data

We will use the `fastx_trimmer` and `fastq_quality_filter` functions
from the `FASTX-Toolkit` to make sure that we are using good quality
data for the STACKS analysis.

We will first need to unzip the data.

``` bash
gunzip *.gz &
```

Create a script (trimfilter.sh, see example below) so that all steps can
be completed without much user input.

    ##Trim 3' end of all reads to a length of 147 bp (FASTX Trimmer). 
    #note: you can change this based on the Phred scores at the ends of sequences - see fastQC results 
    fastx_trimmer -f 1 -l 147 -Q33 -i FILENAME1.fastq -o FILENAME1_t.fastq & 
    fastx_trimmer -f 1 -l 147 -Q33 -i FILENAME2.fastq -o FILENAME2_t.fastq & 
    fastx_trimmer -f 1 -l 147 -Q33 -i FILENAME3.fastq -o FILENAME3_t.fastq

    #note the addition of "&" means that the processes will run concurrently. 
    #Note there is no "&" at the end of the last line, otherwise the script will move on to the next line, but the files needed won't be complete yet


    ##Eliminate sequences where there is a sinlge Phred score below 10 and then sequences where 5% of reads have a with Phred quality scores below 20 

    #100% of the bases in a sequence must have a score of higher than 10 for the sequence to be kept 
    fastq_quality_filter -q 10 -p 100 -Q33 -i FILENAME1_t.fastq -o FILENAME1_tf.fastq &
    fastq_quality_filter -q 10 -p 100 -Q33 -i FILENAME2_t.fastq -o FILENAME2_tf.fastq &
    fastq_quality_filter -q 10 -p 100 -Q33 -i FILENAME3_t.fastq -o FILENAME3_tf.fastq 

    #95% of the bases in a sequence must have a score of more than 20 for the sequence to be kept 
    fastq_quality_filter -q 20 -p 95 -Q33 -i FILENAME1_tf.fastq -o FILENAME1_tff.fastq & 
    fastq_quality_filter -q 20 -p 95 -Q33 -i FILENAME2_tf.fastq -o FILENAME2_tff.fastq & 
    fastq_quality_filter -q 20 -p 95 -Q33 -i FILENAME3_tf.fastq -o FILENAME3_tff.fastq 

    #label of "tff" indicates that the second round of quality filtering is completed

Transfer this script to the /workdir and run the script

``` bash
nohup bash trimfilter.sh &
```

- “nohup” stands for stands for “no hangup.”
- The hangup (HUP) signal is normally sent to a process to inform it
  that the user has logged off (or “hung up”)
- The HUP signal is intercepted by nohup, allowing the process to
  continue running if you logout
- All output, including any error messages, will be written to the file
  nohup.out in the directory where you run this command. \#each time you
  run a command using nohup, if there is already a nohuop.out file, it
  will add the output to the end of that file.
- I usually rename or delete the existing nohup before running the next
  command making it easier to check the files later.
