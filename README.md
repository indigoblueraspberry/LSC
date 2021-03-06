# LSC: a long read error correction tool. It offers fast correction with high sensitivity.

# Getting Started
These simple steps will help you integrate LSC into your transcriptomics analysis pipeline.

* Read the LSC_requirements for running LSC.
* Download and set-up the LSC package.
* Follow the tutorial to see how LSC works on some example data.
* Read the manual if anything is unclear.
* You're ready, Happy LSCing!

# Latest publication
Kin Fai Au, Jason Underwood, Lawrence Lee and Wing Hung Wong 
Improving PacBio Long Read Accuracy by Short Read Alignment [Manuscript] 
PLoS ONE 2012. 7(10): e46679. doi:10.1371/journal.pone.0046679

# Latest News
04-11-2016: Major update version 2.0

*Updates:*
* Added Command line argument support.
* Multi-stage execution modes.
* Support for parallelization. Now execution proceeds in batches of long reads the size of which can be set by --long_read_batch_size N.
* Better compressed intermediate files.
* Added utilities folder.
* Added support for multiple short read files.
* Removed use of configuration file.
* Removed support for BWA, RazerS3, and Novoalign. We are happy to support these if there is interest so please contact us if you prefer them over bowtie2. Bowtie2 is the prefered supported aligner in this release.
* Removed the requirement for long reads to be in PacBio format.

**02-02-2015: Minor updates and bug fixes LSC 1.beta**

*Feature Updates:*

* Updating aligners default command options to gain better performance.
	* Updated Novoalign command options (tested with >= novocraftV3.02.04 version)
	* Updated RazerS3 command options to be compatible with latest version (razers3 3.1.1)
* Added extra clean_up option value to remove all generated intermiediate files in case of issues w/ disk space

*Bug Fixes:*

* Fix a bug in generating full_LR.fa sequences causing some full read sequences to miss couple of bases
 
 12-01-2013: Faster and much less memory-required LSC 1.alpha is released
 
In the LSC 0.3.0 or 0.3.1, we optimized the setting of bowtie2 and BWA to get much more short read alignment, which improve the the accuracy of error correction a lot/ However, the increase of alignments also requires much more running time (on both alignment and the following error correction step) and memory usage. Therefore, a few users met difficulty of running LSC 0.3.0 or 0.3.1.

In LSC 1.alpha, we apply probabilistic algorithm ("SCD" option) to select ""enough" short read alignment for error correction. LSC 1.alpha does NOT sacrifice the error correction performace (sensitivity and specificity). Please see http://www.augroup.org/LSC/LSC_manual.html#aligner Thus, we save running time and memory usage significantly. The running time is 30-50% of LSC 0.3.1. The peak memory usage decreases to ~10G regardless of the data size.

*New features:*

* Added probabilistic algorithm ("SCD" option) to pre-select SR alignments results based on LR-SR alignment coverage depth (Significant improvement in running time and memory usage)
* Removed requirement for loading SR dataset in memory to generate LR-SR mapping file (Significant improvement in memory usage)
* Added option "sort_max_mem" in run.cfg to control maximum memory used by unix sort command to avoid unexpected Mem crash

*Miscellaneous changes:*
* Fixed a bug in generating FASTQ file (it affected some of QualityValue computation results)

If you want to see the manual and tutorial of the old LSC (before 1.alpha), we keep the links of its the Old manual and Old tutorial in the right side bar.

**09-30-2013: More robust and faster LSC 0.3.1**

In LSC 0.3.1, we don't have pseudo chromosome, the alignment time reduced to ~10% (in Bowtie2 mode). And you can re-run some crashed jobs easily now.

*New features:*

* Remove pseudo-chr processing
* Accept compressed SR as input (should be named SR.fa.cps/SR.fa.cps.idx in any folder)
* Added "runLSC -cleanup" option to remove redundant files (per thread split, remaining _tmp files) if the run was successful at the end.
* Changed convertNav to sort reads and then generate LR_SR.map (memory optimization instead of loading all alignments in memory)

*Miscellaneous changes:*

* Changed "print" to system.echo (messages were not printed out in qsub output files)
* Changed a little bit "cleanup" option to keep per thread data (*.aa, *.ab, ..). It was useful when one thread was crashed and we wanted to just re-run that at the end
 
**08-07-2013: Big changes in LSC 0.3**
 
In LSC 0.3, we have a few updates. They are very IMPORTANT updates, new features and small fixes

*Very IMPORTANT updates:*

