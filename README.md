# loblolly_pine_analysis


<div id="toc_container">
<p class="toc_title">Contents</p>
<ul class="toc_list">
<li><a href="#First_Point_Header">1 Overview and directory layout</>
<li><a href="#Second_Point_Header">2 Quality control using sickle and fastqc</a></li>
<li><a href="#Third_Point_Header">3 Quality control using sickl</a></li>
<li><a href="#Fourth_Point_Header">4 Aligning reads to a genome using hisat2</a></li>
<li><a href="#Fifth_Point_Header">5 Predicting gene models with BRAKER</a></li>
<li><a href="#Sixth_Point_Header">6 Quality control of gene models using gfacs</a></li>
	<li><a href="#Seventh_Point_Header">7 Functional annotation using EnTAP</a></li>
	<ol><li><a href="#uniprot">Statistics from uniprot database search</a></li>
		<li><a href="#plantfaa">Statistics from ref-seq plant faa 87 database search</a></li>
		<li><a href="#combined">Integrated statistics for i & ii</a></li>
		<li><a href="#taxonomics">Taxonomic breakdown of EnTAP run</a></li></ol>
<li><a href="#Eighth_Point_Header">8 Further statistical breakdown of EnTAP output</a></li>
	<li><a href="#Ninth_Point_Header">9 Final GTF check</a></li>
</ul>
  
  
<h2 id="First_Point_Header">Overview and directory layout</h2>

There are 10 pools containing multple paired-end samples. The directories for each pool are located at: `/UCHC/LABS/Wegrzyn/ConiferGenomes/Pita_10megaWGS` and may be viewed with the following command:

<pre style="color: silver; background: black;">-bash-4.2$ ls /UCHC/LABS/Wegrzyn/ConiferGenomes/Pita_10megaWGS/
<strong>LDP01   LDP06
LDP02   LDP07
LDP03   LDP08
LDP04   LDP09
LDP05   LDP10</strong></pre>

Each pool directory has the paired end data as well as some additional fastas:

<pre style="color: silver; background: black;">-bash-4.2$ ls LDP01
LDP01.fa.gz            SRR7899198.fa          SRR7899201.fa          SRR7899204.fa          SRR7899207.fa          SRR7899210.fa          SRR7899213.fa          SRR7899216.fa
SRR7890516_1.fastq.gz  SRR7899199_1.fastq.gz  SRR7899202_1.fastq.gz  SRR7899205_1.fastq.gz  SRR7899208_1.fastq.gz  SRR7899211_1.fastq.gz  SRR7899214_1.fastq.gz  SRR7899217_1.fastq.gz
SRR7890516_2.fastq.gz  SRR7899199_2.fastq.gz  SRR7899202_2.fastq.gz  SRR7899205_2.fastq.gz  SRR7899208_2.fastq.gz  SRR7899211_2.fastq.gz  SRR7899214_2.fastq.gz  SRR7899217_2.fastq.gz
SRR7890516.fa          SRR7899199.fa          SRR7899202.fa          SRR7899205.fa          SRR7899208.fa          SRR7899211.fa          SRR7899214.fa          SRR7899217.fa
SRR7899197_1.fastq.gz  SRR7899200_1.fastq.gz  SRR7899203_1.fastq.gz  SRR7899206_1.fastq.gz  SRR7899209_1.fastq.gz  SRR7899212_1.fastq.gz  SRR7899215_1.fastq.gz  SRR7899218_1.fastq.gz
SRR7899197_2.fastq.gz  SRR7899200_2.fastq.gz  SRR7899203_2.fastq.gz  SRR7899206_2.fastq.gz  SRR7899209_2.fastq.gz  SRR7899212_2.fastq.gz  SRR7899215_2.fastq.gz  SRR7899218_2.fastq.gz
SRR7899197.fa          SRR7899200.fa          SRR7899203.fa          SRR7899206.fa          SRR7899209.fa          SRR7899212.fa          SRR7899215.fa          SRR7899218.fa
SRR7899198_1.fastq.gz  SRR7899201_1.fastq.gz  SRR7899204_1.fastq.gz  SRR7899207_1.fastq.gz  SRR7899210_1.fastq.gz  SRR7899213_1.fastq.gz  SRR7899216_1.fastq.gz
SRR7899198_2.fastq.gz  SRR7899201_2.fastq.gz  SRR7899204_2.fastq.gz  SRR7899207_2.fastq.gz  SRR7899210_2.fastq.gz  SRR7899213_2.fastq.gz  SRR7899216_2.fastq.gz</strong></pre>

The first step in the analysis was to compile lists of all of the sample IDs in each pool and place the appropriate list in each directory. This was done with the following code:

<pre style="color: silver; background: black;">for folder in LDP*; do for file in $folder/*_1*; do echo $(basename $file _1.fastq.gz) >> $folder/sample_list; done; done;</pre>

The sample list looks like:
<pre style="color: silver; background: black;">
-bash-4.2$ head LDP01/*list
SRR7890516
SRR7899197
SRR7899198
SRR7899199
SRR7899200
SRR7899201
SRR7899202
SRR7899203
SRR7899204
SRR7899205</pre>

<h2 id="Second_Point_Header">Quality control using fastqc and sickle</h2>

First, a subdirectory `sickle` was created in each pool directory: `-bash-4.2$ for folder in LDP*; do mkdir $folder/sickle; done;`

Next the following code was run:
<pre style="color: silver; background: black;">module load sickle
for $folder in LDP*; do \
cat $folder/sample_list | xargs -Iid sickle pe \
-f $folder/id_1.fastq.gz \
-r $folder/id_2.fastq.gz \
-o $folder/sickle/id_forward.trimmed.fastq \
-p $folder/sickle/id_reverse.trimmed.fastq \
-s $folder/sickle/id_singles.trimmed.fastq \
-q 30 \
-l 70; done;</pre>

The quality score threshold was set to `30` and the length threshold set to `70`.
