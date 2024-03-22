# [Franco](https://github.com/altsplicer) / [***rMATS***]

Script [LINK](https://github.com/Altsplicer/rMATS_script/blob/main/bash/rMATS_script.sub)

[![.img/fig1.jpg](.img/fig1.jpg)](#nolink)

## Overview

This a script used to run a rMATS analysis. rMATS is used detect differential alternative splicing events from RNA-Seq data.

For a tutorial see [link](https://github.com/Xinglab/rmats-turbo/blob/v4.2.0/README.md).

## Slurm script headers
UCI uses the SLURM scheduler so you must use slurm headers to specify how and where you want the job to run. 
You must also name the script SCRIPT_NAME.sub and then run the script using "Sbatch SCRIPT_NAME.sub", while you are in the working directory of the script location. 

For a more in depth view on a SLURM job script headers see https://rcic.uci.edu/slurm/examples.html.

Make note that this script uses the free partition but you can use your free 1000 core hours of the lab's core hours by changing the header.
Otherwise your job can be killed in the free partition.

``` bash
#!/bin/bash
#SBATCH --job-name=rMATS	## Name of the job.
#SBATCH -p free				## partition/queue name
#SBATCH --nodes=8			##(-N) number of nodes to use
#SBATCH --mem=100G ## request 100GB of memory
#SBATCH -e /dfs3b/hertel-lab/fcarranz/project_name/rMATS_analysis/rmats.err	##Error_log
#SBATCH -o /dfs3b/hertel-lab/fcarranz/project_name/rMATS_analysis/rmats.out	##outputfile_log
#SBATCH --mail-user fcarranz@uci.edu
#SBATCH --mail-type=ALL
```

## Load in required modules
``` bash
module load rMATS/4.1.2
module load python/
```

# Annotation file location 
Perferable use the same gtf file you used in your alignment
``` bash
GTF=data/homezvol2/fcarranz/GTF/v45/gencode.v45.primary_assembly.basic.annotation.gtf
```
# Bam file location
``` bash
BAM_DIR=/dfs3b/hertel-lab/fcarranz/project_name/bam_files
```

# Desired directory for output
``` bash
OUTDIR=/dfs3b/hertel-lab/fcarranz/project_name/rMATS_analysis
```
# BAM Files
Bam file used in these examples.

Note this example is a comparison between two conditions with 3 replicates.

rMATS allows you run with no replicates or multiple replicates. See documentation for more info. 
``` bash
1=MB_0_1.bam
2=MB_0_2.bam
3=MB_0_3.bam
4=MB_720_1.bam
5=MB_720_2.bam
6=MB_720_3.bam 
```

# Important to note

Prior to running this script you first must make a text file name b1.txt and b2.txt with the path to the bam_files in replicate separated by only by a comma.

For example b1.txt will be a text file with the following line: /path/to/MB_0_1.bam,/path/to/MB_0_2.bam,/path/to/MB_0_3.bam

As well as a b2.txt file with the following line: /path/to/MB_720_1.bam,/path/to/MB_720_2.bam,/path/to/MB_720_3.bam

/path/to refers to the bam file location so in this example /dfs3b/hertel-lab/fcarranz/project_name/bam_files/example.bam

# OUTPUT FOLDER

``` bash
#name of output folder with rMATS comparison description
SAMPLE_OUTDIR=${OUTDIR}/${1}_${2}_${3}_v_${4}_${5}_${6}
```

# Making the result file directory

``` bash
mkdir -p ${OUTDIR}
mkdir -p ${SAMPLE_OUTDIR}
```
# Text file location

``` bash
TXT=/dfs3b/hertel-lab/fcarranz/project_name/
```
# rMATS execution

``` bash
rmats.py \
--b1 ${TXT}/b1.txt \
--b2 ${TXT}/b2.txt \
-t single \
--readLength 100 \
--gtf ${GTF} \
--od ${SAMPLE_OUTDIR} \
--nthread 8
```

# Notes
--b1 A text file containing a comma separated list of the BAM files for sample 1.

--b2 A text file containing a comma separated list of the BAM files for sample 2.

-t single   Type of read used in the analysis.

--readLength The length of each read.

--gtf Annotation file.

--od The directory for final output.

--nthread 8 The number of threads used. 

See rMATS [Documentation](https://github.com/Xinglab/rmats-turbo/blob/v4.2.0/README.md) for more info

# Please note if any of these commands are not sent in as slurm job but done in the terminal then you need to be in a interactive node, NOT THE LOGIN NODE! You will get in trouble with UCI's HPC staff. 