* Support for Bowtie2 and RazerS3 as initial aligners. Now, BWA, Bowtie2, RazerS3 and Novoalign work in LSC. Please see the comparison details of aligners in the "Short read - Long read aligner#manual".
* Added SR length coverage percentage on LR (SR-covered length/full length of corrected LR) to corrected_LR output file. Here is an example, where the last number 0.82 is the SR length coverage percentage on LR:
```>m111006_202713_42141_c100202382555500000315044810141104_s1_p0/18941/365_1361|0.82```
* Added support for three modes for step-wise runs:

	mode 0: end-to-end
	
	mode 1: generating LR_SR.map file
	
	mode 2: correction step
	
* Generating FASTQ output format based on correction probability given short read coverage. Please refer to LSC paper and manual page for more details. You can select well-corrected reads for downstream analyses by using the quality in FASTQ output or SR length coverage percentage above. Please the the filtering in the "Output#manual".

*New features:*

* Used the python path in the cfg file instead of default user/bin path
* Added option (-clean_up) to remove intermediate files or not (Note: important/useful ones will still be there in temp folder)
* Support for input fastq format for LR (long reads) and/or SR (short reads)
* Updated default BWA and novoalign commands options
* Printing out original LR names in the output file
* Support for printing out version number using -v/-version option

*Small bug fixed:*

* Fixed in removing XZ pattern printed out at the end of some uncorrected_LR sequences
* Fixed samParser bug (which was ignoring some valid alignments in BWA output)

**06-09-2013: BWA is accepted in LSC 0.2.4**
 
We use a short read aligner in the first step of LSC. By default, Novoaligner is used. You can use BWA to run this process as well, which could be much faster. Please find the new aligner options in the webpage ".cfg file format"

The default settings of Novoalign options are:
	```-r All -F FA  -n 300 -o sam -o FullNW ```
    
The default settings of BWA options are:
	```-n 0.08 -o 10 -e 3 -d 0 -i 0 -M 1 -O 1 -E  1 -N ```
    
You can change these aligner setting. The details of these options can be found in the aligners' home page.
In addition, a bug is fixed:

some uncertain corrections may exist at the right ends of the long reads in the old LSC. LSC 0.2.4 settles this problem and likely improves the accuracy further.

**02-06-2013: a bin path bug is fixed in LSC 0.2.2**
If you run LSC at the bin folder (the bin folder is the work directory) or set the bin as the default path, then you may meet this bug. LSC 0.2.3 fixes this bug of finding the correct bin folder. You can download the LSC 0.2.3 
 
**10-17-2012: LSC 0.2.2 Released and a RemoveBothTail bug is fixed**
LSC 0.2.2 fixes the bug of the option "I_RemoveBothTails". LSC 0.2.1 ran this option even if you set "N". It may halt the process in LSC 0.2.1 because the read name does not allow "RemoveBothTails". Now you can choose to use this option or not. 
 
**10-13-2012: The manual and a tutorial are released**
The manual is released and you can also learn LSC by running the example in the tutorial. 
 
**10-4-2012: LSC paper is released**
Kin Fai Au, Jason Underwood, Lawrence Lee and Wing Hung Wong 
**Improving PacBio Long Read Accuracy by Short Read Alignment**
 
**8-12-2012 - LSC 0.2.1 Released**
LSC 0.2.1 fixes the bug of python path. Another bug of removing redundant reads is also fixed. LSC takes a long read data sets (>=100bp) and a short reads data sets (50 - 100bp) as input. They should be in FASTA format. Running time is almost linear with the the number of threads.

* Python (version 2.6 or higher) should be installed in the default user bin "#!/usr/bin/python"
* Novoalign should be in your default path. The version V2.07.10 is recommended.
* A new option of using nonredandunt reads that save ~40% running time

**8-7-2012: some bugs are found**

* Great thanks to Hans Jansen@SEQanswers for testing LSC 0.2. Some bugs of python and novoalign path setting will be fix in the coming verison very soon. The thread in SEQanswers may be helpful for your questions: http://seqanswers.com/forums/showthread.php?p=80859#post80859

If you need to run LSC 0.2 now, please add novoalign in your default path and change the first lines of all scripts 
"#!/home/stow/swtree/bin/python2.6" to "#!/usr/bin/python".

**5-2-2012: LSC 0.2 Released**
LSC 0.2 takes a long read data sets (>=100bp) and a short reads data sets (50 - 100bp) as input. They should be in FASTA format. Running time is almost linear with the the number of threads.

