Denovo Alignment
================

# DENOVO Alignement

- information on STACKS can be found here:
  <http://catchenlab.life.illinois.edu/stacks/>

- explanation of how the denovo pipeline works to form stacks and loci
  and what the parameters mean:
  <http://catchenlab.life.illinois.edu/stacks/param_tut.php>

- see this paper for description of the pipeline we will use: Rochette,
  N., Catchen, J. Deriving genotypes from RAD-seq short-read data using
  Stacks. Nat Protoc 12, 2640–2659 (2017).
  <https://doi-org.proxy.library.cornell.edu/10.1038/nprot.2017.123>

|                                                                                                                                                                                            |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| NOTE: To Run Stacks 2 on the BioHPC cluster                                                                                                                                                |
| To run the most recent version of stacks check the bioHPC website: for stacks2 - <https://biohpc.cornell.edu/lab/userguide.aspx?a=software&i=454#c>                                        |
| `bash export LD_LIBRARY_PATH=/usr/local/gcc-7.3.0/lib64:/usr/local/gcc-7.3.0/lib export PATH=/programs/stacks-2.5/bin:$PATH`                                                               |
| \- Note: these commands are for version 2.5, but there may be a newer version - so check the bioHPC website - note you will need to specify these every time you log back into the cluster |

## Denovo Trials with different M/n values:

Prepare a script to to run the subset of samples through the denovo
wrapper using different M and n parameters. (`denovo_trials.sh`)

- we will run denovo assembly several times to optimize parameters. Fix
  M = n, 1-9 (= the number of mismatches between 2 alleles in either a
  heterozygote (M) or in population (n)) and keep m = 3 (stack
  depth/number of identical reads required to initiate a new allele)

``` bash
#denovo_trials.sh script looks like this:

##Run 1
mkdir denovo_run1
denovo_map.pl --samples /workdir/USER_NAME/demultfilter --popmap /workdir/USER_NAME/popmap_test.txt -m 3 -M 1 -n 1 -T 15 -o /workdir/USER_NAME/denovo_run1
populations -P /workdir/USER_NAME/denovo_run1 -M /workdir/USER_NAME/popmap_test.txt -t 15 -r 0.8

# note -T or -t = # of threads/CPUs to use
# for populations: -P = path to directory containing the Stacks files (output from the denovo run)
#                  -M = path to the population map (in thsi case same as used in the denovo trial)
#                  -r = minimum percentage of individuals in a population req to have a locus to process the locus

##Run 2
mkdir denovo_run2
denovo_map.pl --samples /workdir/USER_NAME/demultfilter --popmap /workdir/USER_NAME/popmap_test.txt -m 3 -M 2 -n 2 -T 15 -o /workdir/USER_NAME/denovo_run2
populations -P /workdir/USER_NAME/denovo_run2 -M /workdir/USER_NAME/popmap_test.txt -t 15 -r 0.8

##Run 3
mkdir denovo_run3
denovo_map.pl --samples /workdir/USER_NAME/demultfilter --popmap /workdir/USER_NAME/popmap_test.txt -m 3 -M 3 -n 3 -T 15 -o /workdir/USER_NAME/denovo_run3
populations -P /workdir/USER_NAME/denovo_run3 -M /workdir/USER_NAME/popmap_test.txt -t 15 -r 0.8

##Run 4
mkdir denovo_run4
denovo_map.pl --samples /workdir/USER_NAME/demultfilter --popmap /workdir/USER_NAME/popmap_test.txt -m 3 -M 4 -n 4 -T 15 -o /workdir/USER_NAME/denovo_run4
populations -P /workdir/USER_NAME/denovo_run4 -M /workdir/USER_NAME/popmap_test.txt -t 15 -r 0.8

##Run 5
mkdir denovo_run5
denovo_map.pl --samples /workdir/USER_NAME/demultfilter --popmap /workdir/USER_NAME/popmap_test.txt -m 3 -M 5 -n 5 -T 15 -o /workdir/USER_NAME/denovo_run5
populations -P /workdir/USER_NAME/denovo_run5 -M /workdir/USER_NAME/popmap_test.txt -t 15 -r 0.8

##Run 6
mkdir denovo_run6
denovo_map.pl --samples /workdir/USER_NAME/demultfilter --popmap /workdir/USER_NAME/popmap_test.txt -m 3 -M 6 -n 6 -T 15 -o /workdir/USER_NAME/denovo_run6
populations -P /workdir/USER_NAME/denovo_run6 -M /workdir/USER_NAME/popmap_test.txt -t 15 -r 0.8

##Run 7
mkdir denovo_run7
denovo_map.pl --samples /workdir/USER_NAME/demultfilter --popmap /workdir/USER_NAME/popmap_test.txt -m 3 -M 7 -n 7 -T 15 -o /workdir/USER_NAME/denovo_run7
populations -P /workdir/USER_NAME/denovo_run7 -M /workdir/USER_NAME/popmap_test.txt -t 15 -r 0.8

##Run 8
mkdir denovo_run8
denovo_map.pl --samples /workdir/USER_NAME/demultfilter --popmap /workdir/USER_NAME/popmap_test.txt -m 3 -M 8 -n 8 -T 15 -o /workdir/USER_NAME/denovo_run8
populations -P /workdir/USER_NAME/denovo_run8 -M /workdir/USER_NAME/popmap_test.txt -t 15 -r 0.8

##Run 9
mkdir denovo_run9
denovo_map.pl --samples /workdir/USER_NAME/demultfilter --popmap /workdir/USER_NAME/popmap_test.txt -m 3 -M 9 -n 9 -T 15 -o /workdir/USER_NAME/denovo_run9
populations -P /workdir/USER_NAME/denovo_run9 -M /workdir/USER_NAME/popmap_test.txt -t 15 -r 0.8
```