* More optional for raw data prefilter
* Multi-threading is available
* Reduced redundant temp files
For detailed information about this release, please see the release notes.

# Release notes

**LSC 2.0 - Release Notes**

* Removed the requirement for long reads to be in a Pacific Biosciences format.
* Removed PacBio specific long read processing.
* Removed support for aligners BWA, RazerS3 and Novoalign. Please contact us if these would be useful.
* Removed the use of run.cfg files.
* Removed support for Mac OSX. Nothing specifically was written to specifically disable Mac functionality, but use on Mac OSX functionality has not been tested for this version and should not be expected to function.
* Added command line argument support. runLSC.py could minimally be run with --long_reads and --short_reads and --output specified. Access to other parameters are available through command line arguments.
* Added a multi-stage execution modes. This makes parallelization on a cluster much easier to implement.

	0: default – end to end run
	
	1: Prepare compressed short reads, and compressed and indexed batches of long reads.
	
	2: Execute correction on all the batches. or (alternate) use --parallelized_mode_2 X: Execute correction on one batch (X).
	3: Combine outputs from mode 2 to produce final outputs.
	
* Added the parameter --long_read_batch_size X option so that you can specify how many long reads will be corrected in each batch.
* Added support for hisat, but parameters have not been optimized and bowtie2 performs best.
* Added samtools as a requirement, that is also included as a linux executable binary. If the software is not installed under 'samtools' or specified by the user, then the packaged version of samtools will be used. This drastically reduces the size of intermediate files produced from alignments.
* Added options for a specific temporary directory --specific_tempdir that can be used to specify where to store intermediate files if you want to keep them. This parameter is required when running in step-by-step modes i.e. 1,2,3 rather than just 0. Otherwise --tempdir can be used to specify where to store temporary files that will eventually be removed. If neither are specified the /tmp directory will be used by default.
* Added a utilities folder. Now it is the new home of filter_corrected_reads.py, and some sequence parsing files called by the main LSC program.

**LSC 1.beta - Release Notes** 

*Feature Updates:*

* Updating aligners default command options to gain better performance.
	* Updated Novoalign command options (tested with >= novocraftV3.02.04 version)
	* Updated RazerS3 command options to be compatible with latest version (razers3 3.1.1)
* Added extra clean_up option value to remove all generated intermiediate files in case of issues w/ disk space

*Bug Fixes:*

* Fix a bug in generating full_LR.fa sequences causing some full read sequences to miss couple of bases


**LSC 1.alpha - Release Notes**

* In the LSC 0.3.0 or 0.3.1, we optimized the setting of bowtie2 and BWA to get much more short read alignment, which improve the the accuracy of error correction a lot/ However, the increase of alignments also requires much more running time (on both alignment and the following error correction step) and memory usage. Therefore, a few users met difficulty of running LSC 0.3.0 or 0.3.1.

* In LSC 1.alpha, we apply probabilistic algorithm ("SCD" option) to select ""enough" short read alignment for error correction. LSC 1.alpha does NOT sacrifice the error correction performace (sensitivity and specificity). Please see http://www.augroup.org/LSC/LSC_manual.html#aligner Thus, we save running time and memory usage significantly. **The running time is 30-50% of LSC 0.3.1. The peak memory usage decreases to ~10G regardless of the data size.**

*New features:*

* Added probabilistic algorithm ("SCD" option) to pre-select SR alignments results based on LR-SR alignment coverage depth (Significant improvement in running time and memory usage)
* Removed requirement for loading SR dataset in memory to generate LR-SR mapping file (Significant improvement in memory usage)
* Added option "sort_max_mem" in run.cfg to control maximum memory used by unix sort command to avoid unexpected Mem crash

*Miscellaneous changes:*

* Fixed a bug in generating FASTQ file (it affected some of QualityValue computation results)

If you want to see the manual and tutorial of the old LSC (before 1.alpha), we keep the links of its the Old manual and Old tutorial in the right side bar.

**LSC 0.3.1 - Release Notes**
In LSC 0.3.1, we don't have pseudo chromosome, the alignment time reduced to ~10% (in Bowtie2 mode). And you can re-run some crashed jobs easily now.

*New features:*

* Remove pseudo-chr processing
* Accept compressed SR as input (should be named SR.fa.cps/SR.fa.cps.idx in any folder)
* Added "runLSC -cleanup" option to remove redundant files (per thread split, remaining _tmp files) if the run was successful at the end.
* Changed convertNav to sort reads and then generate LR_SR.map (memory optimization instead of loading all alignments in memory)

*Miscellaneous changes:*

* Changed "print" to system.echo (messages were not printed out in qsub output files)
* Changed a little bit "cleanup" option to keep per thread data (*.aa, *.ab, ..). It was useful when one thread was crashed and we wanted to just re-run that at the end

**LSC 0.3 - Release Notes**
In LSC 0.3, we have a few updates. They are very IMPORTANT updates, new features and small fixes

*Very IMPORTANT updates:*

* Support for RazerS3 and Bowtie2 as initial aligners. Now, BWA, Bowtie2, RazerS3 and Novoalign work in LSC.
* Added SR length coverage percentage on LR (SR-covered length/full length of corrected LR) to corrected_LR output file. Here is an example, where the last number 0.82 is the SR length coverage percentage on LR:
```>m111006_202713_42141_c100202382555500000315044810141104_s1_p0/18941/365_1361|0.82```
* Added support for three modes for step-wise runs:

	mode 0: end-to-end
	
	mode 1: generating LR_SR.map file
	
	mode 2: correction step
	
* Generating fastq output format:

Using the correction probability given coverage in the LSC paper and fitted a log function to it and then used the probability values to compute Sanger quality score: 33 - 10 * log10(1 - p). For the locations:

- without any SR coverage I used the default quality score of p = 0.725 (the same in your paper).

- with SR coverage but without any correction point, I used the number of covered SRs

- with SR coverage in a correction point (either because of compression or mismatch), I used number of SRs that had similar sequence with the substitute bases (i.e. the number of covered SRs that have the max number of similar sequence not the total number covered SRs.)

*New features:*

* Used the python path in the cfg file instead of default user\bin path
* Added option (-clean_up) to remove intermediate files or not (Note: important/useful ones will still be there in temp folder)
* Support for input fastq format for LR (long reads) and/or SR (short reads)
* Updated default BWA and novoalign commands options
* Printing out original LR names in the output file
* Support for printing out version number and (-v/-version) option

*Small fixes:*

* Fixed in removing XZ pattern from end of uncorrected_LR file
* Fixed samParser bug (which was ignoring some valid alignments in case of BWA)

**LSC 0.2.4 - Release Notes**

1. Besides the default aligner Novoalign, BWA can be also used as the initial aligner from this version. Please find the new aligner options in the webpage ".cfg file format"

2. Some uncertain corrections may exsit at the right ends of the long reads in the old LSC. LSC 0.2.4 settles this problem and likely improves the accuracy further.

**LSC 0.2.3 - Release Notes**

If you run LSC at the bin folder (the bin folder is the work directory) or set the bin as the default path, then you may meet a bug. LSC 0.2.3 fixes this bug of finding the correct bin folder. 

**LSC 0.2.2 - Release Notes**

LSC 0.2.2 fixes the bug of the option "I_RemoveBothTails". LSC 0.2.1 ran this option even if you set "N". It may halt the process in LSC 0.2.1 because the read name does not allow "RemoveBothTails". Now you can choose to use this option or not.

**LSC 0.2.1 - Release Notes**

LSC 0.2.1 fixes the bug of python path. Another bug of removing redundant reads is also fixed. LSC takes a long read data sets (>=100bp) and a short reads data sets (50 - 100bp) as input. They should be in FASTA format. Running time is almost linear with the the number of threads.

* Python (version 2.6 or higher) should be installed in the default user bin ```"#!/usr/bin/python"```
* Novoalign should be in your default path. The version V2.07.10 is recommended.
* A new option of using nonredandunt reads that save ~40% running time

**LSC 0.2 - Release Notes**

LSC 0.2 takes a long read data sets (>=100bp) and a short reads data sets (50 - 100bp) as input. They should be in FASTA format. Running time is almost linear with the the number of threads.

* More optional for raw data prefilter
* Multi-threading is avaiable
* Reduced redundant temp files

# Tutorial


This tutorial will help you get started with LSC by demonstrating how to error correct 57,244 PacBio long reads with 1 million short reads of length 101bp. If you are interested in parallelizing your run, please pay close attention to step 3, where we show an alternate step 3 where you can see how to set up the parallelized execution. If you experience any problems following these steps, please don't hesitate to contact kinfai.au@osumc.edu.

### Step 0 - Requirements
LSC aims to fully integrate into a variety of analysis pipelines. If any of the following requirements conflict with your lab's set up, please contact us.
 
System Requirements: 

* Linux operating system
* Python 2.7
* 16GB RAM recommended (8GB minimum for 5G bp short reads)
* Free hard drive space exceeding 3x The uncompressed size of your short read data
 