Create a `popmap_test.txt` file: This file should be a tab delimited
file listing your sample names and the population they belong to
(usually just “1”). For the trials we will only select a subset of our
samples. Usually we select the 12-20 samples with the highest \# of
reads.

Example popmap file:

    SAMPLE1   1
    SAMPLE2   1
    SAMPLE3   1
    SAMPLE4   1
    SAMPLE5   1

Move `denovo_trials.sh` and the `popmap_test.txt` files into your
`/workdir/USER_NAME` folder using `FileZilla` and run the script. **Make
sure to rename or delete any previous nohup.out files in the
/workdir/USER_NAME folder BEORE you run this script.**

``` bash
nohup bash denovo_trials.sh &
```

Once the denovo_trials script has finished running we will need to
collate the output from the `populations` step of each run. An easy way
to find this information is to search the `nohup.out` file for the
output from each run using the `grep` command in the same folder where
the nohup file is. This information can also be found in the .log files.

``` bash
grep "all/variant/polymorphic sites" nohup.out
```

Plot the variant sites shared by 80% of the samples (output from
populations - found in the nohup.out or the log files) for each M/n
parameter and choose the M/n values that give you the most variant sites
(usually the plot will plateau at this point).

**Note: it might be useful to keep the nohup.out file so you can copy
this to your personal computer and name it something informative.**

Using this M/n value you can now run denovo on all your samples.

## Denovo run on all samples with final M/n values

Prepare a new `popmap_final.txt` with all your samples and transfer this
to the `/workdir/USER_NAME` using FileZilla.

Make a directory for the final denovo analysis:

``` bash
mkdir denovo_final
cd denovo_final 
```

*remember if you have logged in for a new session you will need to run
the commands to add Stacks 2 to your path again.*

Run the denovo pipeline with all of your samples and your chosen M/n
values. Remember to rename or delete your nohup.out file before running
this analysis.

``` bash
nohup denovo_map.pl --samples /workdir/USER_NAME/demultfilter --popmap /workdir/USER_NAME/popmap_final.txt -m 3 -M 5 -n 5 -T 15 -o /workdir/USER_NAME/denovo_final &
```

Now you can run populations with whatever filtering and output options
are appropriate.

For example:

``` bash
populations -P /workdir/USER_NAME/denovo_final -M /workdir/USER_NAME/popmap_final.txt -r 0.80 -p 1 -t 15 --min-maf 0.05 --write-single-snp --structure --vcf --genepop
```

Here is what some of these options mean:

    -P = path to directory containing the Stacks files (output from the denovo run)
    -M = path to the population map (in thsi case same as used in the denovo trial)
    -r = minimum percentage of individuals in a population req to have a locus to process the locus
    --min-maf = minimum minor allele frequency (makes sure alleles only seen a few times, perhaps due to seq errors will not be considered)
    --write-single-snp = writes the first SNP from a locus. This removes the linked SNPs that would also be on that same locus and therefore would be inherited together.
    you can choose the formats that you want the data in - here we have selected: 
              --structure (used for structure plots and easier to visiualize in excel)
              --vcf (variant call format, used by many bioinformatic programs)
              --genepop (this format can be imported into cervus - for paternity analyses)

A useful way view your SNP data is to transfer the .structure file to
your computer. Change the extension on this file to .xls and view the
file in excel.