Read Format:

* Single-end short reads or multiple files for paired-end short reads supported
* Length of short reads should be greater or equal to 50bp
* There is no requirement that reads must be the same length.
* Input to LSC are sequencer reads in FASTA or FASTQ format.

Software:

* Short read mapper: Bowtie2 

If you require support of another short read mapper please contact us.



### Step 1 - Download lastest version of LSC

Extract the source in the location of your choice. After extracting the location of the executable for LSC is:

```LSC-2.0/bin/runLSC.py```

You are welcome to add the bin directory to your path if you want to have runLSC.py command installed, but it is not necessary. If you desire a different python other than one located in /usr/bin/python you can execute LSC through your preferred python. Running the command with the -h option will give detailed descriptions of parameters. Be sure to have the bowtie2 aligner installed, as per the requirements.

```$ LSC-2.0/bin/runLSC.py -h```

or
 
```$ python LSC-2.0/bin/runLSC.py -h```


```
usage: runLSC.py [-h] [--long_reads LONG_READS]
                 [--short_reads [SHORT_READS [SHORT_READS ...]]]
                 [--short_read_file_type {fa,fq,cps}] [--threads THREADS]
                 [--tempdir TEMPDIR | --specific_tempdir SPECIFIC_TEMPDIR]
                 [-o OUTPUT]
                 [--mode {0,1,2,3} | --parallelized_mode_2 PARALLELIZED_MODE_2]
                 [--aligner {hisat,bowtie2}] [--sort_mem_max SORT_MEM_MAX]
                 [--minNumberofNonN MINNUMBEROFNONN] [--maxN MAXN]
                 [--error_rate_threshold ERROR_RATE_THRESHOLD]
                 [--short_read_coverage_threshold SHORT_READ_COVERAGE_THRESHOLD]
                 [--long_read_batch_size LONG_READ_BATCH_SIZE]
                 [--samtools_path SAMTOOLS_PATH]
 
LSC 2.0: Correct errors (e.g. homopolymer errors) in long reads, using short
read data
 
optional arguments:
  -h, --help            show this help message and exit
  --long_reads LONG_READS
                        FASTAFILE Long reads to correct. Required in mode 0 or
                        1. (default: None)
  --short_reads [SHORT_READS [SHORT_READS ...]]
                        FASTA/FASTQ FILE Short reads used to correct the long
                        reads. Can be multiple files. If choice is cps reads,
                        then there must be 2 files, the cps and the idx file
                        following --short reads. Required in mode 0 or 1.
                        (default: None)
  --short_read_file_type {fa,fq,cps}
                        Short read file type (default: fa)
  --threads THREADS     Number of threads (Default = cpu_count) (default: 0)
  --tempdir TEMPDIR     FOLDERNAME where temporary files can be placed
                        (default: /tmp)
  --specific_tempdir SPECIFIC_TEMPDIR
                        FOLDERNAME of exactly where to place temproary
                        folders. Required in mode 1, 2 or 3. Recommended for
                        any run where you may want to look back at
                        intermediate files. (default: None)
  -o OUTPUT, --output OUTPUT
                        FOLDERNAME where output is to be written. Required in
                        mode 0 or 3. (default: None)
  --mode {0,1,2,3}      0: run through, 1: Prepare homopolymer compressed long
                        and short reads. 2: Execute correction on batches of
                        long reads. Can be superseded by --parallelized_mode_2
                        where you will only execute a single batch. 3: Combine
                        corrected batches into a final output folder.
                        (default: 0)
  --parallelized_mode_2 PARALLELIZED_MODE_2
                        Mode 2, but you specify a sigle batch to execute.
                        (default: None)
  --aligner {hisat,bowtie2}
                        Aligner choice. hisat parameters have not been
                        optimized, so we recommend bowtie2. (default: bowtie2)
  --sort_mem_max SORT_MEM_MAX
                        -S option for memory in unix sort (default: None)
  --minNumberofNonN MINNUMBEROFNONN
                        Minimum number of non-N characters in the compressed
                        read (default: 40)
  --maxN MAXN           Maximum number of Ns in the compressed read (default:
                        None)
  --error_rate_threshold ERROR_RATE_THRESHOLD
                        Maximum percent of errors in a read to use the
                        alignment (default: 12)
  --short_read_coverage_threshold SHORT_READ_COVERAGE_THRESHOLD
                        Minimum short read coverage to do correction (default:
                        20)
  --long_read_batch_size LONG_READ_BATCH_SIZE
                        INT number of long reads to work on at a time. This is
                        a key parameter to adjusting performance. A smaller
                        batch size keeps the sizes and runtimes of
                        intermediate steps tractable on large datasets, but
                        can slow down execution on small datasets. The default
                        value should be suitable for large datasets. (default:
                        500)
  --samtools_path SAMTOOLS_PATH
                        Path to samtools by default assumes its installed. If
                        not specified, the included version will be used.
                        (default: samtools)


```

### Step 2 - Download and extract the example files

Download example at:

https://github.com/jason-weirather/LSC/tree/master/example

Extract the example to an empty folder of your choice. After extracting the folder should contain the following files:

```$ ls example-LSC-2.0/
LR.fa   SR.fa 
```

LR.fa contains the long reads in FASTA format. SR.fa contains the short reads in FASTA format.


### Step 3 - Prepare and execute the LSC command

First lets prepare and execute a simple LSC command.

```
$ LSC-2.0/bin/runLSC.py --long_reads example-LSC-2.0/LR.fa --short_reads example-LSC-2.0/SR.fa 
      --specific_tempdir tutorial_temp --output tutorial_output
```

```
 
=== Welcome to LSC 2.0 ===
===rename LR:===
0:00:05.107854
=== sort and uniq SR data ===
0:00:40.875131
===compress SR:===
0:02:01.445827
===batch count:===
Work will begin on 115 batches.
0:02:01.464558
0:02:01.464596
===compress LR, samParser LR.??.cps.nav:===
... executing batch 115.2 (index)        
0:03:00.675972
... step 3 aligning 115/115   
===compress LR, samParser LR.??.cps.nav:===
... executing batch 115.7 (correcting)   
0:55:20.210951
===produce outputs:===
Producing outputs from 115 batches
0:55:26.891062
```

### Step 3 (alternative) - Parallelize execution of the LSC command

We will have to execute one stage at a time for parallel execution. First is --mode 1 where homopolymer compressed long and short reads are created. --specific_temp dir is required for this step-wise mode of execution.

```
$ LSC-2.0/bin/runLSC.py --mode 1 --long_reads example-LSC-2.0/LR.fa --short_reads example-LSC-2.0/SR.fa 
      --specific_tempdir tutorial_temp
``` 

```
=== Welcome to LSC 2.0 ===
===rename LR:===
0:00:05.240716
=== sort and uniq SR data ===
0:00:39.545451
===compress SR:===
0:01:56.110217
===batch count:===
Work will begin on 115 batches.
0:01:56.157878
0:01:56.157921
```

Next we need to find out how many parallel jobs we have to run.

```
$ cat tutorial_temp/batch_count
``` 
```    
115
``` 
    
We need to execute 115 jobs.  These are numbered 1-115 and can be run individually by setting
```--parallelized_mode_2 X```, where X is your job number.  You must use the same ```--specific_tempdir``` you used in ```--mode 1```. You can execute these commands in parallel.
    
 
```    
$ LSC-2.0/bin/runLSC.py --parallelized_mode_2 1 
      --specific_tempdir tutorial_temp
```
 
    
```
=== Welcome to LSC 2.0 ===
===Parallelized mode:===
Will begin work on batch #1
0:00:00.001246
===compress LR, samParser LR.??.cps.nav:===
... executing batch 1.2 (index)        
0:00:04.281204
... step 3 aligning 1/115   
===compress LR, samParser LR.??.cps.nav:===
... executing batch 1.7 (correcting)   
0:01:21.556175
```
    
After execute of that command for ```--parallelized_mode_2 1 ```through ```--parallelized_mode_2 115```. Then we can use ```--mode 3 ```to put all the batches back together. In step-wise execution, output is required to be specified for ```mode 3```.  ```--specific_tempdir ``` needs to be the same directory specified in mode 1 and mode 2.
    
```
$ LSC-2.0/bin/runLSC.py --mode 3 
      --specific_tempdir tutorial_temp --output tutorial_output
```
 
    
``` 
=== Welcome to LSC 2.0 ===
0:00:00.004400
===produce outputs:===
Producing outputs from 115 batches
0:00:07.298073
```   
 
    
There is also another user guide and download links for LSC: https://github.com/jason-weirather/LSC/.
 
    
# Manual

### Installation

No explicit installation is required for LSC. You may save the LSC code to any location, and you are welcome to add the bin/ directory to your path if you want.

You need to Python 2.7 installed in your computer which should be included by default with most linux distributions. Please see LSC requirements for more details.

### Using LSC

Firstly, see the tutorial on how to use LSC on some example data.

In order to use LSC on your own data, you will need to decide if doing a full run or a parallelized run would be more efficient. If you are running a large data set (Tens of millions of short reads, and hundreds of thousands of long reads or bigger), we highly recommend doing a parallelized run. A step by step guide for both normal and parallized execution can be found in the tutorial. Too access the help regarding the command line options, you can used -h option with runLSC.py.


### Executing runLSC.py

"runLSC.py" is the main program in the LSC package. Output is written to the "--output" folder. Details of the output files are described in file formats. Its options are described here and can be accssed using the -h option when running runLSC.py:

```$ LSC-2.0/bin/runLSC.py -h```

The required options differ depending on run mode, but the most basic way to run LSC is to do an end to end run that does not save temporary files.
    ```$ LSC-2.0/bin/runLSC.py --long_reads myLR.fa --short_reads mySR.fa --output myoutputdir```
    
You may also execute LSC step-by-step. To do this we must specify a temporary directory that will not be deleted using --specific_tempdir.
```
    $ LSC-2.0/bin/runLSC.py --mode 1 --long_reads myLR.fa --short_reads mySR.fa 
    --specifc_tempdir mytempdir
    
    $ LSC-2.0/bin/runLSC.py --mode 2 --specifc_tempdir mytempdir
    
    $ LSC-2.0/bin/runLSC.py --mode 3 --specifc_tempdir mytempdir --output myoutdir
 
```
mode 0 (default): end-to-end LSC run.

mode 1: Homopolymer compresses long and short reads.

mode 2: runs the alignment and LSC correction steps.

mode 3: Combine all corrected reads to for final outputs.


Alternatively a parallelized work flow can be done by replacing the --mode 2 paramater with --parallelized_mode_2 X, where X is the batch number. You can execute the command for each batch. If you need to find the number of batches, after running --mode 1, you can look in mytempdir/batch_count. You will need to execute 1 to and including batch_count.

```
$ LSC-2.0/bin/runLSC.py --mode 1 --long_reads myLR.fa --short_reads mySR.fa 
    --specifc_tempdir mytempdir
```

Now the compressed long and short reads are ready for analysis.

 ```$ cat mytempdir/batch_count```
 
This will tell you how many X batches to run. Now for 1 to X:

``` $ LSC-2.0/bin/runLSC.py --parallized_mode_2 X --specifc_tempdir mytempdir ```

Finally you can combine all the outputs back together.

``` $ LSC-2.0/bin/runLSC.py --mode 3 --specifc_tempdir mytempdir --output myoutdir ```

Please refer to the Tutorial for an example. 

### Input files

LSC accepts one long-read sequences file (to be corrected) and one or more short-read sequences file as input. The input files could be in standard fasta or fastq formats. Gzipped inputs are supported.

Note: As part of LSC algorithm, it generates homopolyer-compressed short-read sequences before alignment. If you have already run LSC with the same SR dataeset you can skip this step by using previously generated homopolyer-compressed SR files. (You can find SR.fa.cps and SR.fa.idx in temp folderpath.) Please keep in mind if you are using the cps and idx SR files, you will need to specify both their locations as two parameters following --short_reads.

### Output files

There are four output files: corrected_LR.fa, corrected_LR.fq, full_LR.fa, uncorrected_LR.fa in output folder:

* corrected_LR

As long as there are short reads (SR) mapped to a long read, this long read can be corrected at the SR-covered regions. (Please see more details from the paper). The sequence from the left-most SR-covered base to the right-most SR-covered base is outputted in the file corrected_LR. The output readname format is
  
  ```<original readname>|<percentage of corrected output sequence covered by short reads>```

example:
  
  ```m111006_202713_42141_c100202382555500000315044810141104_s1_p0/16|0.81```

* full_LR

Although the terminus sequences are uncorrected, they are concatenated with their corrected sequence (corrected_LR) to be a "full" sequence. Thus, this sequence covers the equivalent length as the raw read and is outputted in the file full_LR.fa

* uncorrected_LR

This is the negative control. uncorrected_LR.fa contains the left-most SR-covered base to the right-most SR-covered base (equivalent region in corrected_LR) but not error corrected. Thus, it is fragments of the raw reads.

The quality (error rate) of corrected reads in corrected_LR.fq depends on its SR coverage and it uses Sanger standard encoding. 

|SRs Coverage	|Error Probability*|
|--------------:|:----------------:|
|0		|0.275	           |
|1		|0.086             |
|2		|0.063             |
|3		|0.051             |
|4	        |0.041             |
|5	        |0.034             |
|6	        |0.028             |
|7	        |0.023             |
|8	        |0.018             |
|9	        |0.014             |
|10	        |0.011             |
|11	        |0.008             |
|12	        |0.005             |
|13	        |0.002             |
|>= 14	        |~0.000            |

Reference: LSC paper 

* Error probablity is modeled with logarithmic funtion fitted to real data error-probabilities computed in the paper.

Note: Part of corrected_LR sequence without any short read coverage would have the default 27.5% error rate. If input LRs are in fastq format, the original quality values are not used here. 

###  Module: filter_corrected_reads.py

In addition to quality information in corrected_LR.fq file, you can also select corrected LR sequences with higher percentage of SR covered length using filter_corrected_reads.py script in the bin folder.

```LSC_bin_path/utilities/filter_corrected_reads.py <SR_covered_length_threshold> <corrected_LR.fa or fq file> > <output_file> ```

exapmle:   

```python bin/filter_corrected_reads.py 0.5 output/corrected_LR.fa > output/corrected_LR.filtered.fa```

You can also select "best" reads for your downstream analysis by mapping corrected LRs to the reference genome or annotation (for RNA-seq analysis). Then, filter the reads by mapping score or percentage of base match (e.g. "identity" in BLAT)


### Short read-Long read Aligner

LSC uses a short read aligner in the first step. By default, Bowtie2 is used. You can have BWA, , Novoalign or RazerS (v3) to run this step as well.

Default aligners setting are:

Bowtie2 : 

```-a -f -L 15 --mp 1,1 --np 1 --rdg 0,1 --rfg 0,1 --score-min L,0,-0.08 --end-to-end --no-unal```

BWA : 

```-n 0.08 -o 10 -e 3 -d 0 -i 0 -M 1 -O 0 -E 1 -N```

Novoalign* : 

```-r All -F FA -n 300 -o sam```

RazerS3 : 

```-i 92 -mr 0 -of sam```

You can change these settings through .cfg file. Please refer to their manuals for more details. 

* Note: novoalign has limitation on read length. If you are using LSC with novoalign, please make sure your short reads length do not exceed maximum threashold. Following figures compare LSC correction results configured with different supported aligners. Identity metric is defined as number-of-matchs/error-corrected-read-length after aligning reads to reference genome using Blat.


![alt text](https://github.com/RuRuYa/LSC/blob/master/number_of_mappable_bases.png) 
![alt text](https://github.com/RuRuYa/LSC/blob/master/number_of_mappable_reads.png)

Data-set:

* LongReads: human brain cerebellum polyA RNA processed to enrich for full-length cDNA for the PacBio RS platform under C2 chemistry conditions as CLR data link
* ShortReads: human brain data from Illumina’s Human Body Map 2.0 project (GSE30611)

Based on your system configuration, you can select the aligner which fits better with your CPU or Memory resources. 

The below table is derived experimentally by running LSC using different aligners on above-mentioned data-set. 

 |        |CPU 	 | Memory |
 |--------|:----:|:-------|
 |BWA 	  |Less  | Less   |
 |Bowtie2 |More  | Less   |
 |RazerS3 |More  | More   |
 
 
 ### Short-read coverage depth (SCD)
 
LSC uses consensus of short-read mapping results to correct long read sequences. In case of having high SR coverage, pile of SRs mapped to a LR segment would significantly increase running time and memory usage in correction step, while having repetitive (redundant) information. By setting SCD parameter in run.cfg file, LSC uses a probabilistic algorithm to randomly select bounded number of SR alignemt results for each LR region in order to maintain expected SR coverage depth of SCD value. This would eliminate high memory peaks in corection step due to pile of SRs mapped in high coverage or repetitive regions. Based on our experiment on multilpe datasets, setting SCD = 20 gave comparable results w.r.t SCD = -1 (using all alignment results,i.e. without any bounded coverage limit).
 
###  Execution Time

Following CPU and execution times are suggested-usage using LSC.0.2.2 and LSC 1.alpha on our clusters with six thread. These figures will greatly differ based on your system configuration.


100,000 PacBio long reads X 64 million 75bp Illumina short reads (Dataset)

* LSC 1.alpha (w/ bowtie2, SCD=20): Time = 09:40:33, Max vmem = 8.532G
* LSC.0.2.2(w/ novoalign): Time = 16:12:00, Max vmem = 27.506G
 
 






