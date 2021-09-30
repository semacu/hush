
## RIP-seq

### obtain fastq files

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/fastq

ls -lh *.fastq.gz
# -rwxrwxrwx 1 kzqv978 xem-scp-qbio 104M Apr 29 09:42 empty1_R1.fastq.gz
# -rwxrwxrwx 1 kzqv978 xem-scp-qbio 139M Apr 29 09:42 empty1_R2.fastq.gz
# -rwxrwxrwx 1 kzqv978 xem-scp-qbio 182M Apr 29 09:42 empty2_R1.fastq.gz
# -rwxrwxrwx 1 kzqv978 xem-scp-qbio 244M Apr 29 09:42 empty2_R2.fastq.gz
# -rwxrwxrwx 1 kzqv978 xem-scp-qbio 324M Apr 29 09:42 HA1_R1.fastq.gz
# -rwxrwxrwx 1 kzqv978 xem-scp-qbio 437M Apr 29 09:42 HA1_R2.fastq.gz
# -rwxrwxrwx 1 kzqv978 xem-scp-qbio 234M Apr 29 09:42 HA2_R1.fastq.gz
# -rwxrwxrwx 1 kzqv978 xem-scp-qbio 313M Apr 29 09:42 HA2_R2.fastq.gz
# -rw-rw---- 1 kzqv978 xem-scp-qbio 201M May 28 00:11 SKOempty1_R1.fastq.gz
# -rw-rw---- 1 kzqv978 xem-scp-qbio 266M May 28 00:11 SKOempty1_R2.fastq.gz
# -rw-rw---- 1 kzqv978 xem-scp-qbio 219M May 28 00:11 SKOempty2_R1.fastq.gz
# -rw-rw---- 1 kzqv978 xem-scp-qbio 291M May 28 00:11 SKOempty2_R2.fastq.gz
# -rw-rw---- 1 kzqv978 xem-scp-qbio 422M May 28 00:12 SKOHA1_R1.fastq.gz
# -rw-rw---- 1 kzqv978 xem-scp-qbio 559M May 28 00:12 SKOHA1_R2.fastq.gz
# -rw-rw---- 1 kzqv978 xem-scp-qbio 261M May 28 00:11 SKOHA2_R1.fastq.gz
# -rw-rw---- 1 kzqv978 xem-scp-qbio 344M May 28 00:12 SKOHA2_R2.fastq.gz
# -rw-rw---- 1 kzqv978 xem-scp-qbio 189M May 28 20:37 WTempty1_R1.fastq.gz
# -rw-rw---- 1 kzqv978 xem-scp-qbio 250M May 28 20:37 WTempty1_R2.fastq.gz
# -rw-rw---- 1 kzqv978 xem-scp-qbio 182M May 28 20:37 WTempty2_R1.fastq.gz
# -rw-rw---- 1 kzqv978 xem-scp-qbio 241M May 28 20:37 WTempty2_R2.fastq.gz
# -rw-rw---- 1 kzqv978 xem-scp-qbio 439M May 28 20:37 WTHA1_R1.fastq.gz
# -rw-rw---- 1 kzqv978 xem-scp-qbio 581M May 28 20:38 WTHA1_R2.fastq.gz
# -rw-rw---- 1 kzqv978 xem-scp-qbio 320M May 28 20:37 WTHA2_R1.fastq.gz
# -rw-rw---- 1 kzqv978 xem-scp-qbio 425M May 28 20:38 WTHA2_R2.fastq.gz

for fq in *.fastq.gz; do echo $fq, `zcat $fq | paste - - - - | wc -l`; done
# empty1_R1.fastq.gz, 4729885
# empty1_R2.fastq.gz, 4729885
# empty2_R1.fastq.gz, 8282694
# empty2_R2.fastq.gz, 8282694
# HA1_R1.fastq.gz, 14786048
# HA1_R2.fastq.gz, 14786048
# HA2_R1.fastq.gz, 10722549
# HA2_R2.fastq.gz, 10722549
# SKOempty1_R1.fastq.gz, 7323898
# SKOempty1_R2.fastq.gz, 7323898
# SKOempty2_R1.fastq.gz, 7979937
# SKOempty2_R2.fastq.gz, 7979937
# SKOHA1_R1.fastq.gz, 15609117
# SKOHA1_R2.fastq.gz, 15609117
# SKOHA2_R1.fastq.gz, 9550120
# SKOHA2_R2.fastq.gz, 9550120
# WTempty1_R1.fastq.gz, 6866855
# WTempty1_R2.fastq.gz, 6866855
# WTempty2_R1.fastq.gz, 6616513
# WTempty2_R2.fastq.gz, 6616513
# WTHA1_R1.fastq.gz, 16267305
# WTHA1_R2.fastq.gz, 16267305
# WTHA2_R1.fastq.gz, 11772624
# WTHA2_R2.fastq.gz, 11772624
```

- empty and HA are SETDB1 KO
- SKOempty and SKOHA are SETDB1 KO
- WTempty and WTHA are WT



### quality check

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/fastq

mkdir ../fastqc

for fq in *.fastq.gz
do
  bname=${fq%.fastq.gz}
  sbatch -J $bname -o ../fastqc/$bname.log --mem 4G -t 1-0 --wrap "fastqc --noextract --nogroup -q -o ../fastqc $fq"
done
```



### umi_tools and cutadapt

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/fastq

mkdir ../fastq_umi

for fq2 in *_R2.fastq.gz
do
  bname=${fq2%_R2.fastq.gz}
  fq1=${fq2/_R2./_R1.}
  #echo $fq1, $fq2, $bname
  sbatch -J $bname -o ../fastq_umi/$bname.log --mem 8G -t 1-0 --wrap "umi_tools extract --bc-pattern=NNNNNNNN -L ../fastq_umi/${bname}_umi.log -E ../fastq_umi/${bname}_umi.err -I $fq2 -S ../fastq_umi/${bname}_R2.tmp.fastq.gz --read2-in=$fq1 --read2-out=../fastq_umi/${bname}_R1.tmp.fastq.gz && \
  cutadapt -j 0 -f fastq --match-read-wildcards --times 1 -e 0.1 --quality-cutoff 6,6 -U 3 -m 18 -a AGATCGGAAGAGCACACGTCTGAACTCCAGTCAC -g CTTCCGATCTACAAGTT -g CTTCCGATCTTGGTCCT -A AACTTGTAGATCGGA -A AGGACCAAGATCGGA -A ACTTGTAGATCGGAA -A GGACCAAGATCGGAA -A CTTGTAGATCGGAAG -A GACCAAGATCGGAAG -A TTGTAGATCGGAAGA -A ACCAAGATCGGAAGA -A TGTAGATCGGAAGAG -A CCAAGATCGGAAGAG -A GTAGATCGGAAGAGC -A CAAGATCGGAAGAGC -A TAGATCGGAAGAGCG -A AAGATCGGAAGAGCG -A AGATCGGAAGAGCGT -A GATCGGAAGAGCGTC -A ATCGGAAGAGCGTCG -A TCGGAAGAGCGTCGT -A CGGAAGAGCGTCGTG -A GGAAGAGCGTCGTGT -o ../fastq_umi/$fq1 -p ../fastq_umi/$fq2 ../fastq_umi/${bname}_R1.tmp.fastq.gz ../fastq_umi/${bname}_R2.tmp.fastq.gz > ../fastq_umi/${bname}_cutadapt.txt && \
  rm ../fastq_umi/${bname}_R1.tmp.fastq.gz ../fastq_umi/${bname}_R2.tmp.fastq.gz"
done

cd ../fastq_umi

tail *.log

grep -A 10 "Summary" *_cutadapt.txt
# empty1_cutadapt.txt:=== Summary ===
# empty1_cutadapt.txt-
# empty1_cutadapt.txt-Total read pairs processed:          4,729,885
# empty1_cutadapt.txt-  Read 1 with adapter:                 512,741 (10.8%)
# empty1_cutadapt.txt-  Read 2 with adapter:               1,437,545 (30.4%)
# empty1_cutadapt.txt-Pairs that were too short:              41,201 (0.9%)
# empty1_cutadapt.txt-Pairs written (passing filters):     4,688,684 (99.1%)
# empty1_cutadapt.txt-
# empty1_cutadapt.txt-Total basepairs processed:   316,902,295 bp
# empty1_cutadapt.txt-  Read 1:   151,356,320 bp
# empty1_cutadapt.txt-  Read 2:   165,545,975 bp
# --
# empty2_cutadapt.txt:=== Summary ===
# empty2_cutadapt.txt-
# empty2_cutadapt.txt-Total read pairs processed:          8,282,694
# empty2_cutadapt.txt-  Read 1 with adapter:                 893,161 (10.8%)
# empty2_cutadapt.txt-  Read 2 with adapter:               2,524,571 (30.5%)
# empty2_cutadapt.txt-Pairs that were too short:              49,971 (0.6%)
# empty2_cutadapt.txt-Pairs written (passing filters):     8,232,723 (99.4%)
# empty2_cutadapt.txt-
# empty2_cutadapt.txt-Total basepairs processed:   554,940,498 bp
# empty2_cutadapt.txt-  Read 1:   265,046,208 bp
# empty2_cutadapt.txt-  Read 2:   289,894,290 bp
# --
# HA1_cutadapt.txt:=== Summary ===
# HA1_cutadapt.txt-
# HA1_cutadapt.txt-Total read pairs processed:         14,786,048
# HA1_cutadapt.txt-  Read 1 with adapter:               1,694,252 (11.5%)
# HA1_cutadapt.txt-  Read 2 with adapter:               4,594,267 (31.1%)
# HA1_cutadapt.txt-Pairs that were too short:              14,666 (0.1%)
# HA1_cutadapt.txt-Pairs written (passing filters):    14,771,382 (99.9%)
# HA1_cutadapt.txt-
# HA1_cutadapt.txt-Total basepairs processed:   990,665,216 bp
# HA1_cutadapt.txt-  Read 1:   473,153,536 bp
# HA1_cutadapt.txt-  Read 2:   517,511,680 bp
# --
# HA2_cutadapt.txt:=== Summary ===
# HA2_cutadapt.txt-
# HA2_cutadapt.txt-Total read pairs processed:         10,722,549
# HA2_cutadapt.txt-  Read 1 with adapter:               1,231,974 (11.5%)
# HA2_cutadapt.txt-  Read 2 with adapter:               3,348,502 (31.2%)
# HA2_cutadapt.txt-Pairs that were too short:              20,563 (0.2%)
# HA2_cutadapt.txt-Pairs written (passing filters):    10,701,986 (99.8%)
# HA2_cutadapt.txt-
# HA2_cutadapt.txt-Total basepairs processed:   718,410,783 bp
# HA2_cutadapt.txt-  Read 1:   343,121,568 bp
# HA2_cutadapt.txt-  Read 2:   375,289,215 bp
# --
# SKOempty1_cutadapt.txt:=== Summary ===
# SKOempty1_cutadapt.txt-
# SKOempty1_cutadapt.txt-Total read pairs processed:          7,323,898
# SKOempty1_cutadapt.txt-  Read 1 with adapter:                 782,633 (10.7%)
# SKOempty1_cutadapt.txt-  Read 2 with adapter:               2,142,205 (29.2%)
# SKOempty1_cutadapt.txt-Pairs that were too short:               9,280 (0.1%)
# SKOempty1_cutadapt.txt-Pairs written (passing filters):     7,314,618 (99.9%)
# SKOempty1_cutadapt.txt-
# SKOempty1_cutadapt.txt-Total basepairs processed:   490,701,166 bp
# SKOempty1_cutadapt.txt-  Read 1:   234,364,736 bp
# SKOempty1_cutadapt.txt-  Read 2:   256,336,430 bp
# --
# SKOempty2_cutadapt.txt:=== Summary ===
# SKOempty2_cutadapt.txt-
# SKOempty2_cutadapt.txt-Total read pairs processed:          7,979,937
# SKOempty2_cutadapt.txt-  Read 1 with adapter:                 890,097 (11.2%)
# SKOempty2_cutadapt.txt-  Read 2 with adapter:               2,381,994 (29.8%)
# SKOempty2_cutadapt.txt-Pairs that were too short:              60,200 (0.8%)
# SKOempty2_cutadapt.txt-Pairs written (passing filters):     7,919,737 (99.2%)
# SKOempty2_cutadapt.txt-
# SKOempty2_cutadapt.txt-Total basepairs processed:   534,655,779 bp
# SKOempty2_cutadapt.txt-  Read 1:   255,357,984 bp
# SKOempty2_cutadapt.txt-  Read 2:   279,297,795 bp
# --
# SKOHA1_cutadapt.txt:=== Summary ===
# SKOHA1_cutadapt.txt-
# SKOHA1_cutadapt.txt-Total read pairs processed:         15,609,117
# SKOHA1_cutadapt.txt-  Read 1 with adapter:               1,767,948 (11.3%)
# SKOHA1_cutadapt.txt-  Read 2 with adapter:               4,794,874 (30.7%)
# SKOHA1_cutadapt.txt-Pairs that were too short:              42,606 (0.3%)
# SKOHA1_cutadapt.txt-Pairs written (passing filters):    15,566,511 (99.7%)
# SKOHA1_cutadapt.txt-
# SKOHA1_cutadapt.txt-Total basepairs processed: 1,045,810,839 bp
# SKOHA1_cutadapt.txt-  Read 1:   499,491,744 bp
# SKOHA1_cutadapt.txt-  Read 2:   546,319,095 bp
# --
# SKOHA2_cutadapt.txt:=== Summary ===
# SKOHA2_cutadapt.txt-
# SKOHA2_cutadapt.txt-Total read pairs processed:          9,550,120
# SKOHA2_cutadapt.txt-  Read 1 with adapter:               1,040,611 (10.9%)
# SKOHA2_cutadapt.txt-  Read 2 with adapter:               2,916,097 (30.5%)
# SKOHA2_cutadapt.txt-Pairs that were too short:               9,190 (0.1%)
# SKOHA2_cutadapt.txt-Pairs written (passing filters):     9,540,930 (99.9%)
# SKOHA2_cutadapt.txt-
# SKOHA2_cutadapt.txt-Total basepairs processed:   639,858,040 bp
# SKOHA2_cutadapt.txt-  Read 1:   305,603,840 bp
# SKOHA2_cutadapt.txt-  Read 2:   334,254,200 bp
# --
# WTempty1_cutadapt.txt:=== Summary ===
# WTempty1_cutadapt.txt-
# WTempty1_cutadapt.txt-Total read pairs processed:          6,866,855
# WTempty1_cutadapt.txt-  Read 1 with adapter:                 741,174 (10.8%)
# WTempty1_cutadapt.txt-  Read 2 with adapter:               2,033,871 (29.6%)
# WTempty1_cutadapt.txt-Pairs that were too short:              33,611 (0.5%)
# WTempty1_cutadapt.txt-Pairs written (passing filters):     6,833,244 (99.5%)
# WTempty1_cutadapt.txt-
# WTempty1_cutadapt.txt-Total basepairs processed:   460,079,285 bp
# WTempty1_cutadapt.txt-  Read 1:   219,739,360 bp
# WTempty1_cutadapt.txt-  Read 2:   240,339,925 bp
# --
# WTempty2_cutadapt.txt:=== Summary ===
# WTempty2_cutadapt.txt-
# WTempty2_cutadapt.txt-Total read pairs processed:          6,616,513
# WTempty2_cutadapt.txt-  Read 1 with adapter:                 711,464 (10.8%)
# WTempty2_cutadapt.txt-  Read 2 with adapter:               1,931,020 (29.2%)
# WTempty2_cutadapt.txt-Pairs that were too short:              12,191 (0.2%)
# WTempty2_cutadapt.txt-Pairs written (passing filters):     6,604,322 (99.8%)
# WTempty2_cutadapt.txt-
# WTempty2_cutadapt.txt-Total basepairs processed:   443,306,371 bp
# WTempty2_cutadapt.txt-  Read 1:   211,728,416 bp
# WTempty2_cutadapt.txt-  Read 2:   231,577,955 bp
# --
# WTHA1_cutadapt.txt:=== Summary ===
# WTHA1_cutadapt.txt-
# WTHA1_cutadapt.txt-Total read pairs processed:         16,267,305
# WTHA1_cutadapt.txt-  Read 1 with adapter:               1,831,432 (11.3%)
# WTHA1_cutadapt.txt-  Read 2 with adapter:               5,119,310 (31.5%)
# WTHA1_cutadapt.txt-Pairs that were too short:              57,461 (0.4%)
# WTHA1_cutadapt.txt-Pairs written (passing filters):    16,209,844 (99.6%)
# WTHA1_cutadapt.txt-
# WTHA1_cutadapt.txt-Total basepairs processed: 1,089,909,435 bp
# WTHA1_cutadapt.txt-  Read 1:   520,553,760 bp
# WTHA1_cutadapt.txt-  Read 2:   569,355,675 bp
# --
# WTHA2_cutadapt.txt:=== Summary ===
# WTHA2_cutadapt.txt-
# WTHA2_cutadapt.txt-Total read pairs processed:         11,772,624
# WTHA2_cutadapt.txt-  Read 1 with adapter:               1,338,898 (11.4%)
# WTHA2_cutadapt.txt-  Read 2 with adapter:               3,688,399 (31.3%)
# WTHA2_cutadapt.txt-Pairs that were too short:              35,555 (0.3%)
# WTHA2_cutadapt.txt-Pairs written (passing filters):    11,737,069 (99.7%)
# WTHA2_cutadapt.txt-
# WTHA2_cutadapt.txt-Total basepairs processed:   788,765,808 bp
# WTHA2_cutadapt.txt-  Read 1:   376,723,968 bp
# WTHA2_cutadapt.txt-  Read 2:   412,041,840 bp
```



### quality check (post umi)

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/fastq_umi

mkdir ../fastqc_umi

for fq in *.fastq.gz
do
  bname=${fq%.fastq.gz}
  sbatch -J $bname -o ../fastqc_umi/$bname.log --mem 4G -t 1-0 --wrap "fastqc --noextract --nogroup -q -o ../fastqc_umi $fq"
done
```



### alignment hisat2

#### align, sort, index

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/fastq_umi

mkdir ../hisat2

idx=/projects/qbio/bifo/users/sergio/reference/gencode/release35_GRCh38.p13/fasta/GRCh38.p13.genome

for fq1 in *_R1.fastq.gz
do
  fq2=${fq1/_R1./_R2.}
  bname=${fq1%_R1.fastq.gz}
  #echo $fq1, $fq2, $bname
  sbatch -J $bname -o ../hisat2/$bname.log --mem 64G -t 1-0 --wrap "hisat2 -p 20 -x $idx -1 $fq1 -2 $fq2 | \
  samtools view -@ 20 -bS - | \
  samtools sort -@ 20 -T /projects/qbio/bifo/users/sergio/tmp/$bname -o ../hisat2/$bname.bam - && \
  samtools index ../hisat2/$bname.bam"
done

cd ../hisat2

tail *.log

grep "overall alignment rate" *.log
# empty1.log:57.29% overall alignment rate
# empty2.log:62.15% overall alignment rate
# HA1.log:78.72% overall alignment rate
# HA2.log:78.85% overall alignment rate
# SKOempty1.log:78.55% overall alignment rate
# SKOempty2.log:77.68% overall alignment rate
# SKOHA1.log:78.55% overall alignment rate
# SKOHA2.log:79.15% overall alignment rate
# WTempty1.log:80.04% overall alignment rate
# WTempty2.log:79.83% overall alignment rate
# WTHA1.log:79.25% overall alignment rate
# WTHA2.log:79.59% overall alignment rate
```


#### deduplication

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/hisat2

for bam in *.bam
do
  bname=${bam%.bam}
  sbatch -J $bname -o $bname.dedup.log --mem 16G -t 1-0 --wrap "umi_tools dedup --output-stats=$bname.dedup --paired -I $bam -S $bname.dedup.bam && \
  samtools index $bname.dedup.bam"
done

tail *.dedup.log
```

And samtools flagstat:

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/hisat2

for bam in *.bam
do
  echo $bam && samtools flagstat $bam && echo "--------------"
done
# empty1.bam
# 12005001 + 0 in total (QC-passed reads + QC-failed reads)
# 2627633 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 7999587 + 0 mapped (66.64% : N/A)
# 9377368 + 0 paired in sequencing
# 4688684 + 0 read1
# 4688684 + 0 read2
# 4755822 + 0 properly paired (50.72% : N/A)
# 4885852 + 0 with itself and mate mapped
# 486102 + 0 singletons (5.18% : N/A)
# 92864 + 0 with mate mapped to a different chr
# 47374 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
# empty1.dedup.bam
# 4385146 + 0 in total (QC-passed reads + QC-failed reads)
# 1313293 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 4385146 + 0 mapped (100.00% : N/A)
# 3071853 + 0 paired in sequencing
# 1528746 + 0 read1
# 1543107 + 0 read2
# 2967595 + 0 properly paired (96.61% : N/A)
# 3071853 + 0 with itself and mate mapped
# 0 + 0 singletons (0.00% : N/A)
# 77896 + 0 with mate mapped to a different chr
# 39410 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
# empty2.bam
# 20228365 + 0 in total (QC-passed reads + QC-failed reads)
# 3762919 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 13995651 + 0 mapped (69.19% : N/A)
# 16465446 + 0 paired in sequencing
# 8232723 + 0 read1
# 8232723 + 0 read2
# 9021942 + 0 properly paired (54.79% : N/A)
# 9265194 + 0 with itself and mate mapped
# 967538 + 0 singletons (5.88% : N/A)
# 173384 + 0 with mate mapped to a different chr
# 90478 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
# empty2.dedup.bam
# 6863330 + 0 in total (QC-passed reads + QC-failed reads)
# 1641003 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 6863330 + 0 mapped (100.00% : N/A)
# 5222327 + 0 paired in sequencing
# 2598283 + 0 read1
# 2624044 + 0 read2
# 5040720 + 0 properly paired (96.52% : N/A)
# 5222327 + 0 with itself and mate mapped
# 0 + 0 singletons (0.00% : N/A)
# 136034 + 0 with mate mapped to a different chr
# 70602 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
# HA1.bam
# 37273419 + 0 in total (QC-passed reads + QC-failed reads)
# 7730655 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 30985287 + 0 mapped (83.13% : N/A)
# 29542764 + 0 paired in sequencing
# 14771382 + 0 read1
# 14771382 + 0 read2
# 20158770 + 0 properly paired (68.24% : N/A)
# 21093932 + 0 with itself and mate mapped
# 2160700 + 0 singletons (7.31% : N/A)
# 729592 + 0 with mate mapped to a different chr
# 377219 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
# HA1.dedup.bam
# 25689020 + 0 in total (QC-passed reads + QC-failed reads)
# 5136942 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 25689020 + 0 mapped (100.00% : N/A)
# 20552078 + 0 paired in sequencing
# 10229159 + 0 read1
# 10322919 + 0 read2
# 19543717 + 0 properly paired (95.09% : N/A)
# 20552078 + 0 with itself and mate mapped
# 0 + 0 singletons (0.00% : N/A)
# 798609 + 0 with mate mapped to a different chr
# 413732 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
# HA2.bam
# 26923041 + 0 in total (QC-passed reads + QC-failed reads)
# 5519069 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 22396466 + 0 mapped (83.19% : N/A)
# 21403972 + 0 paired in sequencing
# 10701986 + 0 read1
# 10701986 + 0 read2
# 14709370 + 0 properly paired (68.72% : N/A)
# 15362894 + 0 with itself and mate mapped
# 1514503 + 0 singletons (7.08% : N/A)
# 501436 + 0 with mate mapped to a different chr
# 257485 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
# HA2.dedup.bam
# 18655354 + 0 in total (QC-passed reads + QC-failed reads)
# 3711005 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 18655354 + 0 mapped (100.00% : N/A)
# 14944349 + 0 paired in sequencing
# 7439348 + 0 read1
# 7505001 + 0 read2
# 14240193 + 0 properly paired (95.29% : N/A)
# 14944349 + 0 with itself and mate mapped
# 0 + 0 singletons (0.00% : N/A)
# 549298 + 0 with mate mapped to a different chr
# 282848 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
# SKOempty1.bam
# 19207741 + 0 in total (QC-passed reads + QC-failed reads)
# 4578505 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 16069962 + 0 mapped (83.66% : N/A)
# 14629236 + 0 paired in sequencing
# 7314618 + 0 read1
# 7314618 + 0 read2
# 10188360 + 0 properly paired (69.64% : N/A)
# 10499670 + 0 with itself and mate mapped
# 991787 + 0 singletons (6.78% : N/A)
# 227418 + 0 with mate mapped to a different chr
# 126266 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
# SKOempty1.dedup.bam
# 13656985 + 0 in total (QC-passed reads + QC-failed reads)
# 3281409 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 13656985 + 0 mapped (100.00% : N/A)
# 10375576 + 0 paired in sequencing
# 5175408 + 0 read1
# 5200168 + 0 read2
# 10043202 + 0 properly paired (96.80% : N/A)
# 10375576 + 0 with itself and mate mapped
# 0 + 0 singletons (0.00% : N/A)
# 246729 + 0 with mate mapped to a different chr
# 136426 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
# SKOempty2.bam
# 21017077 + 0 in total (QC-passed reads + QC-failed reads)
# 5177603 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 17481043 + 0 mapped (83.18% : N/A)
# 15839474 + 0 paired in sequencing
# 7919737 + 0 read1
# 7919737 + 0 read2
# 10850384 + 0 properly paired (68.50% : N/A)
# 11208138 + 0 with itself and mate mapped
# 1095302 + 0 singletons (6.92% : N/A)
# 271276 + 0 with mate mapped to a different chr
# 150406 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
# SKOempty2.dedup.bam
# 14771485 + 0 in total (QC-passed reads + QC-failed reads)
# 3659241 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 14771485 + 0 mapped (100.00% : N/A)
# 11112244 + 0 paired in sequencing
# 5541629 + 0 read1
# 5570615 + 0 read2
# 10728992 + 0 properly paired (96.55% : N/A)
# 11112244 + 0 with itself and mate mapped
# 0 + 0 singletons (0.00% : N/A)
# 294369 + 0 with mate mapped to a different chr
# 162542 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
# SKOHA1.bam
# 40179349 + 0 in total (QC-passed reads + QC-failed reads)
# 9046327 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 33502549 + 0 mapped (83.38% : N/A)
# 31133022 + 0 paired in sequencing
# 15566511 + 0 read1
# 15566511 + 0 read2
# 20875978 + 0 properly paired (67.05% : N/A)
# 22117082 + 0 with itself and mate mapped
# 2339140 + 0 singletons (7.51% : N/A)
# 1007562 + 0 with mate mapped to a different chr
# 569902 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
# SKOHA1.dedup.bam
# 28173806 + 0 in total (QC-passed reads + QC-failed reads)
# 6081125 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 28173806 + 0 mapped (100.00% : N/A)
# 22092681 + 0 paired in sequencing
# 10990995 + 0 read1
# 11101686 + 0 read2
# 20747877 + 0 properly paired (93.91% : N/A)
# 22092681 + 0 with itself and mate mapped
# 0 + 0 singletons (0.00% : N/A)
# 1101946 + 0 with mate mapped to a different chr
# 621328 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
# SKOHA2.bam
# 24420665 + 0 in total (QC-passed reads + QC-failed reads)
# 5338805 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 20441554 + 0 mapped (83.71% : N/A)
# 19081860 + 0 paired in sequencing
# 9540930 + 0 read1
# 9540930 + 0 read2
# 13055582 + 0 properly paired (68.42% : N/A)
# 13708482 + 0 with itself and mate mapped
# 1394267 + 0 singletons (7.31% : N/A)
# 512858 + 0 with mate mapped to a different chr
# 284536 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
# SKOHA2.dedup.bam
# 17374773 + 0 in total (QC-passed reads + QC-failed reads)
# 3677786 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 17374773 + 0 mapped (100.00% : N/A)
# 13696987 + 0 paired in sequencing
# 6819105 + 0 read1
# 6877882 + 0 read2
# 12988128 + 0 properly paired (94.82% : N/A)
# 13696987 + 0 with itself and mate mapped
# 0 + 0 singletons (0.00% : N/A)
# 563607 + 0 with mate mapped to a different chr
# 311913 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
# WTempty1.bam
# 17092964 + 0 in total (QC-passed reads + QC-failed reads)
# 3426476 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 14364701 + 0 mapped (84.04% : N/A)
# 13666488 + 0 paired in sequencing
# 6833244 + 0 read1
# 6833244 + 0 read2
# 9648784 + 0 properly paired (70.60% : N/A)
# 10016738 + 0 with itself and mate mapped
# 921487 + 0 singletons (6.74% : N/A)
# 279498 + 0 with mate mapped to a different chr
# 161425 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
# WTempty1.dedup.bam
# 12367009 + 0 in total (QC-passed reads + QC-failed reads)
# 2399453 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 12367009 + 0 mapped (100.00% : N/A)
# 9967556 + 0 paired in sequencing
# 4969526 + 0 read1
# 4998030 + 0 read2
# 9573432 + 0 properly paired (96.05% : N/A)
# 9967556 + 0 with itself and mate mapped
# 0 + 0 singletons (0.00% : N/A)
# 303272 + 0 with mate mapped to a different chr
# 174523 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
# WTempty2.bam
# 16686090 + 0 in total (QC-passed reads + QC-failed reads)
# 3477446 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 14021304 + 0 mapped (84.03% : N/A)
# 13208644 + 0 paired in sequencing
# 6604322 + 0 read1
# 6604322 + 0 read2
# 9265198 + 0 properly paired (70.14% : N/A)
# 9632780 + 0 with itself and mate mapped
# 911078 + 0 singletons (6.90% : N/A)
# 285642 + 0 with mate mapped to a different chr
# 164706 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
# WTempty2.dedup.bam
# 12000345 + 0 in total (QC-passed reads + QC-failed reads)
# 2408849 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 12000345 + 0 mapped (100.00% : N/A)
# 9591496 + 0 paired in sequencing
# 4781251 + 0 read1
# 4810245 + 0 read2
# 9197144 + 0 properly paired (95.89% : N/A)
# 9591496 + 0 with itself and mate mapped
# 0 + 0 singletons (0.00% : N/A)
# 310025 + 0 with mate mapped to a different chr
# 178071 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
# WTHA1.bam
# 41146802 + 0 in total (QC-passed reads + QC-failed reads)
# 8727114 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 34418369 + 0 mapped (83.65% : N/A)
# 32419688 + 0 paired in sequencing
# 16209844 + 0 read1
# 16209844 + 0 read2
# 21780678 + 0 properly paired (67.18% : N/A)
# 23234958 + 0 with itself and mate mapped
# 2456297 + 0 singletons (7.58% : N/A)
# 1173966 + 0 with mate mapped to a different chr
# 659815 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
# WTHA1.dedup.bam
# 29082616 + 0 in total (QC-passed reads + QC-failed reads)
# 5779022 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 29082616 + 0 mapped (100.00% : N/A)
# 23303594 + 0 paired in sequencing
# 11585128 + 0 read1
# 11718466 + 0 read2
# 21719632 + 0 properly paired (93.20% : N/A)
# 23303594 + 0 with itself and mate mapped
# 0 + 0 singletons (0.00% : N/A)
# 1291663 + 0 with mate mapped to a different chr
# 724593 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
# WTHA2.bam
# 29843947 + 0 in total (QC-passed reads + QC-failed reads)
# 6369809 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 25051850 + 0 mapped (83.94% : N/A)
# 23474138 + 0 paired in sequencing
# 11737069 + 0 read1
# 11737069 + 0 read2
# 15807938 + 0 properly paired (67.34% : N/A)
# 16905762 + 0 with itself and mate mapped
# 1776279 + 0 singletons (7.57% : N/A)
# 885326 + 0 with mate mapped to a different chr
# 501161 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
# WTHA2.dedup.bam
# 21161714 + 0 in total (QC-passed reads + QC-failed reads)
# 4211137 + 0 secondary
# 0 + 0 supplementary
# 0 + 0 duplicates
# 21161714 + 0 mapped (100.00% : N/A)
# 16950577 + 0 paired in sequencing
# 8425527 + 0 read1
# 8525050 + 0 read2
# 15756347 + 0 properly paired (92.95% : N/A)
# 16950577 + 0 with itself and mate mapped
# 0 + 0 singletons (0.00% : N/A)
# 972793 + 0 with mate mapped to a different chr
# 549799 + 0 with mate mapped to a different chr (mapQ>=5)
# --------------
```


#### bigwig

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/hisat2

for bam in *.dedup.bam
do
  bname=${bam%.bam}
  sbatch -J $bname -o $bname.bigwig.log --mem 32G -t 1-0 --wrap "bamCoverage -b $bam -o ${bname}.bw -of bigwig --binSize 1 --normalizeUsing CPM"
done

tail *.bigwig.log
```


#### bedtools multicov

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/hisat2

mkdir ../multicov

bed=/projects/qbio/bifo/users/sergio/repository/20201122_hush/annotation/gencode.v35.annotation.gene_type.intronless.bed

for bam in *.dedup.bam
do
	bname=${bam%.bam}
	sbatch -J $bname -o ../multicov/$bname.log --mem 16G -t 1-0 --wrap "bedtools multicov -bams $bam -bed $bed -D | cut -f5,9 > ../multicov/$bname.txt"
done

cd ../multicov
tail *.log # looks fine

ls *.txt | xargs wc -l
   # 60656 empty1.dedup.txt
   # 60656 empty2.dedup.txt
   # 60656 HA1.dedup.txt
   # 60656 HA2.dedup.txt
   # 60656 SKOempty1.dedup.txt
   # 60656 SKOempty2.dedup.txt
   # 60656 SKOHA1.dedup.txt
   # 60656 SKOHA2.dedup.txt
   # 60656 WTempty1.dedup.txt
   # 60656 WTempty2.dedup.txt
   # 60656 WTHA1.dedup.txt
   # 60656 WTHA2.dedup.txt

grep "ENSG00000227400.2" *.txt
   # empty1.dedup.txt:ENSG00000227400.2	48
   # empty2.dedup.txt:ENSG00000227400.2	73
   # HA1.dedup.txt:ENSG00000227400.2	3893
   # HA2.dedup.txt:ENSG00000227400.2	2854
   # SKOempty1.dedup.txt:ENSG00000227400.2	111
   # SKOempty2.dedup.txt:ENSG00000227400.2	165
   # SKOHA1.dedup.txt:ENSG00000227400.2	3390
   # SKOHA2.dedup.txt:ENSG00000227400.2	1955
   # WTempty1.dedup.txt:ENSG00000227400.2	156
   # WTempty2.dedup.txt:ENSG00000227400.2	187
   # WTHA1.dedup.txt:ENSG00000227400.2	6123
   # WTHA2.dedup.txt:ENSG00000227400.2	4540
```



### Gene info + RIP-seq WT + RIP-seq SETDB1 KO 1 + RIP-seq SETDB1 KO 2

```r
#module load R-core/3.6.0-foss-2017a
#export R_LIBS_USER=/projects/qbio/bifo/R/3.6.0
#cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/table
#R

library(data.table)
library(GenomicFeatures)
library(edgeR)

# Enlarge the view width when printing tables
options(width = 250)

##############
# genes info #
##############
genes <- fread("/projects/qbio/bifo/users/sergio/repository/20201122_hush/annotation/gencode.v35.annotation.gene_type.intronless.bed")
setnames(genes, c("chr", "start", "end", "gene_name", "gene_id", "strand", "gene_type", "intronless"))

##############
# gene sizes #
##############
txdb <- makeTxDbFromGFF("/projects/qbio/bifo/users/sergio/reference/gencode/release35_GRCh38.p13/gtf/gencode.v35.annotation.gtf", format="gtf")
genes_length <- data.table(data.frame(genes(txdb)))[, c("gene_id", "width")]
genes_length[, length := width]
genes_length[, width := NULL]

genes <- merge(genes, genes_length, by = "gene_id")


##########
# ripseq #
##########
ripseq <- fread("tableCat.py -i /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/multicov/*.dedup.txt -r .dedup.txt")
setnames(ripseq, c("gene_id", "count", "file_id"))
ripseq_dcast <- dcast(ripseq, gene_id ~ file_id, value.var = "count")

# Define group
group <- factor(c('SETDB1KOHA', 'SETDB1KOHA', 'SETDB1KOHA', 'SETDB1KOHA', 'SETDB1KOempty', 'SETDB1KOempty', 'WTHA', 'WTHA', 'WTempty', 'WTempty', 'SETDB1KOempty', 'SETDB1KOempty'))

# Check that gene order is similar
sum(ripseq_dcast$gene_id == genes$gene_id)

# Define DGEList object
y <- DGEList(counts = ripseq_dcast[,c(-1)], group = group, genes = genes)

# Filtering
isexpr <- filterByExpr(y, min.count = 5)
table(isexpr)
#FALSE  TRUE
#21520 39136

y <- y[isexpr, , keep.lib.sizes=FALSE]
dim(y)
#39136    12

# Normalization
y <- calcNormFactors(y)
y$samples

# MDS
pdf("../figures/ripseq_mds.pdf")
p <- plotMDS(y)
dev.off()

# Design matrix
design <- model.matrix(~ 0 + as.vector(y$samples$group))
colnames(design) <- c("SETDB1KOempty", "SETDB1KOHA", "WTempty", "WTHA")

# Dispersion
y <- estimateDisp(y, design, robust=TRUE)
y$common.dispersion

# BCV
pdf("../figures/ripseq_bcv.pdf")
p <- plotBCV(y)
dev.off()

# Fit genewise glms
fit <- glmFit(y, design)

# Differential expression
my.contrasts <- makeContrasts(SETDB1KOHAvsSETDB1KOempty = SETDB1KOHA - SETDB1KOempty, WTHAvsWTempty = WTHA - WTempty, levels = design)

# Likelihood ratio tests
## SETDB1KOHAvsSETDB1KOempty
lrt_SETDB1KOHAvsSETDB1KOempty <- glmLRT(fit, contrast=my.contrasts[,"SETDB1KOHAvsSETDB1KOempty"])
table_SETDB1KOHAvsSETDB1KOempty <- data.table(data.frame(topTags(lrt_SETDB1KOHAvsSETDB1KOempty, n=Inf)))[, c("gene_id", "chr", "start", "end", "gene_name", "strand", "gene_type", "intronless", "length", "logFC", "FDR")]
## WTHAvsWTempty
lrt_WTHAvsWTempty <- glmLRT(fit, contrast=my.contrasts[,"WTHAvsWTempty"])
table_WTHAvsWTempty <- data.table(data.frame(topTags(lrt_WTHAvsWTempty, n=Inf)))[, c("gene_id", "chr", "start", "end", "gene_name", "strand", "gene_type", "intronless", "length", "logFC", "FDR")]

# Combine logFC and FDR and add RPKM
table_ripseq_tmp <- merge(table_SETDB1KOHAvsSETDB1KOempty, table_WTHAvsWTempty, by = c("gene_id", "chr", "start", "end", "gene_name", "strand", "gene_type", "intronless", "length"), suffixes = c("_SETDB1KOHAvsSETDB1KOempty", "_WTHAvsWTempty"))
table_ripseq <- cbind(table_ripseq_tmp, rpkm(y))
setnames(table_ripseq, c("gene_id", "chr", "start", "end", "gene_name", "strand", "gene_type", "intronless", "length", "logFC_SETDB1KOHAvsSETDB1KOempty", "FDR_SETDB1KOHAvsSETDB1KOempty", "logFC_WTHAvsWTempty", "FDR_WTHAvsWTempty", "RPKM_HA1", "RPKM_HA2", "RPKM_SKOHA1", "RPKM_SKOHA2", "RPKM_SKOempty1", "RPKM_SKOempty2", "RPKM_WTHA1", "RPKM_WTHA2", "RPKM_WTempty1", "RPKM_WTempty2", "RPKM_empty1", "RPKM_empty2"))
table_ripseq <- table_ripseq[, c("gene_id", "chr", "start", "end", "gene_name", "strand", "gene_type", "intronless", "length", "RPKM_HA1", "RPKM_HA2", "RPKM_SKOHA1", "RPKM_SKOHA2", "RPKM_empty1", "RPKM_empty2", "RPKM_SKOempty1", "RPKM_SKOempty2", "logFC_SETDB1KOHAvsSETDB1KOempty", "FDR_SETDB1KOHAvsSETDB1KOempty", "RPKM_WTHA1", "RPKM_WTHA2", "RPKM_WTempty1", "RPKM_WTempty2", "logFC_WTHAvsWTempty", "FDR_WTHAvsWTempty")]

# merge and write table
write.table(table_ripseq, "20210609_table.txt", sep="\t", quote = FALSE, row.names = FALSE, col.names = TRUE)
```



### peak calling

Using this suggestions from Devon Ryan [here](https://bioinformatics.stackexchange.com/questions/2378/rip-seq-analysis) on the hisat2 alignments


#### bedgraph, filter, merge regions and count

Create bedgraph files:

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/hisat2

mkdir ../custom

for bam in *.dedup.bam
do
  bname=${bam%.bam}
  sbatch -J $bname -o ../custom/$bname.log --mem 32G -t 1-0 --wrap "bamCoverage -b $bam -o ../custom/$bname.bedgraph -of bedgraph --binSize 1 -p 20 --normalizeUsing CPM"
done

cd ../custom
tail *.dedup.log
wc -l *.dedup.bedgraph
```

Filter bedgraph regions with enough CPM and width and export to bed:

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/custom

for bdg in *.dedup.bedgraph
do
  bname=${bdg%.bedgraph}
  nohup awk '{if($4 >= 0.25){print $0}}' $bdg | sort -k1,1 -k2,2n | bedtools merge -i - -d 500 | awk '{if(($3-$2)>=1000){print $0}}' > $bname.bed &
done

wc -l *.dedup.bed
  # 10085 empty1.dedup.bed
  # 19275 empty2.dedup.bed
  # 13776 HA1.dedup.bed
  # 15686 HA2.dedup.bed
  # 10940 SKOempty1.dedup.bed
  # 11831 SKOempty2.dedup.bed
  # 11012 SKOHA1.dedup.bed
  # 12365 SKOHA2.dedup.bed
  # 10220 WTempty1.dedup.bed
  #  9763 WTempty2.dedup.bed
  # 11718 WTHA1.dedup.bed
  # 12839 WTHA2.dedup.bed
```

Define counting regions:

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/custom

# HA and empty
bedtools merge -i <(bedtools sort -i <(cat HA1.dedup.bed HA2.dedup.bed empty1.dedup.bed empty2.dedup.bed)) > HA.empty.dedup.bed
wc -l HA.empty.dedup.bed # 28808

# SKOHA and SKOempty
bedtools merge -i <(bedtools sort -i <(cat SKOHA1.dedup.bed SKOHA2.dedup.bed SKOempty1.dedup.bed SKOempty2.dedup.bed)) > SKOHA.SKOempty.dedup.bed
wc -l SKOHA.SKOempty.dedup.bed # 19667

# WTHA and WTempty
bedtools merge -i <(bedtools sort -i <(cat WTHA1.dedup.bed WTHA2.dedup.bed WTempty1.dedup.bed WTempty2.dedup.bed)) > WTHA.WTempty.dedup.bed
wc -l WTHA.WTempty.dedup.bed # 21269
```

Count with bedtools multicov:

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/hisat2

# HA and empty
for bam in HA1.dedup.bam HA2.dedup.bam empty1.dedup.bam empty2.dedup.bam
do
	bname=${bam%.bam}
	sbatch -J $bname -o ../custom/$bname.multicov.log --mem 16G -t 1-0 --wrap "bedtools multicov -bams $bam -bed ../custom/HA.empty.dedup.bed -D > ../custom/$bname.txt"
done

# SKOHA and SKOempty
for bam in SKOHA1.dedup.bam SKOHA2.dedup.bam SKOempty1.dedup.bam SKOempty2.dedup.bam
do
	bname=${bam%.bam}
	sbatch -J $bname -o ../custom/$bname.multicov.log --mem 16G -t 1-0 --wrap "bedtools multicov -bams $bam -bed ../custom/SKOHA.SKOempty.dedup.bed -D > ../custom/$bname.txt"
done

# WTHA and WTempty
for bam in WTHA1.dedup.bam WTHA2.dedup.bam WTempty1.dedup.bam WTempty2.dedup.bam
do
	bname=${bam%.bam}
	sbatch -J $bname -o ../custom/$bname.multicov.log --mem 16G -t 1-0 --wrap "bedtools multicov -bams $bam -bed ../custom/WTHA.WTempty.dedup.bed -D > ../custom/$bname.txt"
done

cd ../custom/
ls -lh *.multicov.log # looks fine

ls *.txt | xargs wc -l
  # 28808 empty1.dedup.txt
  # 28808 empty2.dedup.txt
  # 28808 HA1.dedup.txt
  # 28808 HA2.dedup.txt
  # 19667 SKOempty1.dedup.txt
  # 19667 SKOempty2.dedup.txt
  # 19667 SKOHA1.dedup.txt
  # 19667 SKOHA2.dedup.txt
  # 21269 WTempty1.dedup.txt
  # 21269 WTempty2.dedup.txt
  # 21269 WTHA1.dedup.txt
  # 21269 WTHA2.dedup.txt

# checking TAF7 peak
grep -P "chr13\t52028188\t52033243" HA1.dedup.txt HA2.dedup.txt empty1.dedup.txt empty2.dedup.txt
# HA1.dedup.txt:chr13	52028188	52033243	3056
# HA2.dedup.txt:chr13	52028188	52033243	2209
# empty1.dedup.txt:chr13	52028188	52033243	110
# empty2.dedup.txt:chr13	52028188	52033243	354

grep -P "chr13\t52028331\t52031586" SKOHA1.dedup.txt SKOHA2.dedup.txt SKOempty1.dedup.txt SKOempty2.dedup.txt
# SKOHA1.dedup.txt:chr13	52028331	52031586	2058
# SKOHA2.dedup.txt:chr13	52028331	52031586	1393
# SKOempty1.dedup.txt:chr13	52028331	52031586	382
# SKOempty2.dedup.txt:chr13	52028331	52031586	326

grep -P "chr13\t52028331\t52031880" WTHA1.dedup.txt WTHA2.dedup.txt WTempty1.dedup.txt WTempty2.dedup.txt
# WTHA1.dedup.txt:chr13	52028331	52031880	2371
# WTHA2.dedup.txt:chr13	52028331	52031880	1765
# WTempty1.dedup.txt:chr13	52028331	52031880	221
# WTempty2.dedup.txt:chr13	52028331	52031880	251
```


#### edgeR

```r
#module load R-core/3.6.0-foss-2017a
#export R_LIBS_USER=/projects/qbio/bifo/R/3.6.0
#cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/custom
#R

library(data.table)
library(edgeR)

# Enlarge the view width when printing tables
options(width = 250)


#################
# HA and empty #
################
# counts
ha_empty <- fread("tableCat.py -i HA1.dedup.txt HA2.dedup.txt empty1.dedup.txt empty2.dedup.txt -r .dedup.txt")
setnames(ha_empty, c("chr", "start", "end", "count", "file_id"))
ha_empty_dcast <- dcast(ha_empty, chr+start+end ~ file_id, value.var = "count")
counts <- ha_empty_dcast[,4:7]

# peaks
peaks <- ha_empty_dcast[,1:3]
peaks[, peak := paste(chr, start, end, sep = "_")]
peaks[, length := end - start]
setcolorder(peaks, c("peak", "chr", "start", "end", "length"))

# group
group <- factor(c('HA', 'HA', 'empty', 'empty'))

# DGEList object
y <- DGEList(counts = counts, group = group, genes = peaks)

# Normalization
y <- calcNormFactors(y)
y$samples

# Design matrix
design <- model.matrix(~ 0 + as.vector(y$samples$group))
colnames(design) <- c("empty", "HA")

# Dispersion
y <- estimateDisp(y, design, robust=TRUE)

# Fit glm
fit <- glmFit(y, design)

# Differential expression
my.contrasts <- makeContrasts(HAvsempty = HA - empty, levels = design)

# Likelihood ratio tests
lrt <- glmLRT(fit, contrast=my.contrasts[,"HAvsempty"])
data.table(data.frame(topTags(lrt, n=Inf)))

# Test with slides 11_06_2021 peaks checks
data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr13_52028188_52033243"] # UTP14C tp
#                      peak   chr    start      end length    logFC   logCPM       LR       PValue          FDR
#1: chr13_52028188_52033243 chr13 52028188 52033243   5055 1.674688 7.103289 69.58807 7.307748e-17 3.841635e-16

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr5_141318350_141320811"] # TAF7 tp
#                       peak  chr     start       end length    logFC   logCPM       LR       PValue          FDR
#1: chr5_141318350_141320811 chr5 141318350 141320811   2461 1.090032 7.664124 51.09455 8.802245e-13 3.370216e-12

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr13_52460896_52462565"] # CKAP2 fp
#                      peak   chr    start      end length     logFC   logCPM       LR    PValue       FDR
#1: chr13_52460896_52462565 chr13 52460896 52462565   1669 0.2545056 6.207143 2.336814 0.1263477 0.1412921

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr13_52641819_52643593"] # HNRNPA1L12 fp
#                      peak   chr    start      end length      logFC   logCPM        LR    PValue       FDR
#1: chr13_52641819_52643593 chr13 52641819 52643593   1774 -0.1086644 6.335638 0.2181497 0.6404534 0.6581359

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr17_5179807_5187855"] # ZNF594 tp
#                    peak   chr   start     end length    logFC   logCPM       LR       PValue          FDR
#1: chr17_5179807_5187855 chr17 5179807 5187855   8048 3.102062 7.596209 239.7902 4.369793e-54 1.731568e-52

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr3_121690669_121692581"] # GOLGB1 tp 1
#                       peak  chr     start       end length    logFC   logCPM       LR       PValue          FDR
#1: chr3_121690669_121692581 chr3 121690669 121692581   1912 1.470403 6.876384 74.21699 6.998537e-18 3.950115e-17

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr3_121693629_121698931"] # GOLGB1 tp 2
#                       peak  chr     start       end length    logFC   logCPM       LR       PValue          FDR
#1: chr3_121693629_121698931 chr3 121693629 121698931   5302 1.752546 8.483675 81.11401 2.130637e-19 1.329421e-18

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr10_38105335_38111747"] # ZNF37A tp 1
#                      peak   chr    start      end length    logFC   logCPM      LR       PValue          FDR
#1: chr10_38105335_38111747 chr10 38105335 38111747   6412 3.531438 9.059464 381.096 7.183834e-85 1.488863e-82

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr10_38114768_38126001"] # ZNF37A tp 2
#                      peak   chr    start      end length    logFC   logCPM       LR       PValue          FDR
#1: chr10_38114768_38126001 chr10 38114768 38126001  11233 3.285472 8.293615 375.4351 1.226922e-83 2.356344e-81

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr3_121015207_121024141"] # STXBP5L tp 1
#                       peak  chr     start       end length    logFC   logCPM       LR       PValue          FDR
#1: chr3_121015207_121024141 chr3 121015207 121024141   8934 4.398183 6.743457 383.5787 2.069287e-85 4.351242e-83

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr3_121049572_121053846"] # STXBP5L tp 2
#                       peak  chr     start       end length    logFC   logCPM       LR       PValue          FDR
#1: chr3_121049572_121053846 chr3 121049572 121053846   4274 2.498245 5.977401 94.47073 2.487436e-22 1.856907e-21

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr4_20737171_20744606"] # PACRGL tp
#                     peak  chr    start      end length    logFC   logCPM      LR       PValue          FDR
#1: chr4_20737171_20744606 chr4 20737171 20744606   7435 2.787033 7.667642 120.283 5.485121e-28 5.671765e-27

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr2_237101189_237117094"] # COPS8 tp
#                       peak  chr     start       end length    logFC   logCPM       LR       PValue          FDR
#1: chr2_237101189_237117094 chr2 237101189 237117094  15905 4.141184 8.272111 391.0065 4.997615e-87 1.180093e-84

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr2_122300301_122306962"] # XR_001739684.1 tp
#                       peak  chr     start       end length    logFC   logCPM       LR       PValue          FDR
#1: chr2_122300301_122306962 chr2 122300301 122306962   6661 3.117219 7.146515 293.1226 1.037959e-65 7.746512e-64

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr4_20556303_20558414"] # Tigger tp
#                     peak  chr    start      end length    logFC   logCPM       LR       PValue          FDR
#1: chr4_20556303_20558414 chr4 20556303 20558414   2111 2.395675 5.342287 98.11709 3.943609e-23 3.099795e-22

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr19_43964952_43975434"] # ZNF221 tp
#                      peak   chr    start      end length    logFC   logCPM       LR       PValue          FDR
#1: chr19_43964952_43975434 chr19 43964952 43975434  10482 3.002559 7.495804 241.2938 2.054048e-54 8.207075e-53

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr22_30966776_30979290"] # TUG1 tp
#                      peak   chr    start      end length    logFC   logCPM       LR       PValue          FDR
#1: chr22_30966776_30979290 chr22 30966776 30979290  12514 1.021645 8.241257 46.11321 1.116133e-11 3.865538e-11

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr3_179259066_179261445"] # KCNMB3 tp
#                       peak  chr     start       end length   logFC   logCPM       LR       PValue          FDR
#1: chr3_179259066_179261445 chr3 179259066 179261445   2379 2.01969 3.990341 48.68279 3.008953e-12 1.096545e-11

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr2_232547561_232550744"] # TIGD1 tp
#                       peak  chr     start       end length   logFC   logCPM       LR       PValue         FDR
#1: chr2_232547561_232550744 chr2 232547561 232550744   3183 1.92245 7.076425 126.0213 3.042009e-29 3.34354e-28

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr4_10442927_10446522"] # ZNF518B
#                     peak  chr    start      end length    logFC   logCPM       LR       PValue          FDR
#1: chr4_10442927_10446522 chr4 10442927 10446522   3595 1.895672 5.133739 67.25041 2.391208e-16 1.209586e-15

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr2_216454230_216460218"] # SMARCAL1 1
#                       peak  chr     start       end length    logFC   logCPM       LR       PValue          FDR
#1: chr2_216454230_216460218 chr2 216454230 216460218   5988 2.443812 6.019767 154.1505 2.147032e-35 3.289984e-34

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr2_216463548_216464656"] # SMARCAL1 2
#                       peak  chr     start       end length     logFC    logCPM       LR     PValue        FDR
#1: chr2_216463548_216464656 chr2 216463548 216464656   1108 -2.009668 0.7913011 4.958151 0.02596796 0.03150761

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr2_216498667_216501545"] # RPL37A fp
#                       peak  chr     start       end length     logFC   logCPM       LR       PValue          FDR
#1: chr2_216498667_216501545 chr2 216498667 216501545   2878 -1.582188 5.970122 59.78001 1.060754e-14 4.730767e-14

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr2_71422348_71424716"] # ZNF638 fp 1
#                     peak  chr    start      end length    logFC   logCPM        LR    PValue       FDR
#1: chr2_71422348_71424716 chr2 71422348 71424716   2368 0.172991 6.998801 0.9155853 0.3386368 0.3602322

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr2_71426447_71428673"] # ZNF638 fp 2
#                     peak  chr    start      end length     logFC   logCPM       LR      PValue         FDR
#1: chr2_71426447_71428673 chr2 71426447 71428673   2226 0.4765589 6.642333 7.446346 0.006356536 0.008306981

data.table(data.frame(topTags(lrt, n=Inf)))[peak=="chr3_22381719_22382837"] # UNKNOWN fp
#                     peak  chr    start      end length      logFC   logCPM        LR    PValue       FDR
#1: chr3_22381719_22382837 chr3 22381719 22382837   1118 -0.1327352 6.747148 0.5871064 0.4435403 0.4650594


# Bed file
table <- data.table(data.frame(topTags(lrt, n=Inf)))[logFC > 1 & FDR < 1e-3][,c("chr", "start", "end", "logFC", "PValue", "FDR")]
nrow(table) # 11300
write.table(table, "HA.v2.bed", sep="\t", quote = FALSE, row.names = FALSE, col.names = FALSE)


######################
# SKOHA and SKOempty #
######################
# counts
ha_empty <- fread("tableCat.py -i SKOHA1.dedup.txt SKOHA2.dedup.txt SKOempty1.dedup.txt SKOempty2.dedup.txt -r .dedup.txt")
setnames(ha_empty, c("chr", "start", "end", "count", "file_id"))
ha_empty_dcast <- dcast(ha_empty, chr+start+end ~ file_id, value.var = "count")
counts <- ha_empty_dcast[,4:7]

# peaks
peaks <- ha_empty_dcast[,1:3]
peaks[, peak := paste(chr, start, end, sep = "_")]
peaks[, length := end - start]
setcolorder(peaks, c("peak", "chr", "start", "end", "length"))

# group
group <- factor(c('HA', 'HA', 'empty', 'empty'))

# DGEList object
y <- DGEList(counts = counts, group = group, genes = peaks)

# Normalization
y <- calcNormFactors(y)
y$samples

# Design matrix
design <- model.matrix(~ 0 + as.vector(y$samples$group))
colnames(design) <- c("empty", "HA")

# Dispersion
y <- estimateDisp(y, design, robust=TRUE)

# Fit glm
fit <- glmFit(y, design)

# Differential expression
my.contrasts <- makeContrasts(HAvsempty = HA - empty, levels = design)

# Likelihood ratio tests
lrt <- glmLRT(fit, contrast=my.contrasts[,"HAvsempty"])
data.table(data.frame(topTags(lrt, n=Inf)))

# Bed file
table <- data.table(data.frame(topTags(lrt, n=Inf)))[logFC > 1 & FDR < 1e-3][,c("chr", "start", "end", "logFC", "PValue", "FDR")]
nrow(table) # 6661
write.table(table, "SKOHA.v2.bed", sep="\t", quote = FALSE, row.names = FALSE, col.names = FALSE)


####################
# WTHA and WTempty #
####################
# counts
ha_empty <- fread("tableCat.py -i WTHA1.dedup.txt WTHA2.dedup.txt WTempty1.dedup.txt WTempty2.dedup.txt -r .dedup.txt")
setnames(ha_empty, c("chr", "start", "end", "count", "file_id"))
ha_empty_dcast <- dcast(ha_empty, chr+start+end ~ file_id, value.var = "count")
counts <- ha_empty_dcast[,4:7]

# peaks
peaks <- ha_empty_dcast[,1:3]
peaks[, peak := paste(chr, start, end, sep = "_")]
peaks[, length := end - start]
setcolorder(peaks, c("peak", "chr", "start", "end", "length"))

# group
group <- factor(c('HA', 'HA', 'empty', 'empty'))

# DGEList object
y <- DGEList(counts = counts, group = group, genes = peaks)

# Normalization
y <- calcNormFactors(y)
y$samples

# Design matrix
design <- model.matrix(~ 0 + as.vector(y$samples$group))
colnames(design) <- c("empty", "HA")

# Dispersion
y <- estimateDisp(y, design, robust=TRUE)

# Fit glm
fit <- glmFit(y, design)

# Differential expression
my.contrasts <- makeContrasts(HAvsempty = HA - empty, levels = design)

# Likelihood ratio tests
lrt <- glmLRT(fit, contrast=my.contrasts[,"HAvsempty"])
data.table(data.frame(topTags(lrt, n=Inf)))

# Bed file
table <- data.table(data.frame(topTags(lrt, n=Inf)))[logFC > 1 & FDR < 1e-3][,c("chr", "start", "end", "logFC", "PValue", "FDR")]
nrow(table) # 8687
write.table(table, "WTHA.v2.bed", sep="\t", quote = FALSE, row.names = FALSE, col.names = FALSE)
```

Marta sounds ok with the latest `HA.v2.bed`, `SKOHA.v2.bed` and `WTHA.v2.bed`

Count how many peaks we have on each file and merge `HA.v2.bed` and `SKOHA.v2.bed`

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/custom
wc -l HA.v2.bed SKOHA.v2.bed WTHA.v2.bed
  # 11300 HA.v2.bed
  #  6661 SKOHA.v2.bed
  #  8687 WTHA.v2.bed

bedtools merge -i <(bedtools sort -i <(cat HA.v2.bed SKOHA.v2.bed)) > HA.SKOHA.v2.bed
wc -l HA.SKOHA.v2.bed # 11660
```


#### intervene

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/custom

intervene venn -i HA.SKOHA.v2.bed \
WTHA.v2.bed \
--names SETDB1-KO,WT \
-o intervene/ \
--save-overlaps \
--title "" \
--project HA.SKOHA.WTHA.intervene \
--colors green,orange \
--figsize 6 6 \
--fontsize 14

mv intervene/HA.SKOHA.WTHA.intervene_venn.pdf ../figures/ripseq_intervene_venn_HA.SKOHA.WTHA.pdf
```


#### macs2

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/hisat2

mkdir ../macs2

declare -A pairs=(\
[HA1.dedup.bam]=empty1.dedup.bam \
[HA2.dedup.bam]=empty2.dedup.bam \
[SKOHA1.dedup.bam]=SKOempty1.dedup.bam \
[SKOHA2.dedup.bam]=SKOempty2.dedup.bam \
[WTHA1.dedup.bam]=WTempty1.dedup.bam \
[WTHA2.dedup.bam]=WTempty2.dedup.bam)

for bam in ${!pairs[@]}
do
  bname=${bam%.bam}
  sbatch -J $bname -o ../macs2/$bname.log --mem 4G -t 1-0 --wrap "macs2 callpeak \
  -t $bam \
  -c ${pairs[$bam]} \
  -f BAMPE \
  --keep-dup all \
  --outdir ../macs2/ \
  -n $bname \
  --nomodel \
  -q 0.05 \
  --tempdir /projects/qbio/bifo/users/sergio/tmp/ \
  --broad"
done

cd ../macs2/

tail *.log

wc -l *.narrowPeak
#  1745 HA1.dedup_peaks.narrowPeak
#  1692 HA2.dedup_peaks.narrowPeak
```


#### piranha

Create bed files with raw reads:

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/hisat2

mkdir ../piranha

for bam in *.dedup.bam
do
  bname=${bam%.bam}
  sbatch -J $bname -o ../piranha/$bname.bamtobed.log --mem 32G -t 1-0 --wrap "bedtools bamtobed -i $bam > ../piranha/$bname.bed"
done

cd ../piranha

tail *.bamtobed.log

wc -l *.bed
  #  4385254 empty1.dedup.bed
  #  6863518 empty2.dedup.bed
  # 25689022 HA1.dedup.bed
  # 18655369 HA2.dedup.bed
```

Run piranha:

```bash
module load GSL/2.5-GCC-8.2.0-2.31.1

cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/piranha

for bed in *.dedup.bed
do
  bname=${bed%.bed}
  sbatch -J $bname -o $bname.piranha.log --mem 32G -t 1-0 --wrap "Piranha $bed -o $bname.peaks.bed -s -p 0.05"
done

tail *.piranha.log
wc -l *.peaks.bed
  # 1604251 empty1.dedup.peaks.bed
  # 2104670 empty2.dedup.peaks.bed
  # 4737090 HA1.dedup.peaks.bed
  # 3789322 HA2.dedup.peaks.bed
```


#### other peak calling methods

- pureclip
- pepr
- clipper



### RepeatMasker

Downloaded the annotation from the [UCSC table browser](https://genome.ucsc.edu/cgi-bin/hgTables) and copied it across. Another possibility would have been to have downloaded it directly from the Repeat Masker [website](https://www.repeatmasker.org/species/hg.html), however it would have been more convoluted to get it into the right format and UCSC uses anyway the Repeat Masker annotation


#### Explore txt file and convert into bed

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/repeatmasker

grep -v "^#" ucsc_hg38_repeats_repeatmasker.txt | cut -f5 | sort | uniq -c | wc -l # 15572 different names of repeats
grep -v "^#" ucsc_hg38_repeats_repeatmasker.txt | cut -f5 | sort | uniq -c | awk -v OFS="\t" '{print $1, $2}' > ucsc_hg38_repeats_repeatmasker.names.txt
head ucsc_hg38_repeats_repeatmasker.names.txt
# 1369	5S
# 64	6kbHsap
# 721	7SK
# 1707	7SLRNA
# 39	(AAAAAAC)n
# 13	(AAAAAAG)n
# 11	(AAAAAAT)n
# 25	(AAAAACA)n
# 8	(AAAAACC)n
# 276	(AAAAAC)n

grep -P "\tL1" ucsc_hg38_repeats_repeatmasker.names.txt > ucsc_hg38_repeats_repeatmasker.names.L1s.txt
head ucsc_hg38_repeats_repeatmasker.names.L1s.txt
# 1707	L1HS
# 756	L1M
# 10048	L1M1
# 9954	L1M2
# 461	L1M2a
# 110	L1M2a1
# 207	L1M2b
# 393	L1M2c
# 7205	L1M3
# 672	L1M3a

grep -v "^#" ucsc_hg38_repeats_repeatmasker.txt | cut -f6 | sort | uniq -c | grep -v "?" | wc -l # 16 different classes of repeats
grep -v "^#" ucsc_hg38_repeats_repeatmasker.txt | cut -f6 | sort | uniq -c | grep -v "?" | awk -v OFS="\t" '{print $1, $2}' > ucsc_hg38_repeats_repeatmasker.classes.txt
cat ucsc_hg38_repeats_repeatmasker.classes.txt
# 506784	DNA
# 1594040	LINE
# 104605	Low_complexity
# 760262	LTR
# 1804	RC
# 5882	Retroposon
# 721	RNA
# 1907	rRNA
# 8228	Satellite
# 1455	scRNA
# 714283	Simple_repeat
# 1884172	SINE
# 4606	snRNA
# 1707	srpRNA
# 2050	tRNA
# 5669	Unknown

grep -v "^#" ucsc_hg38_repeats_repeatmasker.txt | cut -f7 | sort | uniq -c | grep -v "?" | wc -l # 54 different families of repeats
grep -v "^#" ucsc_hg38_repeats_repeatmasker.txt | cut -f7 | sort | uniq -c | grep -v "?" | awk -v OFS="\t" '{print $1, $2}' > ucsc_hg38_repeats_repeatmasker.families.txt
cat ucsc_hg38_repeats_repeatmasker.families.txt
# 2571	5S-Deu-L2
# 150	acro
# 1262425	Alu
# 2965	centr
# 68975	CR1
# 3	Crypton
# 2365	DNA
# 544	Dong-R4
# 184079	ERV1
# 11706	ERVK
# 169746	ERVL
# 363032	ERVL-MaLR
# 17255	Gypsy
# 8856	hAT
# 4522	hAT-Ac
# 19905	hAT-Blackjack
# 268067	hAT-Charlie
# 219	hAT-Tag1
# 47245	hAT-Tip100
# 1804	Helitron
# 3	I-Jockey
# 4	Kolobok
# 1017024	L1
# 1	L1-Tx1
# 481336	L2
# 104605	Low_complexity
# 3317	LTR
# 63	Merlin
# 610603	MIR
# 2043	MULE-MuDR
# 1125	Penelope
# 32	PIF-Harbinger
# 2223	PiggyBac
# 721	RNA
# 1907	rRNA
# 9225	RTE-BovB
# 15807	RTE-X
# 4672	Satellite
# 1455	scRNA
# 714283	Simple_repeat
# 4606	snRNA
# 1707	srpRNA
# 5882	SVA
# 172	TcMar
# 16685	TcMar-Mariner
# 35	TcMar-Pogo
# 8	TcMar-Tc1
# 8362	TcMar-Tc2
# 121805	TcMar-Tigger
# 441	telo
# 4340	tRNA
# 639	tRNA-Deu
# 5649	tRNA-RTE
# 5669	Unknown

grep -v "^#" ucsc_hg38_repeats_repeatmasker.txt | awk -v OFS="\t" '{print $1, $2, $3, $5, $6, $4, $7}' > ucsc_hg38_repeats_repeatmasker.bed
wc -l ucsc_hg38_repeats_repeatmasker.bed # 5607738

grep -v "^#" ucsc_hg38_repeats_repeatmasker.txt | awk -v OFS="\t" '{print $1, $2, $3, $5}' | grep -P "\tL1" > ucsc_hg38_repeats_repeatmasker.names.L1s.bed
wc -l ucsc_hg38_repeats_repeatmasker.names.L1s.bed # 975647

grep -v "^#" ucsc_hg38_repeats_repeatmasker.txt | awk -v OFS="\t" '{print $1, $2, $3, $6}' | grep -v "?" > ucsc_hg38_repeats_repeatmasker.classes.bed
wc -l ucsc_hg38_repeats_repeatmasker.classes.bed # 5598175

grep -v "^#" ucsc_hg38_repeats_repeatmasker.txt | awk -v OFS="\t" '{print $1, $2, $3, $7}' | grep -v "?" > ucsc_hg38_repeats_repeatmasker.families.bed
wc -l ucsc_hg38_repeats_repeatmasker.families.bed # 5582883
```


#### overlap with ripseq data

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/custom

# SETDB1-KO
grep "^chr" HA.SKOHA.v2.bed | wc -l # 10960 SETDB1-KO peaks in core chr
bedtools intersect -a <(grep "^chr" HA.SKOHA.v2.bed) -b ../repeatmasker/ucsc_hg38_repeats_repeatmasker.bed -wa -u | wc -l # 10623 SETDB1-KO peaks in core chr overlap with repeats
bedtools intersect -a <(grep "^chr" HA.SKOHA.v2.bed) -b ../repeatmasker/ucsc_hg38_repeats_repeatmasker.bed -wo | bedtools groupby -i - -g 1,2,3 -c 7 -o count,collapse | head
bedtools intersect -a <(grep "^chr" HA.SKOHA.v2.bed) -b ../repeatmasker/ucsc_hg38_repeats_repeatmasker.bed -wo | cut -f7 | sort | uniq -c | awk -v OFS="\t" '{print $1, $2}' > HA.SKOHA.v2.ucsc_hg38_repeats_repeatmasker.names.txt
head HA.SKOHA.v2.ucsc_hg38_repeats_repeatmasker.names.txt
# 12	5S
# 3	7SK
# 19	7SLRNA
# 1	(AAAAAAC)n
# 1	(AAAAAC)n
# 1	(AAAACAA)n
# 3	(AAAACA)n
# 1	(AAAACC)n
# 16	(AAAAC)n
# 5	(AAAAG)n

grep -P "\tL1" HA.SKOHA.v2.ucsc_hg38_repeats_repeatmasker.names.txt > HA.SKOHA.v2.ucsc_hg38_repeats_repeatmasker.names.L1s.txt
head HA.SKOHA.v2.ucsc_hg38_repeats_repeatmasker.names.L1s.txt
# 159	L1HS
# 8	L1M
# 310	L1M1
# 249	L1M2
# 10	L1M2a
# 9	L1M2a1
# 8	L1M2b
# 28	L1M2c
# 110	L1M3
# 27	L1M3a

bedtools intersect -a <(grep "^chr" HA.SKOHA.v2.bed) -b ../repeatmasker/ucsc_hg38_repeats_repeatmasker.bed -wo | cut -f8 | sort | uniq -c | grep -v "?" | wc -l # 16 of 16 classes of repeats overlap
bedtools intersect -a <(grep "^chr" HA.SKOHA.v2.bed) -b ../repeatmasker/ucsc_hg38_repeats_repeatmasker.bed -wo | cut -f8 | sort | uniq -c | grep -v "?" | awk -v OFS="\t" '{print $1, $2}' > HA.SKOHA.v2.ucsc_hg38_repeats_repeatmasker.classes.txt
cat HA.SKOHA.v2.ucsc_hg38_repeats_repeatmasker.classes.txt
# 2495	DNA
# 22998	LINE
# 355	Low_complexity
# 3707	LTR
# 2	RC
# 68	Retroposon
# 3	RNA
# 14	rRNA
# 100	Satellite
# 9	scRNA
# 2516	Simple_repeat
# 9864	SINE
# 53	snRNA
# 19	srpRNA
# 43	tRNA
# 11	Unknown

bedtools intersect -a <(grep "^chr" HA.SKOHA.v2.bed) -b ../repeatmasker/ucsc_hg38_repeats_repeatmasker.bed -wo | cut -f10 | sort | uniq -c | grep -v "?" | wc -l # 43 of 54 families of repeats overlap
bedtools intersect -a <(grep "^chr" HA.SKOHA.v2.bed) -b ../repeatmasker/ucsc_hg38_repeats_repeatmasker.bed -wo | cut -f10 | sort | uniq -c | grep -v "?" | awk -v OFS="\t" '{print $1, $2}' > HA.SKOHA.v2.ucsc_hg38_repeats_repeatmasker.families.txt
cat HA.SKOHA.v2.ucsc_hg38_repeats_repeatmasker.families.txt
# 8	5S-Deu-L2
# 8639	Alu
# 57	centr
# 230	CR1
# 3	DNA
# 2	Dong-R4
# 801	ERV1
# 188	ERVK
# 700	ERVL
# 1949	ERVL-MaLR
# 26	Gypsy
# 22	hAT
# 20	hAT-Ac
# 41	hAT-Blackjack
# 867	hAT-Charlie
# 122	hAT-Tip100
# 2	Helitron
# 21235	L1
# 1461	L2
# 355	Low_complexity
# 9	LTR
# 1188	MIR
# 4	MULE-MuDR
# 5	Penelope
# 28	PiggyBac
# 3	RNA
# 14	rRNA
# 28	RTE-BovB
# 37	RTE-X
# 32	Satellite
# 9	scRNA
# 2516	Simple_repeat
# 53	snRNA
# 19	srpRNA
# 68	SVA
# 91	TcMar-Mariner
# 26	TcMar-Tc2
# 1265	TcMar-Tigger
# 11	telo
# 51	tRNA
# 3	tRNA-Deu
# 18	tRNA-RTE
# 11	Unknown


# WT
grep "^chr" WTHA.v2.bed | wc -l # 8224 WT peaks in core chr
bedtools intersect -a <(grep "^chr" WTHA.v2.bed | cut -f1-3) -b ../repeatmasker/ucsc_hg38_repeats_repeatmasker.bed -wa -u | wc -l # 8001 WT peaks in core chr overlap with repeats
bedtools intersect -a <(grep "^chr" WTHA.v2.bed | cut -f1-3) -b ../repeatmasker/ucsc_hg38_repeats_repeatmasker.bed -wo | bedtools groupby -i - -g 1,2,3 -c 7 -o count,collapse | head
bedtools intersect -a <(grep "^chr" WTHA.v2.bed | cut -f1-3) -b ../repeatmasker/ucsc_hg38_repeats_repeatmasker.bed -wo | cut -f7 | sort | uniq -c | awk -v OFS="\t" '{print $1, $2}' > WTHA.v2.ucsc_hg38_repeats_repeatmasker.names.txt
head WTHA.v2.ucsc_hg38_repeats_repeatmasker.names.txt
# 8	5S
# 1	7SK
# 16	7SLRNA
# 1	(AAAACAA)n
# 3	(AAAACA)n
# 15	(AAAAC)n
# 3	(AAAAG)n
# 1	(AAAATA)n
# 6	(AAAAT)n
# 5	(AAACA)n

grep -P "\tL1" WTHA.v2.ucsc_hg38_repeats_repeatmasker.names.txt > WTHA.v2.ucsc_hg38_repeats_repeatmasker.names.L1s.txt
head WTHA.v2.ucsc_hg38_repeats_repeatmasker.names.L1s.txt
# 134	L1HS
# 5	L1M
# 264	L1M1
# 228	L1M2
# 10	L1M2a
# 8	L1M2a1
# 8	L1M2b
# 24	L1M2c
# 91	L1M3
# 24	L1M3a

bedtools intersect -a <(grep "^chr" WTHA.v2.bed | cut -f1-3) -b ../repeatmasker/ucsc_hg38_repeats_repeatmasker.bed -wo | cut -f8 | sort | uniq -c | grep -v "?" | wc -l # 16 out of 16 classes of repeats overlap
bedtools intersect -a <(grep "^chr" WTHA.v2.bed | cut -f1-3) -b ../repeatmasker/ucsc_hg38_repeats_repeatmasker.bed -wo | cut -f8 | sort | uniq -c | grep -v "?" | awk -v OFS="\t" '{print $1, $2}' > WTHA.v2.ucsc_hg38_repeats_repeatmasker.classes.txt
cat WTHA.v2.ucsc_hg38_repeats_repeatmasker.classes.txt
# 1611	DNA
# 17331	LINE
# 211	Low_complexity
# 2500	LTR
# 1	RC
# 42	Retroposon
# 1	RNA
# 10	rRNA
# 61	Satellite
# 8	scRNA
# 1686	Simple_repeat
# 6616	SINE
# 43	snRNA
# 16	srpRNA
# 41	tRNA
# 6	Unknown

bedtools intersect -a <(grep "^chr" WTHA.v2.bed | cut -f1-3) -b ../repeatmasker/ucsc_hg38_repeats_repeatmasker.bed -wo | cut -f10 | sort | uniq -c | grep -v "?" | wc -l # 43 out of 53 families of repeats overlap
bedtools intersect -a <(grep "^chr" WTHA.v2.bed | cut -f1-3) -b ../repeatmasker/ucsc_hg38_repeats_repeatmasker.bed -wo | cut -f10 | sort | uniq -c | grep -v "?" | awk -v OFS="\t" '{print $1, $2}' > WTHA.v2.ucsc_hg38_repeats_repeatmasker.families.txt
cat WTHA.v2.ucsc_hg38_repeats_repeatmasker.families.txt
# 5	5S-Deu-L2
# 5923	Alu
# 39	centr
# 128	CR1
# 2	DNA
# 2	Dong-R4
# 513	ERV1
# 131	ERVK
# 443	ERVL
# 1361	ERVL-MaLR
# 21	Gypsy
# 6	hAT
# 9	hAT-Ac
# 24	hAT-Blackjack
# 524	hAT-Charlie
# 71	hAT-Tip100
# 1	Helitron
# 16380	L1
# 791	L2
# 211	Low_complexity
# 3	LTR
# 673	MIR
# 6	MULE-MuDR
# 4	Penelope
# 24	PiggyBac
# 1	RNA
# 10	rRNA
# 7	RTE-BovB
# 19	RTE-X
# 11	Satellite
# 8	scRNA
# 1686	Simple_repeat
# 43	snRNA
# 16	srpRNA
# 42	SVA
# 63	TcMar-Mariner
# 14	TcMar-Tc2
# 865	TcMar-Tigger
# 11	telo
# 43	tRNA
# 1	tRNA-Deu
# 12	tRNA-RTE
# 6	Unknown
```


#### visualisation

```r
#module load R-core/3.6.0-foss-2017a
#export R_LIBS_USER=/projects/qbio/bifo/R/3.6.0
#cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/custom
#R

library(data.table)
library(ggplot2)

# Enlarge the view width when printing tables
options(width = 250)


# all repeats
## classes
data <- fread('grep -v "^#" ../repeatmasker/ucsc_hg38_repeats_repeatmasker.txt | cut -f6 | sort | uniq -c')
data <- data[!V2 %like% "\\?$"]

### pie chart
ggplot(data = data, aes(x = "", y = V1, fill = V2)) +
geom_bar(stat = "identity", color = "black") +
coord_polar("y", direction = -1) +
theme_void() +
theme(legend.title=element_blank())

ggsave("../figures/ripseq_ucsc_hg38_repeats_repeatmasker_classes_pie.pdf", width = 15, height = 15, units= 'cm', useDingbats = FALSE)

### barplot
ggplot(data = data, aes(x = V1, y = factor(data$V2, levels = data[order(-V2)]$V2))) +
geom_bar(stat="identity") +
xlab("Count") +
ylab("Class of repeats") +
theme_bw() +
theme(axis.title = element_text(size=16), axis.text = element_text(size=12, color = "black"), axis.text.y = element_text(angle = 0, vjust = 0.5, hjust=1))

ggsave("../figures/ripseq_ucsc_hg38_repeats_repeatmasker_classes_bar.pdf", width = 15, height = 15, units= 'cm', useDingbats = FALSE)


## families
data <- fread('grep -v "^#" ../repeatmasker/ucsc_hg38_repeats_repeatmasker.txt | cut -f7 | sort | uniq -c')
data <- data[!V2 %like% "\\?$"]

## pie chart
ggplot(data = data, aes(x = "", y = V1, fill = V2)) +
geom_bar(stat = "identity", color = "black") +
coord_polar("y", direction = -1) +
theme_void() +
theme(legend.title=element_blank())

ggsave("../figures/ripseq_ucsc_hg38_repeats_repeatmasker_families_pie.pdf", width = 24, height = 24, units= 'cm', useDingbats = FALSE)

### barplot
ggplot(data = data, aes(x = V1, y = factor(data$V2, levels = data[order(-V2)]$V2))) +
geom_bar(stat="identity") +
xlab("Count") +
ylab("Family of repeats") +
theme_bw() +
theme(axis.title = element_text(size=16), axis.text = element_text(size=12, color = "black"), axis.text.y = element_text(angle = 0, vjust = 0.5, hjust=1))

ggsave("../figures/ripseq_ucsc_hg38_repeats_repeatmasker_families_bar.pdf", width = 15, height = 25, units= 'cm', useDingbats = FALSE)



# repeats overlapping with SETDB1-KO peaks
## classes
data <- fread('HA.SKOHA.v2.ucsc_hg38_repeats_repeatmasker.classes.txt')

### pie chart
ggplot(data = data, aes(x = "", y = V1, fill = V2)) +
geom_bar(stat = "identity", color = "black") +
coord_polar("y", direction = -1) +
theme_void() +
theme(legend.title=element_blank())

ggsave("../figures/ripseq_HA.SKOHA.v2.ucsc_hg38_repeats_repeatmasker_classes_pie.pdf", width = 15, height = 15, units= 'cm', useDingbats = FALSE)

### barplot
ggplot(data = data, aes(x = V1, y = factor(data$V2, levels = data[order(-V2)]$V2))) +
geom_bar(stat="identity") +
xlab("Count") +
ylab("Class of repeats") +
theme_bw() +
theme(axis.title = element_text(size=16), axis.text = element_text(size=12, color = "black"), axis.text.y = element_text(angle = 0, vjust = 0.5, hjust=1))

ggsave("../figures/ripseq_HA.SKOHA.v2.ucsc_hg38_repeats_repeatmasker_classes_bar.pdf", width = 15, height = 15, units= 'cm', useDingbats = FALSE)


## families
data <- fread('HA.SKOHA.v2.ucsc_hg38_repeats_repeatmasker.families.txt')

### pie chart
ggplot(data = data, aes(x = "", y = V1, fill = V2)) +
geom_bar(stat = "identity", color = "black") +
coord_polar("y", direction = -1) +
theme_void() +
theme(legend.title=element_blank())

ggsave("../figures/ripseq_HA.SKOHA.v2.ucsc_hg38_repeats_repeatmasker_families_pie.pdf", width = 24, height = 24, units= 'cm', useDingbats = FALSE)

### barplot
ggplot(data = data, aes(x = V1, y = factor(data$V2, levels = data[order(-V2)]$V2))) +
geom_bar(stat="identity") +
xlab("Count") +
ylab("Family of repeats") +
theme_bw() +
theme(axis.title = element_text(size=16), axis.text = element_text(size=12, color = "black"), axis.text.y = element_text(angle = 0, vjust = 0.5, hjust=1))

ggsave("../figures/ripseq_HA.SKOHA.v2.ucsc_hg38_repeats_repeatmasker_families_bar.pdf", width = 15, height = 25, units= 'cm', useDingbats = FALSE)



# repeats overlapping with WT peaks
## classes
data <- fread('WTHA.v2.ucsc_hg38_repeats_repeatmasker.classes.txt')

### pie chart
ggplot(data = data, aes(x = "", y = V1, fill = V2)) +
geom_bar(stat = "identity", color = "black") +
coord_polar("y", direction = -1) +
theme_void() +
theme(legend.title=element_blank())

ggsave("../figures/ripseq_WTHA.v2.ucsc_hg38_repeats_repeatmasker_classes_pie.pdf", width = 15, height = 15, units= 'cm', useDingbats = FALSE)

### barplot
ggplot(data = data, aes(x = V1, y = factor(data$V2, levels = data[order(-V2)]$V2))) +
geom_bar(stat="identity") +
xlab("Count") +
ylab("Class of repeats") +
theme_bw() +
theme(axis.title = element_text(size=16), axis.text = element_text(size=12, color = "black"), axis.text.y = element_text(angle = 0, vjust = 0.5, hjust=1))

ggsave("../figures/ripseq_WTHA.v2.ucsc_hg38_repeats_repeatmasker_classes_bar.pdf", width = 15, height = 15, units= 'cm', useDingbats = FALSE)


## families
data <- fread('WTHA.v2.ucsc_hg38_repeats_repeatmasker.families.txt')

### pie chart
ggplot(data = data, aes(x = "", y = V1, fill = V2)) +
geom_bar(stat = "identity", color = "black") +
coord_polar("y", direction = -1) +
theme_void() +
theme(legend.title=element_blank())

ggsave("../figures/ripseq_WTHA.v2.ucsc_hg38_repeats_repeatmasker_families_pie.pdf", width = 24, height = 24, units= 'cm', useDingbats = FALSE)

### barplot
ggplot(data = data, aes(x = V1, y = factor(data$V2, levels = data[order(-V2)]$V2))) +
geom_bar(stat="identity") +
xlab("Count") +
ylab("Family of repeats") +
theme_bw() +
theme(axis.title = element_text(size=16), axis.text = element_text(size=12, color = "black"), axis.text.y = element_text(angle = 0, vjust = 0.5, hjust=1))

ggsave("../figures/ripseq_WTHA.v2.ucsc_hg38_repeats_repeatmasker_families_bar.pdf", width = 15, height = 25, units= 'cm', useDingbats = FALSE)
```


#### gat

[Documentation](https://gat.readthedocs.io/en/latest/introduction.html)

Prepare workspaces:

```bash
# whitelist
## hg38-blacklist.v2.bed.gz obtained from https://github.com/Boyle-Lab/Blacklist/
cd /projects/qbio/bifo/users/sergio/reference/blacklist
cut -f1-2 ../gencode/release35_GRCh38.p13/fasta/GRCh38.p13.genome.fa.fai | awk -v OFS="\t" '{print $1, 0, $2}' | bedtools subtract -a - -b <(zcat hg38-blacklist.v2.bed.gz) | sort -k1,1 -k2,2n > hg38.whitelist.v2.bed

# genes
# /projects/qbio/bifo/users/sergio/repository/20201122_hush/annotation/gencode.v35.annotation.gene_type.intronless.bed
```

Copy the following files to `ukc02zt7gvmd6n`:

Segments:
  - /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/custom/HA.SKOHA.v2.bed
  - /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/custom/WTHA.v2.bed

Annotations:
  - /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/repeatmasker/ucsc_hg38_repeats_repeatmasker.names.L1s.bed
  - /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/repeatmasker/ucsc_hg38_repeats_repeatmasker.classes.bed
  - /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/repeatmasker/ucsc_hg38_repeats_repeatmasker.families.bed

Workspaces:
  - /projects/qbio/bifo/users/sergio/reference/blacklist/hg38.whitelist.v2.bed
  - /projects/qbio/bifo/users/sergio/repository/20201122_hush/annotation/gencode.v35.annotation.gene_type.intronless.bed

Run gat.py:

```bash
cd /Users/kzqv978/Desktop/gat
ls -1
# HA.SKOHA.v2.bed
# WTHA.v2.bed
# gencode.v35.annotation.gene_type.intronless.bed
# hg38.whitelist.v2.bed
# ucsc_hg38_repeats_repeatmasker.classes.bed
# ucsc_hg38_repeats_repeatmasker.families.bed
# ucsc_hg38_repeats_repeatmasker.names.L1s.bed

cut -f1-3 gencode.v35.annotation.gene_type.intronless.bed > gencode.v35.annotation.gene_type.intronless.cut.bed # just chr,start,end otherwise gat-run.py fails

mkdir whitelist
mkdir genes

for seg in HA.SKOHA.v2.bed WTHA.v2.bed
do
  bname1=${seg%.bed}
  for ann in ucsc_hg38_repeats_repeatmasker.names.L1s.bed ucsc_hg38_repeats_repeatmasker.classes.bed ucsc_hg38_repeats_repeatmasker.families.bed
  do
    bname2=${ann%.bed}
    nohup /Users/kzqv978/Library/Python/2.7/bin/gat-run.py -s $seg -a $ann -w hg38.whitelist.v2.bed --ignore-segment-tracks -n 10000 -L whitelist/${bname1}_${bname2}.log > whitelist/${bname1}_${bname2}.txt &
    nohup /Users/kzqv978/Library/Python/2.7/bin/gat-run.py -s $seg -a $ann -w gencode.v35.annotation.gene_type.intronless.cut.bed --ignore-segment-tracks -n 10000 -L genes/${bname1}_${bname2}.log > genes/${bname1}_${bname2}.txt &
  done
done

tail whitelist/*.log
tail genes/*.log

cut -f2-11 whitelist/HA.SKOHA.v2_ucsc_hg38_repeats_repeatmasker.names.L1s.txt | column -t
# annotation  observed  expected     CI95low      CI95high     stddev      fold     l2fold    pvalue      qvalue
# L1P4e       0         1668.4270    0.0000       4776.0000    1569.4947   0.0006   -10.7051  1.2880e-01  1.4737e-01
# L1P4c       0         175.7404     0.0000       1026.0000    360.8218    0.0057   -7.4655   6.7980e-01  6.7980e-01
# L1P4b       306       1275.8766    0.0000       4021.0000    1384.7360   0.2404   -2.0563   3.3080e-01  3.5907e-01
# L1ME4a      23327     75659.5369   66456.0000   85366.0000   5784.6900   0.3083   -1.6975   1.0000e-04  1.5875e-04
# L1ME3G      17496     42260.4071   34677.0000   50157.0000   4707.6878   0.4140   -1.2722   1.0000e-04  1.5875e-04
# L1ME4b      24864     55693.8111   47235.0000   64520.0000   5272.9874   0.4465   -1.1634   1.0000e-04  1.5875e-04
# L1MC5a      23729     51139.5297   41706.0000   61378.0000   6029.2650   0.4640   -1.1078   1.0000e-04  1.5875e-04
# L1ME3E      7669      16257.5606   10158.0000   23168.0000   4000.9348   0.4718   -1.0839   6.3000e-03  8.6967e-03
# L1ME4c      9195      19179.1309   15123.0000   23573.0000   2595.7612   0.4795   -1.0605   1.0000e-04  1.5875e-04
# L1P         71        140.8486     0.0000       351.0000     229.7314    0.5076   -0.9783   3.9960e-01  4.0599e-01
# L1MC5       30243     46471.4336   37254.0000   56434.0000   5811.2971   0.6508   -0.6197   9.0000e-04  1.3291e-03
# L1MC4a      26320     39668.5982   29984.0000   50159.0000   6129.7774   0.6635   -0.5918   8.7000e-03  1.1509e-02
# L1MEg2      1708      2430.1118    450.0000     5118.0000    1443.0728   0.7030   -0.5085   3.4550e-01  3.7033e-01
# L1ME3A      43193     60578.0928   48125.0000   73983.0000   7794.1152   0.7130   -0.4880   8.6000e-03  1.1497e-02
# L1MC3       53043     73023.9451   57214.0000   90044.0000   9915.0415   0.7264   -0.4612   1.6300e-02  2.1123e-02
# L1ME3C      7937      10867.1178   6536.0000    15934.0000   2879.9439   0.7304   -0.4533   1.5220e-01  1.7258e-01
# L1PBb       2942      3922.7429    110.0000     9376.0000    2861.0251   0.7500   -0.4149   4.3110e-01  4.3452e-01
# L1M5        117923    155291.1549  139623.0000  171360.0000  9814.5509   0.7594   -0.3971   1.0000e-04  1.5875e-04
# L1ME5       7655      10052.1663   6237.0000    14541.0000   2519.7933   0.7616   -0.3930   1.6670e-01  1.8571e-01
# L1M         656       807.4841     299.0000     1426.0000    411.2954    0.8126   -0.2993   3.7630e-01  3.9172e-01
# L1ME3Cz     30648     36972.6301   28940.0000   45854.0000   5152.0255   0.8289   -0.2707   1.0340e-01  1.2159e-01
# L1ME3F      13673     16214.0736   10282.0000   23107.0000   3883.2450   0.8433   -0.2459   2.6860e-01  2.9407e-01
# L1ME3D      14468     15799.5020   9517.0000    23080.0000   4117.2123   0.9157   -0.1270   3.9370e-01  4.0333e-01
# L1MC4       89611     86373.1528   72763.0000   100509.0000  8449.0248   1.0375   0.0531    3.4700e-01  3.7033e-01
# L1ME3       35843     34511.5427   25202.0000   44849.0000   5981.3892   1.0386   0.0546    3.9380e-01  4.0333e-01
# L1MEh       10032     9231.0221    5158.0000    13935.0000   2683.9130   1.0868   0.1200    3.5630e-01  3.7397e-01
# L1M3b       5129      4467.7274    805.0000     9605.0000    2765.9370   1.1480   0.1991    3.5030e-01  3.7073e-01
# L1ME2       66670     57965.7598   44512.0000   72303.0000   8466.5601   1.1502   0.2018    1.5450e-01  1.7364e-01
# L1M6        19721     16889.5004   11675.0000   22625.0000   3368.2016   1.1676   0.2236    1.9710e-01  2.1767e-01
# L1MC        31589     24393.9764   18012.0000   31298.0000   4025.9424   1.2949   0.3729    4.3200e-02  5.3266e-02
# L1ME3B      39304     29455.2718   20847.0000   38975.0000   5531.0771   1.3344   0.4161    4.5200e-02  5.5196e-02
# L1MCc       19364     14321.6793   8185.0000    21259.0000   4031.6256   1.3521   0.4352    1.1210e-01  1.2942e-01
# L1ME2z      32982     24336.6541   16515.0000   33145.0000   5021.5909   1.3552   0.4385    5.2100e-02  6.3016e-02
# L1MA10      26246     18109.5891   11494.0000   25771.0000   4348.0396   1.4493   0.5353    4.1000e-02  5.1049e-02
# L1MD        44040     28660.2735   21438.0000   36405.0000   4576.3213   1.5366   0.6197    1.8000e-03  2.5977e-03
# L1MEd       126042    76248.3354   62817.0000   90486.0000   8473.0157   1.6530   0.7251    1.0000e-04  1.5875e-04
# L1MEi       29003     17298.1257   11547.0000   23669.0000   3677.2046   1.6766   0.7456    1.4000e-03  2.0437e-03
# L1MCb       20966     12190.7888   6412.0000    19009.0000   3874.9955   1.7198   0.7822    2.0300e-02  2.5908e-02
# L1P5        1908      1076.0792    252.0000     2330.0000    646.1152    1.7724   0.8257    1.0540e-01  1.2281e-01
# L1MB5       75129     40484.0535   30208.0000   51632.0000   6527.9876   1.8557   0.8920    1.0000e-04  1.5875e-04
# L1MC2       61395     32733.6801   22618.0000   43939.0000   6510.4305   1.8756   0.9073    1.0000e-04  1.5875e-04
# L1MA1       87621     45248.6948   30162.0000   62232.0000   9817.3174   1.9364   0.9534    1.0000e-04  1.5875e-04
# L1M3        49092     24615.9384   17431.0000   32324.0000   4529.0907   1.9943   0.9959    1.0000e-04  1.5875e-04
# L1ME1       318232    156576.5822  133748.0000  180531.0000  14217.9076  2.0324   1.0232    1.0000e-04  1.5875e-04
# L1MEg       120711    59298.3974   46336.0000   72924.0000   8110.6637   2.0356   1.0255    1.0000e-04  1.5875e-04
# L1MA8       115062    56435.3310   42933.0000   71186.0000   8664.3017   2.0388   1.0277    1.0000e-04  1.5875e-04
# L1M7        24429     11714.9791   6868.0000    17311.0000   3189.4014   2.0852   1.0602    6.0000e-04  9.1807e-04
# L1MA9       179176    81313.5868   64462.0000   99289.0000   10657.4364  2.2035   1.1398    1.0000e-04  1.5875e-04
# L1MEj       16679     7511.8160    3908.0000    11606.0000   2347.3802   2.2202   1.1507    1.0000e-04  1.5875e-04
# L1MB1       73997     33228.0872   22639.0000   44994.0000   6835.7760   2.2269   1.1550    1.0000e-04  1.5875e-04
# L1MB8       171324    73872.1396   59283.0000   89698.0000   9282.2514   2.3192   1.2136    1.0000e-04  1.5875e-04
# L1MEb       15370     6429.5558    3005.0000    10458.0000   2293.0252   2.3903   1.2572    8.0000e-04  1.2095e-03
# L1MB3       173865    71983.2110   58264.0000   86596.0000   8620.5471   2.4153   1.2722    1.0000e-04  1.5875e-04
# L1M3c       14896     6143.6015    1959.0000    11557.0000   2938.0819   2.4244   1.2776    6.8000e-03  9.1872e-03
# L1MD2       127314    51967.1277   39483.0000   66005.0000   8062.5970   2.4499   1.2927    1.0000e-04  1.5875e-04
# L1MB4       89223     36217.9128   26428.0000   47307.0000   6369.3694   2.4635   1.3007    1.0000e-04  1.5875e-04
# L1MB7       265249    107412.5774  89427.0000   126435.0000  11332.1739  2.4694   1.3042    1.0000e-04  1.5875e-04
# L1M8        17068     6908.6359    3146.0000    11452.0000   2537.5307   2.4703   1.3047    5.0000e-04  7.8395e-04
# L1MD3       46072     18312.8053   12030.0000   25536.0000   4133.4811   2.5158   1.3310    1.0000e-04  1.5875e-04
# L1MA7       107888    42191.0857   30779.0000   55109.0000   7397.2454   2.5571   1.3545    1.0000e-04  1.5875e-04
# L1MC1       209725    80956.1157   63878.0000   99320.0000   10852.6837  2.5906   1.3733    1.0000e-04  1.5875e-04
# L1PB2       62184     23762.4834   13999.0000   35042.0000   6481.2341   2.6168   1.3878    1.0000e-04  1.5875e-04
# L1MD1       109716    41395.8860   28539.0000   55862.0000   8372.4592   2.6504   1.4062    1.0000e-04  1.5875e-04
# L1MCa       116333    43093.2272   30532.0000   57057.0000   8058.2088   2.6995   1.4327    1.0000e-04  1.5875e-04
# L1M4        243909    88291.3825   72415.0000   105425.0000  10052.8652  2.7625   1.4660    1.0000e-04  1.5875e-04
# L1PA12      42339     15308.6800   7671.0000    24153.0000   5051.8703   2.7656   1.4676    1.0000e-04  1.5875e-04
# L1MEc       256878    90936.3978   74151.0000   108786.0000  10547.2489  2.8248   1.4981    1.0000e-04  1.5875e-04
# L1PA16      329567    115055.8534  93378.0000   138326.0000  13734.1996  2.8644   1.5182    1.0000e-04  1.5875e-04
# L1M1        273247    93834.3252   72327.0000   116674.0000  13473.3170  2.9120   1.5420    1.0000e-04  1.5875e-04
# L1MEa       4342      1459.6368    0.0000       3752.0000    1180.7007   2.9734   1.5721    2.4400e-02  3.0681e-02
# L1MEf       176722    59393.6059   45304.0000   74379.0000   8877.4245   2.9754   1.5731    1.0000e-04  1.5875e-04
# L1PA17      100413    32833.0458   21699.0000   45483.0000   7314.5207   3.0582   1.6127    1.0000e-04  1.5875e-04
# L1P4d       4554      1485.0278    0.0000       4646.0000    1620.7679   3.0652   1.6160    5.3700e-02  6.4339e-02
# L1MA6       102431    33328.8932   22282.0000   45935.0000   7224.4259   3.0733   1.6198    1.0000e-04  1.5875e-04
# L1MA5A      57446     18618.2183   10691.0000   28021.0000   5273.7702   3.0854   1.6254    1.0000e-04  1.5875e-04
# L1M4a1      36990     11905.2027   6533.0000    18288.0000   3592.9366   3.1069   1.6355    1.0000e-04  1.5875e-04
# L1P3b       1782      572.8847     0.0000       2144.0000    812.3102    3.1069   1.6355    8.2300e-02  9.7683e-02
# L1P4        52808     16901.9620   10667.0000   23806.0000   4003.6923   3.1242   1.6435    1.0000e-04  1.5875e-04
# L1PA13      249544    79689.6119   61217.0000   100152.0000  11768.0088  3.1314   1.6468    1.0000e-04  1.5875e-04
# L1PA10      217821    69454.2539   51461.0000   89054.0000   11382.0461  3.1361   1.6490    1.0000e-04  1.5875e-04
# L1M4a2      29447     9363.5614    4441.0000    15226.0000   3294.7201   3.1446   1.6529    1.0000e-04  1.5875e-04
# L1M2a       15475     4820.2532    584.0000     10521.0000   3084.5553   3.2100   1.6826    3.8000e-03  5.3033e-03
# L1PA8       230376    71280.0386   53046.0000   91238.0000   11608.0442  3.2320   1.6924    1.0000e-04  1.5875e-04
# L1M2        180182    55525.5198   41539.0000   71011.0000   8941.6592   3.2450   1.6982    1.0000e-04  1.5875e-04
# L1M3de      10183     3120.6658    110.0000     7582.0000    2333.0280   3.2624   1.7059    1.0100e-02  1.3224e-02
# L1MDb       16058     4910.8748    1436.0000    9378.0000    2430.2224   3.2694   1.7090    1.0000e-04  1.5875e-04
# L1M6B       4272      1277.3646    302.0000     2841.0000    807.8908    3.3426   1.7409    3.5000e-03  4.9389e-03
# L1MEg1      9744      2850.3358    721.0000     5616.0000    1525.3079   3.4177   1.7730    6.0000e-04  9.1807e-04
# L1M4b       82915     24247.8532   15103.0000   34584.0000   5931.3606   3.4194   1.7737    1.0000e-04  1.5875e-04
# L1MB2       130215    37498.5368   27456.0000   48693.0000   6504.2499   3.4725   1.7960    1.0000e-04  1.5875e-04
# L1MA4       228718    65404.2292   49370.0000   83082.0000   10249.7180  3.4970   1.8061    1.0000e-04  1.5875e-04
# L1PA15      301885    83215.7246   63449.0000   104201.0000  12516.4112  3.6277   1.8591    1.0000e-04  1.5875e-04
# L1PB3       104719    28759.2686   17862.0000   41174.0000   7102.7817   3.6411   1.8644    1.0000e-04  1.5875e-04
# L1PBa1      28202     7743.3532    2023.0000    15157.0000   4006.1534   3.6418   1.8646    1.0000e-04  1.5875e-04
# L1MA2       283256    77388.6480   57306.0000   99124.0000   12847.5154  3.6601   1.8719    1.0000e-04  1.5875e-04
# L1PB4       161827    43938.6388   31670.0000   57740.0000   7959.9454   3.6830   1.8809    1.0000e-04  1.5875e-04
# L1PA11      159503    42832.5999   28664.0000   58670.0000   9138.7082   3.7238   1.8968    1.0000e-04  1.5875e-04
# L1M3a       12639     3390.8384    569.0000     7211.0000    2064.4150   3.7266   1.8979    9.0000e-04  1.3291e-03
# L1M2b       5650      1512.1365    0.0000       4417.0000    1477.3090   3.7346   1.9010    2.0400e-02  2.5908e-02
# L1PA15-16   57304     15201.2224   6877.0000    25241.0000   5588.0036   3.7695   1.9144    1.0000e-04  1.5875e-04
# L1MDa       154390    39904.7171   27921.0000   52724.0000   7588.0423   3.8689   1.9519    1.0000e-04  1.5875e-04
# L1M3e       22438     5785.3559    1563.0000    11160.0000   2954.9096   3.8779   1.9553    1.0000e-04  1.5875e-04
# L1M3d       10782     2728.5870    266.0000     6427.0000    1956.0767   3.9504   1.9820    1.9000e-03  2.7112e-03
# L1PB        37705     9438.2315    4690.0000    15179.0000   3175.6123   3.9946   1.9981    1.0000e-04  1.5875e-04
# L1PREC2     278407    65119.6805   48907.0000   82921.0000   10390.4629  4.2753   2.0960    1.0000e-04  1.5875e-04
# L1MA5       124891    29159.0701   18392.0000   41404.0000   6993.8000   4.2830   2.0986    1.0000e-04  1.5875e-04
# L1PA14      132306    30835.0514   19499.0000   44081.0000   7495.1868   4.2907   2.1012    1.0000e-04  1.5875e-04
# L1M3f       26934     6173.0833    1581.0000    12265.0000   3299.3682   4.3626   2.1252    1.0000e-04  1.5875e-04
# L1PB1       602319    137312.8199  110079.0000  166280.0000  17122.2349  4.3864   2.1331    1.0000e-04  1.5875e-04
# L1PA8A      149034    33907.8084   20885.0000   48661.0000   8530.3115   4.3952   2.1359    1.0000e-04  1.5875e-04
# L1MA3       404939    89887.9246   67841.0000   113632.0000  13952.4494  4.5049   2.1715    1.0000e-04  1.5875e-04
# L1PBa       122084    26385.8060   15233.0000   39155.0000   7291.1632   4.6267   2.2100    1.0000e-04  1.5875e-04
# L1MA4A      246189    53106.4874   37213.0000   70840.0000   10267.3798  4.6357   2.2128    1.0000e-04  1.5875e-04
# L1M4c       109717    20941.6331   12330.0000   30817.0000   5642.9291   5.2390   2.3893    1.0000e-04  1.5875e-04
# L1PA7       1125941   207693.1753  173642.0000  243851.0000  21391.0836  5.4212   2.4386    1.0000e-04  1.5875e-04
# L1P4a       35170     6428.3323    1442.0000    12840.0000   3539.5263   5.4704   2.4516    1.0000e-04  1.5875e-04
# L1P3        156407    28434.6029   18108.0000   40206.0000   6761.8074   5.5004   2.4595    1.0000e-04  1.5875e-04
# L1M2a1      6789      1201.0644    0.0000       4127.0000    1451.3244   5.6486   2.4979    6.4000e-03  8.7398e-03
# L1PA6       657350    115396.9929  89331.0000   144421.0000  16659.4975  5.6964   2.5100    1.0000e-04  1.5875e-04
# L1HS        195574    33899.7254   20305.0000   49508.0000   8920.7901   5.7691   2.5283    1.0000e-04  1.5875e-04
# L1PA5       1280105   166341.7698  136483.0000  197726.0000  18746.5322  7.6956   2.9440    1.0000e-04  1.5875e-04
# L1PA4       1672567   193557.8821  160947.0000  227624.0000  20437.5143  8.6411   3.1112    1.0000e-04  1.5875e-04
# L1P1        248060    28628.6490   18665.0000   39995.0000   6520.5005   8.6645   3.1151    1.0000e-04  1.5875e-04
# L1P2        205578    22914.8254   12617.0000   35029.0000   6783.0513   8.9710   3.1653    1.0000e-04  1.5875e-04
# L1M2c       34830     3520.6075    168.0000     8422.0000    2564.2324   9.8907   3.3061    1.0000e-04  1.5875e-04
# L1PA2       1130321   96674.8675   72893.0000   122995.0000  15195.0385  11.6919  3.5474    1.0000e-04  1.5875e-04
# L1PA3       2321205   195592.3546  162493.0000  230155.0000  20641.3332  11.8675  3.5689    1.0000e-04  1.5875e-04

cut -f2-11 whitelist/HA.SKOHA.v2_ucsc_hg38_repeats_repeatmasker.classes.txt | column -t
# annotation      observed  expected      CI95low       CI95high      stddev      fold    l2fold   pvalue      qvalue
# RC              142       3807.8935     1995.0000     5872.0000     1187.3088   0.0375  -4.7353  1.0000e-04  1.6000e-04
# Unknown         1101      7729.5346     5891.0000     9705.0000     1159.4328   0.1426  -2.8104  1.0000e-04  1.6000e-04
# Simple_repeat   112504    363872.7836   350313.0000   378167.0000   8437.9279   0.3092  -1.6934  1.0000e-04  1.6000e-04
# Satellite       40224     120604.3149   88446.0000    155496.0000   20434.9888  0.3335  -1.5841  1.0000e-04  1.6000e-04
# Low_complexity  20487     61347.5270    56584.0000    66421.0000    2996.6706   0.3340  -1.5822  1.0000e-04  1.6000e-04
# LTR             1184090   2773541.1202  2687061.0000  2860582.0000  52873.6915  0.4269  -1.2279  1.0000e-04  1.6000e-04
# scRNA           644       1337.9030     752.0000      1976.0000     372.6345    0.4817  -1.0537  2.2300e-02  3.2436e-02
# SINE            2087385   4081032.0458  4009538.0000  4155632.0000  44621.0815  0.5115  -0.9672  1.0000e-04  1.6000e-04
# RNA             693       1158.8776     436.0000      1979.0000     473.7350    0.5983  -0.7410  1.6640e-01  1.7749e-01
# rRNA            1136      1773.2964     861.0000      3198.0000     754.5555    0.6408  -0.6420  1.6500e-01  1.7749e-01
# Retroposon      31696     44179.2330    32270.0000    57116.0000    7539.8064   0.7174  -0.4791  4.3500e-02  5.8000e-02
# DNA             882720    1061857.2836  1023792.0000  1100948.0000  23582.2299  0.8313  -0.2666  1.0000e-04  1.6000e-04
# srpRNA          3311      2785.3775     1502.0000     4219.0000     820.0211    1.1886  0.2493   2.4900e-01  2.4900e-01
# snRNA           4316      3475.2281     2568.0000     4465.0000     571.7161    1.2419  0.3125   7.6400e-02  9.4031e-02
# tRNA            2599      1035.7128     595.0000      1544.0000     289.7840    2.5079  1.3265   1.0000e-04  1.6000e-04
# LINE            19962368  6700796.0124  6562787.0000  6840761.0000  84640.1965  2.9791  1.5749   1.0000e-04  1.6000e-04

cut -f2-11 whitelist/HA.SKOHA.v2_ucsc_hg38_repeats_repeatmasker.families.txt | column -t
# annotation      observed  expected      CI95low       CI95high      stddev      fold    l2fold   pvalue      qvalue
# TcMar           0         308.5303      0.0000        831.0000      276.2707    0.0032  -8.2739  2.1910e-01  2.7515e-01
# hAT-Tag1        0         194.2464      0.0000        495.0000      158.6462    0.0051  -7.6092  1.9820e-01  2.6473e-01
# Merlin          0         180.7730      0.0000        1029.0000     355.2905    0.0055  -7.5060  6.5400e-01  7.6774e-01
# acro            0         111.8918      0.0000        989.0000      358.9725    0.0089  -6.8188  8.7050e-01  9.5933e-01
# TcMar-Pogo      0         42.2995       0.0000        173.0000      72.0396     0.0231  -5.4363  6.8170e-01  7.8323e-01
# Helitron        142       3798.2393     1986.0000     5898.0000     1199.7771   0.0376  -4.7316  1.0000e-04  2.0769e-04
# MULE-MuDR       369       7133.8959     3143.0000     11904.0000    2681.4433   0.0519  -4.2693  3.0000e-04  6.0000e-04
# PIF-Harbinger   0         17.4354       0.0000        86.0000       31.9367     0.0542  -4.2044  7.2410e-01  8.1461e-01
# DNA             267       3119.4515     1980.0000     4394.0000     730.5550    0.0859  -3.5414  1.0000e-04  2.0769e-04
# Gypsy           3492      37549.9901    30894.0000    44390.0000    4116.4363   0.0930  -3.4263  1.0000e-04  2.0769e-04
# LTR             971       7108.5143     4578.0000     9896.0000     1623.4707   0.1367  -2.8707  1.0000e-04  2.0769e-04
# Unknown         1101      7738.9572     5908.0000     9772.0000     1164.3192   0.1424  -2.8122  1.0000e-04  2.0769e-04
# hAT-Blackjack   5486      35037.4335    28658.0000    42043.0000    4100.3572   0.1566  -2.6748  1.0000e-04  2.0769e-04
# MIR             143991    877580.4246   854405.0000   901074.0000   14244.0945  0.1641  -2.6075  1.0000e-04  2.0769e-04
# hAT             1988      11749.4189    9491.0000     14146.0000    1420.0555   0.1693  -2.5626  1.0000e-04  2.0769e-04
# 5S-Deu-L2       526       2871.9364     1837.0000     4006.0000     661.3075    0.1834  -2.4467  1.0000e-04  2.0769e-04
# tRNA-RTE        1387      7402.8766     5680.0000     9230.0000     1084.6066   0.1875  -2.4153  1.0000e-04  2.0769e-04
# RTE-X           6913      36843.0497    30205.0000    43908.0000    4175.6154   0.1877  -2.4138  1.0000e-04  2.0769e-04
# hAT-Tip100      19549     88388.3641    77925.0000    99221.0000    6507.6291   0.2212  -2.1767  1.0000e-04  2.0769e-04
# centr           21942     91976.2143    62533.0000    125250.0000   19013.0466  0.2386  -2.0675  1.0000e-04  2.0769e-04
# L2              295148    1083235.1918  1044260.0000  1122786.0000  23704.4945  0.2725  -1.8758  1.0000e-04  2.0769e-04
# TcMar-Tc2       4687      17045.0471    12880.0000    21530.0000    2657.3239   0.2750  -1.8624  1.0000e-04  2.0769e-04
# Penelope        287       1015.7548     476.0000      1681.0000     370.6819    0.2833  -1.8198  7.0000e-03  1.3500e-02
# RTE-BovB        3995      13754.8625    10680.0000    17106.0000    1948.3141   0.2905  -1.7834  1.0000e-04  2.0769e-04
# tRNA-Deu        194       665.5271      229.0000      1191.0000     295.3018    0.2926  -1.7732  3.4300e-02  6.1740e-02
# Simple_repeat   112504    363821.6306   350126.0000   378025.0000   8457.1742   0.3092  -1.6932  1.0000e-04  2.0769e-04
# hAT-Charlie     151746    475532.7272   453962.0000   497192.0000   13045.4623  0.3191  -1.6479  1.0000e-04  2.0769e-04
# CR1             41180     125592.6419   113960.0000   137598.0000   7146.8519   0.3279  -1.6087  1.0000e-04  2.0769e-04
# Low_complexity  20487     61345.3918    56685.0000    66327.0000    2970.2786   0.3340  -1.5822  1.0000e-04  2.0769e-04
# ERV1            300963    873341.2106   813613.0000   934556.0000   36711.6186  0.3446  -1.5370  1.0000e-04  2.0769e-04
# ERVL            216405    590004.7070   552144.0000   628812.0000   23245.8651  0.3668  -1.4470  1.0000e-04  2.0769e-04
# scRNA           644       1340.7628     768.0000      1966.0000     368.0669    0.4807  -1.0568  1.8500e-02  3.4448e-02
# ERVL-MaLR       574863    1152934.2920  1107914.0000  1199356.0000  28117.3256  0.4986  -1.0040  1.0000e-04  2.0769e-04
# Dong-R4         616       1207.7393     307.0000      2393.0000     645.2839    0.5104  -0.9702  1.8270e-01  2.5297e-01
# Satellite       15075     27239.6345    14459.0000    42431.0000    8674.5933   0.5534  -0.8535  6.2100e-02  1.0479e-01
# RNA             693       1157.5676     449.0000      1999.0000     473.9289    0.5990  -0.7393  1.6720e-01  2.5080e-01
# Alu             1940633   3189871.3207  3120472.0000  3261286.0000  42404.6171  0.6084  -0.7170  1.0000e-04  2.0769e-04
# rRNA            1136      1752.0369     851.0000      3179.0000     746.7763    0.6486  -0.6246  1.7740e-01  2.5209e-01
# SVA             31696     44074.5185    31962.0000    56644.0000    7489.0591   0.7192  -0.4756  4.6100e-02  8.0303e-02
# hAT-Ac          3404      4108.4830     2937.0000     5406.0000     748.1284    0.8286  -0.2713  1.7240e-01  2.5161e-01
# tRNA            3269      3893.0235     2735.0000     5193.0000     747.0122    0.8397  -0.2520  2.0730e-01  2.6653e-01
# ERVK            80701     91054.9269    71517.0000    111937.0000   12236.2977  0.8863  -0.1741  2.0100e-01  2.6473e-01
# TcMar-Mariner   26931     28948.1482    22269.0000    36193.0000    4256.8631   0.9303  -0.1042  3.2930e-01  3.9516e-01
# Kolobok         0         0.0000        0.0000        0.0000        0.0000      1.0000  0.0000   1.0000e+00  1.0000e+00
# L1-Tx1          0         0.0000        0.0000        0.0000        0.0000      1.0000  0.0000   1.0000e+00  1.0000e+00
# I-Jockey        0         0.0000        0.0000        0.0000        0.0000      1.0000  0.0000   1.0000e+00  1.0000e+00
# Crypton         0         0.0000        0.0000        0.0000        0.0000      1.0000  0.0000   1.0000e+00  1.0000e+00
# TcMar-Tc1       0         0.0000        0.0000        0.0000        0.0000      1.0000  0.0000   1.0000e+00  1.0000e+00
# srpRNA          3311      2774.0941     1507.0000     4217.0000     832.1649    1.1935  0.2552   2.5300e-01  3.1050e-01
# snRNA           4316      3464.4855     2540.0000     4452.0000     582.9607    1.2457  0.3170   7.6200e-02  1.2102e-01
# PiggyBac        7779      5229.9020     2771.0000     8209.0000     1656.6077   1.4873  0.5727   7.2800e-02  1.1913e-01
# TcMar-Tigger    659932    378108.2807   350492.0000   406513.0000   17135.1159  1.7453  0.8035   1.0000e-04  2.0769e-04
# telo            3207      1594.7630     0.0000        4555.0000     1521.7239   2.0103  1.0074   1.4530e-01  2.2418e-01
# L1              19614248  5440768.5087  5298411.0000  5584487.0000  86292.8498  3.6051  1.8500   1.0000e-04  2.0769e-04

cut -f2-11 genes/HA.SKOHA.v2_ucsc_hg38_repeats_repeatmasker.names.L1s.txt | column -t
# annotation  observed  expected     CI95low      CI95high     stddev      fold     l2fold    pvalue      qvalue
# L1P4e       0         1090.6558    0.0000       3743.0000    1308.3378   0.0009   -10.0923  2.7980e-01  3.0371e-01
# L1P4c       0         127.3380     0.0000       820.0000     306.3279    0.0078   -7.0038   7.7270e-01  7.7270e-01
# L1ME4a      19287     70519.3415   61623.0000   79879.0000   5553.5893   0.2735   -1.8703   1.0000e-04  1.5301e-04
# L1ME4b      22489     50152.9325   42141.0000   58503.0000   4982.9875   0.4484   -1.1571   1.0000e-04  1.5301e-04
# L1ME3G      16683     36557.5577   29689.0000   43814.0000   4354.6190   0.4564   -1.1317   1.0000e-04  1.5301e-04
# L1ME4c      9233      18383.0347   14377.0000   22763.0000   2557.8902   0.5023   -0.9934   1.0000e-04  1.5301e-04
# L1MC5a      22934     45443.2494   36461.0000   55046.0000   5624.8916   0.5047   -0.9865   1.0000e-04  1.5301e-04
# L1ME3E      7273      13674.5085   7961.0000    20268.0000   3719.8702   0.5319   -0.9108   2.9100e-02  3.6232e-02
# L1P4b       306       442.3397     0.0000       2144.0000    813.0629    0.6925   -0.5302   6.9210e-01  6.9759e-01
# L1MC4a      25012     34884.2159   25533.0000   45011.0000   5907.4345   0.7170   -0.4799   3.8300e-02  4.7224e-02
# L1MC5       30086     41231.3911   32463.0000   50772.0000   5510.8037   0.7297   -0.4546   1.4300e-02  1.8344e-02
# L1MEg2      1185      1520.4260    68.0000      3756.0000    1166.6242   0.7795   -0.3593   4.6080e-01  4.7969e-01
# L1ME3C      7253      9234.8535    5262.0000    13901.0000   2658.0155   0.7854   -0.3485   2.3950e-01  2.6221e-01
# L1ME3A      38415     46827.5699   36266.0000   58571.0000   6721.4401   0.8204   -0.2857   9.9100e-02  1.1442e-01
# L1M5        101823    121147.1257  107100.0000  135401.0000  8673.3341   0.8405   -0.2507   1.2500e-02  1.6199e-02
# L1M         592       655.6347     200.0000     1209.0000    407.9187    0.9031   -0.1471   4.9810e-01  5.1015e-01
# L1MC3       44145     48661.8452   36340.0000   62410.0000   8049.3793   0.9072   -0.1405   2.9830e-01  3.2105e-01
# L1ME3F      13366     13867.9381   8250.0000    20514.0000   3739.5264   0.9638   -0.0532   4.7600e-01  4.9148e-01
# L1P         71        72.7551      0.0000       224.0000     78.3266     0.9762   -0.0347   5.9260e-01  6.0208e-01
# L1ME3Cz     30806     31549.0041   23908.0000   39855.0000   4890.9665   0.9764   -0.0344   4.6040e-01  4.7969e-01
# L1ME3D      13542     12882.0475   7145.0000    19669.0000   3789.9994   1.0512   0.0721    3.9680e-01  4.1995e-01
# L1ME5       8326      7633.6341    4281.0000    11649.0000   2237.2657   1.0907   0.1252    3.5360e-01  3.7737e-01
# L1ME3       31509     27355.4576   18984.0000   36741.0000   5374.9824   1.1518   0.2039    2.1210e-01  2.3838e-01
# L1MC4       87699     74573.3585   62173.0000   87697.0000   7767.0636   1.1760   0.2339    4.9800e-02  6.0234e-02
# L1MEh       8515      6623.0788    3153.0000    10683.0000   2319.8259   1.2856   0.3625    2.0040e-01  2.2724e-01
# L1M6        19551     14389.4043   9476.0000    19904.0000   3192.3541   1.3587   0.4422    6.0200e-02  7.0791e-02
# L1ME2       63912     45166.4710   33393.0000   58216.0000   7495.9916   1.4150   0.5008    1.0000e-02  1.3093e-02
# L1M3b       4037      2738.2350    0.0000       6819.0000    2165.6058   1.4741   0.5599    2.3450e-01  2.5897e-01
# L1ME2z      31321     20864.2201   13817.0000   28846.0000   4601.1690   1.5012   0.5861    2.0700e-02  2.6289e-02
# L1ME3B      37396     24432.9629   16562.0000   33154.0000   5083.1699   1.5305   0.6140    1.0000e-02  1.3093e-02
# L1MCc       15400     9577.3159    4612.0000    15500.0000   3334.1419   1.6079   0.6852    5.2800e-02  6.3260e-02
# L1MC        29286     18076.9215   12687.0000   24115.0000   3474.6214   1.6200   0.6960    2.1000e-03  2.8989e-03
# L1PBb       2942      1776.5657    0.0000       5472.0000    1898.6955   1.6556   0.7274    2.2220e-01  2.4754e-01
# L1MB5       57514     33032.8782   23700.0000   43176.0000   5918.9017   1.7411   0.8000    1.0000e-04  1.5301e-04
# L1MD        38871     21965.6321   15781.0000   28841.0000   3953.4738   1.7696   0.8234    1.0000e-04  1.5301e-04
# L1MEi       24266     13396.8223   8334.0000    19043.0000   3289.4646   1.8113   0.8570    1.8000e-03  2.5121e-03
# L1MA10      23205     12515.1273   7245.0000    18958.0000   3583.0515   1.8541   0.8907    7.0000e-03  9.3579e-03
# L1MEd       117149    62267.2190   50026.0000   75408.0000   7783.7296   1.8814   0.9118    1.0000e-04  1.5301e-04
# L1M2b       2231      1133.1547    0.0000       3915.0000    1375.4398   1.9680   0.9767    1.7310e-01  1.9805e-01
# L1MC2       53765     26236.8984   16994.0000   36684.0000   5964.8030   2.0492   1.0350    1.0000e-04  1.5301e-04
# L1MEg       93127     44456.8026   33230.0000   56596.0000   7119.9720   2.0948   1.0668    1.0000e-04  1.5301e-04
# L1MD3       34054     15070.2539   9397.0000    21767.0000   3816.3994   2.2596   1.1761    1.0000e-04  1.5301e-04
# L1P5        1789      768.7211     125.0000     1742.0000    508.8441    2.3255   1.2176    4.5100e-02  5.5074e-02
# L1ME1       291527    125289.5791  104820.0000  146532.0000  12750.7199  2.3268   1.2184    1.0000e-04  1.5301e-04
# L1M7        22273     9044.3562    4811.0000    14105.0000   2855.8084   2.4625   1.3001    1.0000e-04  1.5301e-04
# L1MB8       159057    62522.8863   49437.0000   76970.0000   8433.8302   2.5440   1.3471    1.0000e-04  1.5301e-04
# L1MA1       68208     26744.9923   15444.0000   39973.0000   7479.5217   2.5503   1.3506    1.0000e-04  1.5301e-04
# L1M3        45989     17802.6652   11776.0000   24596.0000   3892.8428   2.5832   1.3691    1.0000e-04  1.5301e-04
# L1MB3       163454    60172.6153   47950.0000   73485.0000   7873.5689   2.7164   1.4417    1.0000e-04  1.5301e-04
# L1MB7       254467    93607.8424   76891.0000   111600.0000  10530.7543  2.7184   1.4428    1.0000e-04  1.5301e-04
# L1MEb       13298     4890.2589    1910.0000    8591.0000    2036.3521   2.7189   1.4430    3.0000e-04  4.4302e-04
# L1MEj       15675     5759.7071    2696.0000    9457.0000    2068.7830   2.7212   1.4442    1.0000e-04  1.5301e-04
# L1MEa       2937      1074.3283    0.0000       3012.0000    991.3342    2.7322   1.4501    5.4300e-02  6.4450e-02
# L1MB1       68980     24699.4078   15586.0000   35274.0000   6003.7833   2.7927   1.4817    1.0000e-04  1.5301e-04
# L1MA9       165319    58922.0749   44797.0000   74520.0000   9066.7106   2.8057   1.4884    1.0000e-04  1.5301e-04
# L1M8        14657     5217.1535    1993.0000    9183.0000    2210.0761   2.8090   1.4901    5.0000e-04  7.2989e-04
# L1MB4       86834     30829.3228   21831.0000   40902.0000   5799.3892   2.8165   1.4939    1.0000e-04  1.5301e-04
# L1MA7       82364     29120.8957   19894.0000   39762.0000   6028.5161   2.8283   1.4999    1.0000e-04  1.5301e-04
# L1MA8       113896    39349.6124   28362.0000   51686.0000   7108.2625   2.8944   1.5333    1.0000e-04  1.5301e-04
# L1MD2       116398    39519.7460   28688.0000   51378.0000   6926.6438   2.9453   1.5584    1.0000e-04  1.5301e-04
# L1MA6       73454     24613.0450   15311.0000   35361.0000   6141.1349   2.9843   1.5774    1.0000e-04  1.5301e-04
# L1MA5A      45336     14918.2790   7875.0000    23686.0000   4836.2109   3.0388   1.6035    1.0000e-04  1.5301e-04
# L1M4        199927    65542.5800   51764.0000   80139.0000   8604.5403   3.0503   1.6090    1.0000e-04  1.5301e-04
# L1PA17      68146     22318.2447   13059.0000   33082.0000   6138.8125   3.0533   1.6104    1.0000e-04  1.5301e-04
# L1MD1       99084     32051.4500   20720.0000   44941.0000   7318.4515   3.0913   1.6282    1.0000e-04  1.5301e-04
# L1PB2       50741     15447.3024   7834.0000    24581.0000   5157.7612   3.2846   1.7157    1.0000e-04  1.5301e-04
# L1MEc       227524    68622.8886   53427.0000   84608.0000   9470.8914   3.3155   1.7292    1.0000e-04  1.5301e-04
# L1MCb       22368     6696.0593    2438.0000    11889.0000   2906.2131   3.3401   1.7399    1.0000e-04  1.5301e-04
# L1MC1       196528    57896.3570   43631.0000   73455.0000   9093.8069   3.3944   1.7632    1.0000e-04  1.5301e-04
# L1PA16      272347    78702.9460   60711.0000   98164.0000   11299.8913  3.4604   1.7909    1.0000e-04  1.5301e-04
# L1M4a2      27025     7705.9251    3193.0000    13241.0000   3073.8887   3.5067   1.8101    1.0000e-04  1.5301e-04
# L1MCa       104979    29788.1711   19300.0000   41336.0000   6699.2516   3.5241   1.8173    1.0000e-04  1.5301e-04
# L1MEf       169482    46463.4663   33713.0000   60049.0000   7998.9348   3.6476   1.8669    1.0000e-04  1.5301e-04
# L1M6B       4272      1165.5242    222.0000     2748.0000    815.5726    3.6630   1.8730    4.4000e-03  5.9447e-03
# L1M3c       11669     3171.0844    429.0000     7045.0000    2055.6962   3.6790   1.8793    1.3000e-03  1.8344e-03
# L1MB2       119993    32416.3908   23071.0000   42813.0000   6023.1648   3.7015   1.8881    1.0000e-04  1.5301e-04
# L1PA12      34892     9316.0050    3686.0000    16356.0000   3881.0662   3.7451   1.9050    1.0000e-04  1.5301e-04
# L1PA15-16   32003     8352.3982    2524.0000    15751.0000   4061.0167   3.8313   1.9378    1.0000e-04  1.5301e-04
# L1M4b       67913     17439.2424   9509.0000    26586.0000   5233.7801   3.8941   1.9613    1.0000e-04  1.5301e-04
# L1M4a1      36492     9283.2070    4567.0000    14930.0000   3186.7112   3.9307   1.9748    1.0000e-04  1.5301e-04
# L1PA13      198506    50123.8487   35839.0000   66066.0000   9211.3518   3.9603   1.9856    1.0000e-04  1.5301e-04
# L1PA10      183469    45606.8706   31606.0000   61070.0000   9005.7514   4.0228   2.0082    1.0000e-04  1.5301e-04
# L1P3b       1782      438.9961     0.0000       1942.0000    761.9454    4.0523   2.0187    6.1800e-02  7.2006e-02
# L1PA11      123199    30358.8419   18629.0000   43572.0000   7630.4872   4.0580   2.0208    1.0000e-04  1.5301e-04
# L1PBa1      20066     4909.7975    492.0000     10915.0000   3220.6639   4.0863   2.0308    2.0000e-04  3.0238e-04
# L1HS        112095    27405.9085   14764.0000   41676.0000   8302.6499   4.0901   2.0321    1.0000e-04  1.5301e-04
# L1M1        233005    56356.8184   40370.0000   74186.0000   10364.8825  4.1344   2.0477    1.0000e-04  1.5301e-04
# L1MA2       202969    48665.9656   33031.0000   66242.0000   10138.9813  4.1706   2.0603    1.0000e-04  1.5301e-04
# L1PA8       204090    48905.7526   34414.0000   65371.0000   9392.3886   4.1731   2.0611    1.0000e-04  1.5301e-04
# L1MA4       198460    47452.0424   33689.0000   62655.0000   8771.8372   4.1823   2.0643    1.0000e-04  1.5301e-04
# L1P4        48186     11213.3661   6138.0000    17003.0000   3295.6679   4.2969   2.1033    1.0000e-04  1.5301e-04
# L1M2        168429    37997.7029   26264.0000   50855.0000   7458.7568   4.4325   2.1481    1.0000e-04  1.5301e-04
# L1M2a       15673     3504.9231    0.0000       8637.0000    2691.6545   4.4707   2.1605    3.0000e-04  4.4302e-04
# L1MEg1      9323      2080.2312    272.0000     4612.0000    1334.5447   4.4800   2.1635    1.0000e-04  1.5301e-04
# L1MDb       16058     3548.7086    698.0000     7473.0000    2089.1106   4.5240   2.1776    1.0000e-04  1.5301e-04
# L1PB4       145468    31297.3016   21077.0000   43045.0000   6691.6060   4.6478   2.2166    1.0000e-04  1.5301e-04
# L1PA14      97227     20763.7044   11724.0000   31344.0000   6005.1349   4.6824   2.2272    1.0000e-04  1.5301e-04
# L1P4d       4554      971.1062     0.0000       3862.0000    1375.7327   4.6857   2.2283    2.7500e-02  3.4579e-02
# L1MDa       140358    29444.0056   19059.0000   40795.0000   6630.1699   4.7668   2.2530    1.0000e-04  1.5301e-04
# L1M3a       12639     2639.8294    321.0000     5780.0000    1711.4046   4.7864   2.2589    1.0000e-04  1.5301e-04
# L1MA5       104890    21802.3242   12621.0000   32613.0000   6089.4106   4.8108   2.2663    1.0000e-04  1.5301e-04
# L1PA15      257963    53311.4894   37874.0000   70221.0000   9863.7190   4.8387   2.2746    1.0000e-04  1.5301e-04
# L1PB        32625     6321.2850    2506.0000    10990.0000   2574.4593   5.1605   2.3675    1.0000e-04  1.5301e-04
# L1PB3       98070     18939.3768   10397.0000   29020.0000   5725.8582   5.1779   2.3724    1.0000e-04  1.5301e-04
# L1M3de      10602     2007.0704    0.0000       5620.0000    1851.3785   5.2802   2.4006    9.0000e-04  1.2989e-03
# L1MA4A      209918    38933.9869   25200.0000   54259.0000   8918.9591   5.3915   2.4307    1.0000e-04  1.5301e-04
# L1PREC2     234604    42764.9799   29942.0000   57169.0000   8296.6058   5.4858   2.4557    1.0000e-04  1.5301e-04
# L1M3d       10108     1833.7442    0.0000       5088.0000    1655.9827   5.5098   2.4620    1.1000e-03  1.5697e-03
# L1PA8A      137370    24672.4262   13440.0000   37632.0000   7417.7806   5.5676   2.4770    1.0000e-04  1.5301e-04
# L1MA3       359778    63860.0669   45258.0000   84807.0000   11992.8887  5.6338   2.4941    1.0000e-04  1.5301e-04
# L1PB1       541497    94063.4807   71454.0000   118564.0000  14216.2009  5.7567   2.5252    1.0000e-04  1.5301e-04
# L1M3e       22034     3807.2043    520.0000     8483.0000    2445.8185   5.7862   2.5326    1.0000e-04  1.5301e-04
# L1M3f       23875     4085.6060    438.0000     9126.0000    2690.4178   5.8425   2.5466    1.0000e-04  1.5301e-04
# L1PA6       501498    80752.4451   58634.0000   104130.0000  13846.6654  6.2102   2.6347    1.0000e-04  1.5301e-04
# L1M4c       97911     15459.3278   8082.0000    24252.0000   4930.1910   6.3331   2.6629    1.0000e-04  1.5301e-04
# L1PA7       983370    150638.9242  121433.0000  182279.0000  18562.0796  6.5280   2.7066    1.0000e-04  1.5301e-04
# L1PBa       102104    15191.2358   6950.0000    25160.0000   5570.3913   6.7209   2.7486    1.0000e-04  1.5301e-04
# L1P3        126736    18477.1392   10319.0000   28195.0000   5479.0512   6.8588   2.7779    1.0000e-04  1.5301e-04
# L1M2a1      6789      907.8417     0.0000       3618.0000    1289.9555   7.4710   2.9013    2.9000e-03  3.9602e-03
# L1P4a       29204     3774.8243    56.0000      8983.0000    2765.4608   7.7347   2.9514    1.0000e-04  1.5301e-04
# L1PA5       1031995   128966.1546  101728.0000  158031.0000  16984.3828  8.0020   3.0004    1.0000e-04  1.5301e-04
# L1PA4       1176550   138908.8770  111145.0000  168647.0000  17526.2801  8.4699   3.0823    1.0000e-04  1.5301e-04
# L1P1        150247    17388.8839   9961.0000    25928.0000   4905.7285   8.6400   3.1110    1.0000e-04  1.5301e-04
# L1PA2       650142    74229.0051   52755.0000   97723.0000   13672.6615  8.7585   3.1307    1.0000e-04  1.5301e-04
# L1PA3       1388972   142594.5479  113847.0000  173067.0000  17931.3190  9.7406   3.2840    1.0000e-04  1.5301e-04
# L1P2        153419    15318.6351   7068.0000    25147.0000   5529.9891   10.0146  3.3240    1.0000e-04  1.5301e-04
# L1M2c       29808     2767.9002    0.0000       7352.0000    2342.1708   10.7656  3.4284    1.0000e-04  1.5301e-04

cut -f2-11 genes/HA.SKOHA.v2_ucsc_hg38_repeats_repeatmasker.classes.txt | column -t
# annotation      observed  expected      CI95low       CI95high      stddev      fold    l2fold   pvalue      qvalue
# RC              142       3103.7154     1514.0000     4990.0000     1061.7684   0.0461  -4.4404  1.0000e-04  1.7778e-04
# Unknown         1008      6579.2089     4900.0000     8340.0000     1054.2668   0.1533  -2.7052  1.0000e-04  1.7778e-04
# Simple_repeat   100049    300588.0690   288735.0000   313037.0000   7349.7936   0.3328  -1.5871  1.0000e-04  1.7778e-04
# Low_complexity  20316     51112.8182    47046.0000    55438.0000    2569.0668   0.3975  -1.3310  1.0000e-04  1.7778e-04
# scRNA           636       1270.1870     719.0000      1900.0000     359.0906    0.5011  -0.9968  2.6800e-02  3.8982e-02
# SINE            1929717   3653426.1744  3586962.0000  3718835.0000  39993.3050  0.5282  -0.9209  1.0000e-04  1.7778e-04
# LTR             1051051   1837552.2551  1767420.0000  1909051.0000  42930.0751  0.5720  -0.8060  1.0000e-04  1.7778e-04
# RNA             693       1046.5840     373.0000      1845.0000     447.6002    0.6625  -0.5941  2.2040e-01  2.5189e-01
# rRNA            1246      1863.1653     774.0000      4181.0000     1164.9739   0.6689  -0.5801  3.1070e-01  3.1070e-01
# Retroposon      30762     35413.6069    24730.0000    47180.0000    6775.1016   0.8687  -0.2031  2.5320e-01  2.7008e-01
# DNA             844388    927807.0756   892810.0000   963547.0000   21601.2585  0.9101  -0.1359  1.0000e-04  1.7778e-04
# srpRNA          3311      2571.5026     1363.0000     3930.0000     781.3649    1.2875  0.3645   1.6630e-01  2.0468e-01
# snRNA           4635      3186.9789     2314.0000     4136.0000     558.3423    1.4542  0.5402   7.5000e-03  1.2000e-02
# Satellite       37794     23200.3045    11861.0000    37074.0000    7754.7418   1.6290  0.7040   4.4300e-02  5.9067e-02
# tRNA            2170      712.9041      356.0000      1144.0000     242.2920    3.0410  1.6046   1.0000e-04  1.7778e-04
# LINE            15871041  5187465.4633  5063914.0000  5313541.0000  76009.1101  3.0595  1.6133   1.0000e-04  1.7778e-04

cut -f2-11 genes/HA.SKOHA.v2_ucsc_hg38_repeats_repeatmasker.families.txt | column -t
# annotation      observed  expected      CI95low       CI95high      stddev      fold    l2fold   pvalue      qvalue
# TcMar           0         303.2565      0.0000        821.0000      272.3934    0.0033  -8.2491  2.3440e-01  3.1914e-01
# hAT-Tag1        0         219.3515      0.0000        531.0000      166.9958    0.0045  -7.7837  1.4680e-01  2.2881e-01
# Merlin          0         173.7593      0.0000        1029.0000     360.0356    0.0057  -7.4492  6.9010e-01  8.1012e-01
# acro            0         86.2337       0.0000        674.0000      329.6268    0.0115  -6.4468  8.8910e-01  9.7982e-01
# TcMar-Pogo      0         31.8788       0.0000        161.0000      62.4230     0.0304  -5.0391  7.4730e-01  8.5860e-01
# Helitron        142       3108.2626     1480.0000     4949.0000     1066.6055   0.0460  -4.4425  1.0000e-04  2.1600e-04
# MULE-MuDR       286       4937.7116     1691.0000     9038.0000     2245.8702   0.0581  -4.1050  1.4000e-03  2.9077e-03
# PIF-Harbinger   0         13.2764       0.0000        61.0000       26.9456     0.0700  -3.8356  7.7160e-01  8.6805e-01
# DNA             267       2847.2232     1766.0000     4065.0000     696.7021    0.0941  -3.4098  1.0000e-04  2.1600e-04
# LTR             645       5224.9009     3051.0000     7713.0000     1403.5708   0.1236  -3.0161  1.0000e-04  2.1600e-04
# Gypsy           3351      25565.9320    20113.0000    31503.0000    3469.9722   0.1311  -2.9312  1.0000e-04  2.1600e-04
# Unknown         1008      6573.5833     4911.0000     8380.0000     1057.4072   0.1535  -2.7040  1.0000e-04  2.1600e-04
# hAT-Blackjack   4472      26831.0558    21294.0000    32920.0000    3539.6411   0.1667  -2.5846  1.0000e-04  2.1600e-04
# MIR             134333    800889.3745   778569.0000   822990.0000   13693.2854  0.1677  -2.5758  1.0000e-04  2.1600e-04
# RTE-X           6437      34188.6607    27654.0000    41134.0000    4100.1637   0.1883  -2.4089  1.0000e-04  2.1600e-04
# hAT             1800      9217.0463     7221.0000     11321.0000    1258.0061   0.1954  -2.3557  1.0000e-04  2.1600e-04
# 5S-Deu-L2       526       2666.1026     1705.0000     3761.0000     630.9867    0.1976  -2.3394  1.0000e-04  2.1600e-04
# tRNA-RTE        1457      7347.6436     5627.0000     9142.0000     1071.9819   0.1984  -2.3335  1.0000e-04  2.1600e-04
# L2              272442    963304.9118   926356.0000   1000355.0000  22554.2421  0.2828  -1.8220  1.0000e-04  2.1600e-04
# RTE-BovB        3709      13041.8966    9961.0000     16401.0000    1942.0011   0.2844  -1.8138  1.0000e-04  2.1600e-04
# TcMar-Tc2       4476      15386.1166    11455.0000    19703.0000    2505.4036   0.2910  -1.7811  1.0000e-04  2.1600e-04
# hAT-Tip100      19234     63831.4586    55468.0000    72590.0000    5241.5975   0.3013  -1.7306  1.0000e-04  2.1600e-04
# tRNA-Deu        194       627.0258      209.0000      1144.0000     285.4772    0.3105  -1.6873  4.2400e-02  7.1550e-02
# Penelope        276       872.2269      377.0000      1502.0000     347.1903    0.3172  -1.6565  1.6300e-02  3.1436e-02
# CR1             39040     117594.1801   106173.0000   129119.0000   6968.9839   0.3320  -1.5908  1.0000e-04  2.1600e-04
# Simple_repeat   100049    300579.5622   288961.0000   312997.0000   7357.1502   0.3329  -1.5870  1.0000e-04  2.1600e-04
# hAT-Charlie     145401    430137.1438   409777.0000   450728.0000   12387.4795  0.3380  -1.5648  1.0000e-04  2.1600e-04
# Low_complexity  20316     51085.4645    46971.0000    55462.0000    2578.2747   0.3977  -1.3303  1.0000e-04  2.1600e-04
# ERV1            260349    543084.2580   495840.0000   590208.0000   28802.0430  0.4794  -1.0607  1.0000e-04  2.1600e-04
# ERVL            186979    381411.8084   351830.0000   412559.0000   18538.3515  0.4902  -1.0285  1.0000e-04  2.1600e-04
# scRNA           636       1279.1916     718.0000      1910.0000     362.1325    0.4976  -1.0070  2.5800e-02  4.6440e-02
# Dong-R4         616       1100.0840     254.0000      2242.0000     611.5172    0.5604  -0.8356  2.3100e-01  3.1914e-01
# Alu             1792620   2839654.9322  2774498.0000  2904138.0000  39350.6859  0.6313  -0.6636  1.0000e-04  2.1600e-04
# ERVL-MaLR       515921    806926.3699   768591.0000   846263.0000   23543.6480  0.6394  -0.6453  1.0000e-04  2.1600e-04
# rRNA            1246      1868.6355     774.0000      4176.0000     1146.2476   0.6670  -0.5843  2.9460e-01  3.6996e-01
# RNA             693       1034.1650     373.0000      1818.0000     444.0118    0.6704  -0.5769  2.3640e-01  3.1914e-01
# tRNA            2773      3283.8497     2232.0000     4452.0000     674.2999    0.8445  -0.2439  2.3080e-01  3.1914e-01
# SVA             30762     35369.2956    24849.0000    47052.0000    6729.7597   0.8697  -0.2013  2.5370e-01  3.3414e-01
# hAT-Ac          3404      3406.1597     2364.0000     4595.0000     683.9765    0.9994  -0.0009  5.1880e-01  6.2256e-01
# Kolobok         0         0.0000        0.0000        0.0000        0.0000      1.0000  0.0000   1.0000e+00  1.0000e+00
# L1-Tx1          0         0.0000        0.0000        0.0000        0.0000      1.0000  0.0000   1.0000e+00  1.0000e+00
# I-Jockey        0         0.0000        0.0000        0.0000        0.0000      1.0000  0.0000   1.0000e+00  1.0000e+00
# Crypton         0         0.0000        0.0000        0.0000        0.0000      1.0000  0.0000   1.0000e+00  1.0000e+00
# TcMar-Tc1       0         0.0000        0.0000        0.0000        0.0000      1.0000  0.0000   1.0000e+00  1.0000e+00
# TcMar-Mariner   26561     22804.1065    17287.0000    29028.0000    3573.2841   1.1647  0.2200   1.4830e-01  2.2881e-01
# Satellite       17078     14465.5168    5814.0000     25317.0000    5991.9117   1.1806  0.2395   3.0260e-01  3.7137e-01
# srpRNA          3311      2562.7523     1364.0000     3919.0000     777.7199    1.2919  0.3694   1.6800e-01  2.5200e-01
# ERVK            78010     60255.2320    44919.0000    76982.0000    9794.9049   1.2947  0.3726   4.0300e-02  7.0200e-02
# telo            1299      915.0291      0.0000        2974.0000     1031.0102   1.4192  0.5050   2.8880e-01  3.6996e-01
# snRNA           4635      3190.1149     2309.0000     4129.0000     550.9659    1.4528  0.5388   7.0000e-03  1.4000e-02
# PiggyBac        6772      4495.2267     2186.0000     7321.0000     1584.4957   1.5064  0.5911   8.4100e-02  1.3762e-01
# TcMar-Tigger    631248    337483.9551   311661.0000   363963.0000   15911.1935  1.8705  0.9034   1.0000e-04  2.1600e-04
# centr           19419     7567.4400     1505.0000     16227.0000    4641.5745   2.5659  1.3595   2.1100e-02  3.9290e-02
# L1              15548538  4058979.5923  3933380.0000  4186361.0000  77041.0795  3.8307  1.9376   1.0000e-04  2.1600e-04
```



### L1Base

Downloaded the annotation from the [website](http://l1base.charite.de/l1base.php) and copied it across.


#### Explore txt file and convert into bed

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/l1base

tail -n +2 FLI_L1.bed.txt | wc -l # 146 full-length intact L1s
tail -n +2 ORF2_L1.bed.txt | wc -l # 107 ORF2 intact and ORF1 disrupted L1s
tail -n +2 FLnI_L1.bed.txt | wc -l # 13418 full-length non-intact L1s

cat \
<(tail -n +2 FLI_L1.bed.txt | awk -v OFS="\t" '{print $1, $2, $3, "FLI", $5, $6}') \
<(tail -n +2 ORF2_L1.bed.txt | awk -v OFS="\t" '{print $1, $2, $3, "ORF2", $5, $6}') \
<(tail -n +2 FLnI_L1.bed.txt | awk -v OFS="\t" '{print $1, $2, $3, "FLnI", $5, $6}') > l1base.bed

wc -l l1base.bed # 13671

cat \
<(tail -n +2 FLI_L1.bed.txt | awk -v OFS="\t" '{print $1, $2, $3, "FLI"}') \
<(tail -n +2 ORF2_L1.bed.txt | awk -v OFS="\t" '{print $1, $2, $3, "ORF2"}') \
<(tail -n +2 FLnI_L1.bed.txt | awk -v OFS="\t" '{print $1, $2, $3, "FLnI"}') > l1base.gat.bed

wc -l l1base.gat.bed # 13671

cat \
<(tail -n +2 FLI_L1.bed.txt | awk -v OFS="\t" '{print $1, $2, $3, "FLI", $5, $6}') \
<(tail -n +2 ORF2_L1.bed.txt | awk -v OFS="\t" '{print $1, $2, $3, "ORF2", $5, $6}') \
<(tail -n +2 FLnI_L1.bed.txt | awk -v OFS="\t" '{print $1, $2, $3, "FLnI", $5, $6}') | \
cut -f4 | sort | uniq -c | awk -v OFS="\t" '{print $1, $2}' > l1base.txt

cat l1base.txt
# 146	FLI
# 13418	FLnI
# 107	ORF2
```


#### overlap with ripseq data

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/custom

# SETDB1-KO
grep "^chr" HA.SKOHA.v2.bed | wc -l # 10960 SETDB1-KO peaks in core chr
bedtools intersect -a <(grep "^chr" HA.SKOHA.v2.bed) -b ../l1base/l1base.bed -wa -u | wc -l # 3181 SETDB1-KO peaks in core chr overlap with l1base L1s
bedtools intersect -a <(grep "^chr" HA.SKOHA.v2.bed) -b ../l1base/l1base.bed -wo | bedtools groupby -i - -g 1,2,3 -c 7 -o count,collapse | head
bedtools intersect -a <(grep "^chr" HA.SKOHA.v2.bed) -b ../l1base/l1base.bed -wo | cut -f7 | sort | uniq -c | wc -l # 3 of 3 kinds of l1base L1s overlap
bedtools intersect -a <(grep "^chr" HA.SKOHA.v2.bed) -b ../l1base/l1base.bed -wo | cut -f7 | sort | uniq -c | awk -v OFS="\t" '{print $1, $2}' > HA.SKOHA.v2.l1base.txt
cat HA.SKOHA.v2.l1base.txt
# 28	FLI
# 3532	FLnI
# 33	ORF2


# WT
grep "^chr" WTHA.v2.bed | wc -l # 8224 WT peaks in core chr
bedtools intersect -a <(grep "^chr" WTHA.v2.bed | cut -f1-3) -b ../l1base/l1base.bed -wa -u | wc -l # 2685 WT peaks in core chr overlap with l1base L1s
bedtools intersect -a <(grep "^chr" WTHA.v2.bed | cut -f1-3) -b ../l1base/l1base.bed -wo | bedtools groupby -i - -g 1,2,3 -c 7 -o count,collapse | head
bedtools intersect -a <(grep "^chr" WTHA.v2.bed | cut -f1-3) -b ../l1base/l1base.bed -wo | cut -f7 | sort | uniq -c | wc -l # 3 out of 3 kinds of l1base L1s overlap
bedtools intersect -a <(grep "^chr" WTHA.v2.bed | cut -f1-3) -b ../l1base/l1base.bed -wo | cut -f7 | sort | uniq -c | awk -v OFS="\t" '{print $1, $2}' > WTHA.v2.l1base.txt
cat WTHA.v2.l1base.txt
# 24	FLI
# 2974	FLnI
# 21	ORF2
```


#### visualisation

```r
#module load R-core/3.6.0-foss-2017a
#export R_LIBS_USER=/projects/qbio/bifo/R/3.6.0
#cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/custom
#R

library(data.table)
library(ggplot2)

# Enlarge the view width when printing tables
options(width = 250)


# all l1base
data <- fread('cat ../l1base/l1base.bed | cut -f4 | sort | uniq -c')

### pie chart
ggplot(data = data, aes(x = "", y = V1, fill = V2)) +
geom_bar(stat = "identity", color = "black") +
coord_polar("y", direction = -1) +
theme_void() +
theme(legend.title=element_blank()) +
scale_fill_brewer(palette="Dark2")

ggsave("../figures/ripseq_l1base_pie.pdf", width = 10, height = 10, units= 'cm', useDingbats = FALSE)

### barplot
ggplot(data = data, aes(x = V1, y = factor(data$V2, levels = data[order(-V2)]$V2))) +
geom_bar(stat="identity") +
xlab("Count") +
ylab("L1Base") +
theme_bw() +
theme(axis.title = element_text(size=16), axis.text = element_text(size=12, color = "black"), axis.text.y = element_text(angle = 0, vjust = 0.5, hjust=1))

ggsave("../figures/ripseq_l1base_bar.pdf", width = 10, height = 5, units= 'cm', useDingbats = FALSE)



# l1base overlapping with SETDB1-KO peaks
data <- fread('HA.SKOHA.v2.l1base.txt')

### pie chart
ggplot(data = data, aes(x = "", y = V1, fill = V2)) +
geom_bar(stat = "identity", color = "black") +
coord_polar("y", direction = -1) +
theme_void() +
theme(legend.title=element_blank()) +
scale_fill_brewer(palette="Dark2")

ggsave("../figures/ripseq_HA.SKOHA.v2.l1base_pie.pdf", width = 10, height = 10, units= 'cm', useDingbats = FALSE)

### barplot
ggplot(data = data, aes(x = V1, y = factor(data$V2, levels = data[order(-V2)]$V2))) +
geom_bar(stat="identity") +
xlab("Count") +
ylab("L1Base") +
theme_bw() +
theme(axis.title = element_text(size=16), axis.text = element_text(size=12, color = "black"), axis.text.y = element_text(angle = 0, vjust = 0.5, hjust=1))

ggsave("../figures/ripseq_HA.SKOHA.v2.l1base_bar.pdf", width = 10, height = 5, units= 'cm', useDingbats = FALSE)



# l1base overlapping with WT peaks
data <- fread('WTHA.v2.l1base.txt')

### pie chart
ggplot(data = data, aes(x = "", y = V1, fill = V2)) +
geom_bar(stat = "identity", color = "black") +
coord_polar("y", direction = -1) +
theme_void() +
theme(legend.title=element_blank()) +
scale_fill_brewer(palette="Dark2")

ggsave("../figures/ripseq_WTHA.v2.l1base_pie.pdf", width = 10, height = 10, units= 'cm', useDingbats = FALSE)

### barplot
ggplot(data = data, aes(x = V1, y = factor(data$V2, levels = data[order(-V2)]$V2))) +
geom_bar(stat="identity") +
xlab("Count") +
ylab("L1Base") +
theme_bw() +
theme(axis.title = element_text(size=16), axis.text = element_text(size=12, color = "black"), axis.text.y = element_text(angle = 0, vjust = 0.5, hjust=1))

ggsave("../figures/ripseq_WTHA.v2.l1base_bar.pdf", width = 10, height = 5, units= 'cm', useDingbats = FALSE)
```


#### gat

[Documentation](https://gat.readthedocs.io/en/latest/introduction.html)

Copy the following files to `ukc02zt7gvmd6n`:

Segments:
  - /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/custom/HA.SKOHA.v2.bed
  - /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/custom/WTHA.v2.bed

Annotations:
  - /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/l1base/l1base.gat.bed

Workspaces:
  - /projects/qbio/bifo/users/sergio/reference/blacklist/hg38.whitelist.v2.bed
  - /projects/qbio/bifo/users/sergio/repository/20201122_hush/annotation/gencode.v35.annotation.gene_type.intronless.bed

Run gat.py:

```bash
cd /Users/kzqv978/Desktop/gat
ls -1
# HA.SKOHA.v2.bed
# WTHA.v2.bed
# gencode.v35.annotation.gene_type.intronless.bed
# hg38.whitelist.v2.bed
# l1base.gat.bed

cut -f1-3 gencode.v35.annotation.gene_type.intronless.bed > gencode.v35.annotation.gene_type.intronless.cut.bed # just chr,start,end otherwise gat-run.py fails

mkdir whitelist
mkdir genes

for seg in HA.SKOHA.v2.bed WTHA.v2.bed
do
  bname1=${seg%.bed}
  ann=l1base.gat.bed
  bname2=${ann%.gat.bed}
  nohup /Users/kzqv978/Library/Python/2.7/bin/gat-run.py -s $seg -a $ann -w hg38.whitelist.v2.bed --ignore-segment-tracks -n 10000 -L whitelist/${bname1}_${bname2}.log > whitelist/${bname1}_${bname2}.txt &
  nohup /Users/kzqv978/Library/Python/2.7/bin/gat-run.py -s $seg -a $ann -w gencode.v35.annotation.gene_type.intronless.cut.bed --ignore-segment-tracks -n 10000 -L genes/${bname1}_${bname2}.log > genes/${bname1}_${bname2}.txt &
done

tail whitelist/*.log
tail genes/*.log

cut -f2-11 whitelist/HA.SKOHA.v2_l1base.txt | column -t
# annotation  observed  expected      CI95low       CI95high      stddev      fold    l2fold  pvalue      qvalue
# FLI         31355     11707.0862    3066.0000     22900.0000    6114.3066   2.6781  1.4212  4.5000e-03  4.5000e-03
# ORF2        49097     8785.1017     1522.0000     18550.0000    5232.0522   5.5881  2.4824  1.0000e-04  1.5000e-04
# FLnI        7868000   1241948.2217  1138717.0000  1346156.0000  62953.7146  6.3352  2.6634  1.0000e-04  1.5000e-04

cut -f2-11 whitelist/WTHA.v2_l1base.txt | column -t
# annotation  observed  expected     CI95low      CI95high      stddev      fold    l2fold  pvalue      qvalue
# FLI         26354     8949.2239    1648.0000    18756.0000    5312.3264   2.9446  1.5581  4.9000e-03  4.9000e-03
# ORF2        33353     6714.0329    614.0000     15588.0000    4590.3326   4.9671  2.3124  1.0000e-04  1.5000e-04
# FLnI        6554270   946353.5721  855803.0000  1040933.0000  55811.7923  6.9258  2.7920  1.0000e-04  1.5000e-04

cut -f2-11 genes/HA.SKOHA.v2_l1base.txt | column -t
# annotation  observed  expected     CI95low      CI95high     stddev      fold    l2fold  pvalue      qvalue
# FLI         33586     12094.4884   3156.0000    23801.0000   6361.2997   2.7768  1.4734  3.1000e-03  3.1000e-03
# ORF2        23286     5490.5990    0.0000       13517.0000   4194.4748   4.2405  2.0842  9.0000e-04  1.3500e-03
# FLnI        5572805   871133.8722  783136.0000  962462.0000  54024.5401  6.3972  2.6774  1.0000e-04  3.0000e-04

cut -f2-11 genes/WTHA.v2_l1base.txt | column -t
# annotation  observed  expected     CI95low      CI95high     stddev      fold    l2fold  pvalue      qvalue
# FLI         27440     9279.4050    1518.0000    19767.0000   5606.7216   2.9569  1.5641  4.9000e-03  7.3500e-03
# ORF2        13545     4295.6124    0.0000       11656.0000   3807.9335   3.1527  1.6566  2.5500e-02  2.5500e-02
# FLnI        4809688   665961.6906  589414.0000  744087.0000  47162.0234  7.2222  2.8524  1.0000e-04  3.0000e-04
```



### genes

Gene types and intronless y/n available at `/projects/qbio/bifo/users/sergio/repository/20201122_hush/annotation/gencode.v35.annotation.gene_type.intronless.bed`


#### overlap with ripseq data

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/custom

genes=/projects/qbio/bifo/users/sergio/repository/20201122_hush/annotation/gencode.v35.annotation.gene_type.intronless.bed
cut -f7 $genes | sort | uniq -c | wc -l # 40 types of genes


# SETDB1-KO
grep "^chr" HA.SKOHA.v2.bed | wc -l # 10960 SETDB1-KO peaks in core chr
bedtools intersect -a <(grep "^chr" HA.SKOHA.v2.bed) -b $genes -wa -u | wc -l # 8478 SETDB1-KO peaks in core chr overlap with genes
bedtools intersect -a <(grep "^chr" HA.SKOHA.v2.bed) -b $genes -wo | bedtools groupby -i - -g 1,2,3 -c 10 -o count,collapse | head
bedtools intersect -a <(grep "^chr" HA.SKOHA.v2.bed) -b $genes -wo | cut -f10 | sort | uniq -c | wc -l # 19 of 40 types of genes overlap
bedtools intersect -a <(grep "^chr" HA.SKOHA.v2.bed) -b $genes -wo | cut -f10 | sort | uniq -c > HA.SKOHA.v2.genes.txt
cat HA.SKOHA.v2.genes.txt
   #    1 IG_C_pseudogene
   # 2115 lncRNA
   #    5 miRNA
   #   26 misc_RNA
   #    1 polymorphic_pseudogene
   #  302 processed_pseudogene
   # 7648 protein_coding
   #    1 pseudogene
   #    7 rRNA_pseudogene
   #    7 snoRNA
   #   33 snRNA
   #    1 sRNA
   #   35 TEC
   #   82 transcribed_processed_pseudogene
   #   23 transcribed_unitary_pseudogene
   #  256 transcribed_unprocessed_pseudogene
   #    1 translated_unprocessed_pseudogene
   #    4 unitary_pseudogene
   #  114 unprocessed_pseudogene


# WT
grep "^chr" WTHA.v2.bed | wc -l # 8224 WT peaks in core chr
bedtools intersect -a <(grep "^chr" WTHA.v2.bed | cut -f1-3) -b $genes -wa -u | wc -l # 6317 WT peaks in core chr overlap with genes
bedtools intersect -a <(grep "^chr" WTHA.v2.bed | cut -f1-3) -b $genes -wo | bedtools groupby -i - -g 1,2,3 -c 10 -o count,collapse | head
bedtools intersect -a <(grep "^chr" WTHA.v2.bed | cut -f1-3) -b $genes -wo | cut -f10 | sort | uniq -c | wc -l # 17 out of 40 types of genes overlap
bedtools intersect -a <(grep "^chr" WTHA.v2.bed | cut -f1-3) -b $genes -wo | cut -f10 | sort | uniq -c > WTHA.v2.genes.txt
cat WTHA.v2.genes.txt
   # 1647 lncRNA
   #    4 miRNA
   #   18 misc_RNA
   #    1 polymorphic_pseudogene
   #  229 processed_pseudogene
   # 5321 protein_coding
   #    4 rRNA_pseudogene
   #    1 scaRNA
   #    3 snoRNA
   #   28 snRNA
   #    1 sRNA
   #   24 TEC
   #   72 transcribed_processed_pseudogene
   #   17 transcribed_unitary_pseudogene
   #  199 transcribed_unprocessed_pseudogene
   #    2 unitary_pseudogene
   #   74 unprocessed_pseudogene
```


#### visualisation

```r
#module load R-core/3.6.0-foss-2017a
#export R_LIBS_USER=/projects/qbio/bifo/R/3.6.0
#cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/custom
#R

library(data.table)
library(ggplot2)

# Enlarge the view width when printing tables
options(width = 250)


# all genes
data <- fread('cat /projects/qbio/bifo/users/sergio/repository/20201122_hush/annotation/gencode.v35.annotation.gene_type.intronless.bed | cut -f7 | sort | uniq -c')

### barplot
ggplot(data = data, aes(x = V1, y = factor(data$V2, levels = data[order(-V2)]$V2))) +
geom_bar(stat="identity") +
xlab("Count") +
ylab("Type of gene") +
theme_bw() +
theme(axis.title = element_text(size=16), axis.text = element_text(size=10, color = "black"), axis.text.y = element_text(angle = 0, vjust = 0.5, hjust=1))

ggsave("../figures/ripseq_genes_bar.pdf", width = 15, height = 20, units= 'cm', useDingbats = FALSE)



# genes overlapping with SETDB1-KO peaks
data <- fread('HA.SKOHA.v2.genes.txt')

### barplot
ggplot(data = data, aes(x = V1, y = factor(data$V2, levels = data[order(-V2)]$V2))) +
geom_bar(stat="identity") +
xlab("Count") +
ylab("Type of gene") +
theme_bw() +
theme(axis.title = element_text(size=16), axis.text = element_text(size=10, color = "black"), axis.text.y = element_text(angle = 0, vjust = 0.5, hjust=1))

ggsave("../figures/ripseq_HA.SKOHA.v2.genes_bar.pdf", width = 15, height = 12, units= 'cm', useDingbats = FALSE)



# genes overlapping with WT peaks
data <- fread('WTHA.v2.genes.txt')

### barplot
ggplot(data = data, aes(x = V1, y = factor(data$V2, levels = data[order(-V2)]$V2))) +
geom_bar(stat="identity") +
xlab("Count") +
ylab("Type of gene") +
theme_bw() +
theme(axis.title = element_text(size=16), axis.text = element_text(size=10, color = "black"), axis.text.y = element_text(angle = 0, vjust = 0.5, hjust=1))

ggsave("../figures/ripseq_WTHA.v2.genes_bar.pdf", width = 15, height = 12, units= 'cm', useDingbats = FALSE)
```


#### overlap with ripseq data - exclude genes that have an PPHLN peak overlapping L1 elements

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/custom

genes=/projects/qbio/bifo/users/sergio/repository/20201122_hush/annotation/gencode.v35.annotation.gene_type.intronless.bed
cut -f7 $genes | sort | uniq -c | wc -l # 40 types of genes

# SETDB1-KO
bedtools intersect \
-a <(bedtools intersect -a $genes -b <(grep "^chr" HA.SKOHA.v2.bed) -wa -u) \
-b <(bedtools intersect -a <(grep "^chr" HA.SKOHA.v2.bed) -b <(awk -v OFS="\t" '{if ($7=="L1") print $0}' ../repeatmasker/ucsc_hg38_repeats_repeatmasker.bed) -wa -u) \
-v | cut -f7 | sort | uniq -c | awk -v OFS="\t" '{print $1, $2}' > gencode.v35.annotation.gene_type.intronless_HA.SKOHA.v2_excluding_HA.SKOHA.v2_ucsc_hg38_repeats_repeatmasker_L1.txt
cat gencode.v35.annotation.gene_type.intronless_HA.SKOHA.v2_excluding_HA.SKOHA.v2_ucsc_hg38_repeats_repeatmasker_L1.txt
# 173	lncRNA
# 1	miRNA
# 2	misc_RNA
# 126	processed_pseudogene
# 759	protein_coding
# 2	snoRNA
# 4	snRNA
# 10	TEC
# 18	transcribed_processed_pseudogene
# 5	transcribed_unitary_pseudogene
# 28	transcribed_unprocessed_pseudogene
# 1	translated_unprocessed_pseudogene
# 1	unitary_pseudogene
# 30	unprocessed_pseudogene

bedtools intersect \
-a <(bedtools intersect -a $genes -b <(grep "^chr" HA.SKOHA.v2.bed) -wa -u) \
-b <(bedtools intersect -a <(grep "^chr" HA.SKOHA.v2.bed) -b <(awk -v OFS="\t" '{if ($7=="L1") print $0}' ../repeatmasker/ucsc_hg38_repeats_repeatmasker.bed) -wa -u) \
-v > gencode.v35.annotation.gene_type.intronless_HA.SKOHA.v2_excluding_HA.SKOHA.v2_ucsc_hg38_repeats_repeatmasker_L1.bed
wc -l gencode.v35.annotation.gene_type.intronless_HA.SKOHA.v2_excluding_HA.SKOHA.v2_ucsc_hg38_repeats_repeatmasker_L1.bed # 1160


# WT
bedtools intersect \
-a <(bedtools intersect -a $genes -b <(grep "^chr" WTHA.v2.bed) -wa -u) \
-b <(bedtools intersect -a <(grep "^chr" WTHA.v2.bed) -b <(awk -v OFS="\t" '{if ($7=="L1") print $0}' ../repeatmasker/ucsc_hg38_repeats_repeatmasker.bed) -wa -u) \
-v | cut -f7 | sort | uniq -c | awk -v OFS="\t" '{print $1, $2}' > gencode.v35.annotation.gene_type.intronless_WTHA.v2_excluding_WTHA.v2_ucsc_hg38_repeats_repeatmasker_L1.txt
cat gencode.v35.annotation.gene_type.intronless_WTHA.v2_excluding_WTHA.v2_ucsc_hg38_repeats_repeatmasker_L1.txt
# 84	lncRNA
# 2	miRNA
# 87	processed_pseudogene
# 419	protein_coding
# 1	snoRNA
# 4	snRNA
# 2	TEC
# 13	transcribed_processed_pseudogene
# 1	transcribed_unitary_pseudogene
# 6	transcribed_unprocessed_pseudogene
# 1	unitary_pseudogene
# 9	unprocessed_pseudogene

bedtools intersect \
-a <(bedtools intersect -a $genes -b <(grep "^chr" WTHA.v2.bed) -wa -u) \
-b <(bedtools intersect -a <(grep "^chr" WTHA.v2.bed) -b <(awk -v OFS="\t" '{if ($7=="L1") print $0}' ../repeatmasker/ucsc_hg38_repeats_repeatmasker.bed) -wa -u) \
-v > gencode.v35.annotation.gene_type.intronless_WTHA.v2_excluding_WTHA.v2_ucsc_hg38_repeats_repeatmasker_L1.bed
wc -l gencode.v35.annotation.gene_type.intronless_WTHA.v2_excluding_WTHA.v2_ucsc_hg38_repeats_repeatmasker_L1.bed # 629
```



### Gene info (incl. L1 overlap) + RIP-seq WT + RIP-seq SETDB1 KO 1 + RIP-seq SETDB1 KO 2

Add a column to annotation with genes that overlap with L1:

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/annotation

bedtools intersect \
-a gencode.v35.annotation.gene_type.intronless.bed \
-b <(awk -v OFS="\t" '{if ($7=="L1") print $0}' ../ripseq/repeatmasker/ucsc_hg38_repeats_repeatmasker.bed) \
-wao | \
bedtools groupby -i - -g 1,2,3,4,5,6,7,8 -c 9 -o collapse | \
awk -v OFS="\t" '{if ($9~"^chr") {$9="y"} print $0}' | \
awk -v OFS="\t" '{if ($9==".") {$9="n"} print $0}' > gencode.v35.annotation.gene_type.intronless.L1.bed
```

Create table:

```r
#module load R-core/3.6.0-foss-2017a
#export R_LIBS_USER=/projects/qbio/bifo/R/3.6.0
#cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/table
#R

library(data.table)
library(GenomicFeatures)
library(edgeR)

# Enlarge the view width when printing tables
options(width = 250)

##############
# genes info #
##############
genes <- fread("/projects/qbio/bifo/users/sergio/repository/20201122_hush/annotation/gencode.v35.annotation.gene_type.intronless.L1.bed")
setnames(genes, c("chr", "start", "end", "gene_name", "gene_id", "strand", "gene_type", "intronless", "L1"))


##############
# gene sizes #
##############
txdb <- makeTxDbFromGFF("/projects/qbio/bifo/users/sergio/reference/gencode/release35_GRCh38.p13/gtf/gencode.v35.annotation.gtf", format="gtf")
genes_length <- data.table(data.frame(genes(txdb)))[, c("gene_id", "width")]
genes_length[, length := width]
genes_length[, width := NULL]

genes <- merge(genes, genes_length, by = "gene_id")


##########
# ripseq #
##########
ripseq <- fread("tableCat.py -i /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/multicov/*.dedup.txt -r .dedup.txt")
setnames(ripseq, c("gene_id", "count", "file_id"))
ripseq_dcast <- dcast(ripseq, gene_id ~ file_id, value.var = "count")

# Define group
group <- factor(c('SETDB1KOHA', 'SETDB1KOHA', 'SETDB1KOHA', 'SETDB1KOHA', 'SETDB1KOempty', 'SETDB1KOempty', 'WTHA', 'WTHA', 'WTempty', 'WTempty', 'SETDB1KOempty', 'SETDB1KOempty'))

# Check that gene order is similar
sum(ripseq_dcast$gene_id == genes$gene_id)

# Define DGEList object
y <- DGEList(counts = ripseq_dcast[,c(-1)], group = group, genes = genes)

# Filtering
isexpr <- filterByExpr(y, min.count = 5)
table(isexpr)
#FALSE  TRUE
#21520 39136

y <- y[isexpr, , keep.lib.sizes=FALSE]
dim(y)
#39136    12

# Normalization
y <- calcNormFactors(y)
y$samples

# Design matrix
design <- model.matrix(~ 0 + as.vector(y$samples$group))
colnames(design) <- c("SETDB1KOempty", "SETDB1KOHA", "WTempty", "WTHA")

# Dispersion
y <- estimateDisp(y, design, robust=TRUE)
y$common.dispersion

# Fit genewise glms
fit <- glmFit(y, design)

# Differential expression
my.contrasts <- makeContrasts(SETDB1KOHAvsSETDB1KOempty = SETDB1KOHA - SETDB1KOempty, WTHAvsWTempty = WTHA - WTempty, levels = design)

# Likelihood ratio tests
## SETDB1KOHAvsSETDB1KOempty
lrt_SETDB1KOHAvsSETDB1KOempty <- glmLRT(fit, contrast=my.contrasts[,"SETDB1KOHAvsSETDB1KOempty"])
table_SETDB1KOHAvsSETDB1KOempty <- data.table(data.frame(topTags(lrt_SETDB1KOHAvsSETDB1KOempty, n=Inf)))[, c("gene_id", "chr", "start", "end", "gene_name", "strand", "gene_type", "intronless", "L1", "length", "logFC", "FDR")]
## WTHAvsWTempty
lrt_WTHAvsWTempty <- glmLRT(fit, contrast=my.contrasts[,"WTHAvsWTempty"])
table_WTHAvsWTempty <- data.table(data.frame(topTags(lrt_WTHAvsWTempty, n=Inf)))[, c("gene_id", "chr", "start", "end", "gene_name", "strand", "gene_type", "intronless", "L1", "length", "logFC", "FDR")]

# Combine logFC and FDR and add RPKM
table_ripseq_tmp <- merge(table_SETDB1KOHAvsSETDB1KOempty, table_WTHAvsWTempty, by = c("gene_id", "chr", "start", "end", "gene_name", "strand", "gene_type", "intronless", "L1", "length"), suffixes = c("_SETDB1KOHAvsSETDB1KOempty", "_WTHAvsWTempty"))
table_ripseq <- cbind(table_ripseq_tmp, rpkm(y))
setnames(table_ripseq, c("gene_id", "chr", "start", "end", "gene_name", "strand", "gene_type", "intronless", "L1", "length", "logFC_SETDB1KOHAvsSETDB1KOempty", "FDR_SETDB1KOHAvsSETDB1KOempty", "logFC_WTHAvsWTempty", "FDR_WTHAvsWTempty", "RPKM_HA1", "RPKM_HA2", "RPKM_SKOHA1", "RPKM_SKOHA2", "RPKM_SKOempty1", "RPKM_SKOempty2", "RPKM_WTHA1", "RPKM_WTHA2", "RPKM_WTempty1", "RPKM_WTempty2", "RPKM_empty1", "RPKM_empty2"))
table_ripseq <- table_ripseq[, c("gene_id", "chr", "start", "end", "gene_name", "strand", "gene_type", "intronless", "L1", "length", "RPKM_HA1", "RPKM_HA2", "RPKM_SKOHA1", "RPKM_SKOHA2", "RPKM_empty1", "RPKM_empty2", "RPKM_SKOempty1", "RPKM_SKOempty2", "logFC_SETDB1KOHAvsSETDB1KOempty", "FDR_SETDB1KOHAvsSETDB1KOempty", "RPKM_WTHA1", "RPKM_WTHA2", "RPKM_WTempty1", "RPKM_WTempty2", "logFC_WTHAvsWTempty", "FDR_WTHAvsWTempty")]

# merge and write table
write.table(table_ripseq, "20210717_table.txt", sep="\t", quote = FALSE, row.names = FALSE, col.names = TRUE)
```



### advanced visualisation

- deeptools https://deeptools.readthedocs.io/en/develop/index.html
- EnrichedHeatmap https://bioconductor.org/packages/release/bioc/html/EnrichedHeatmap.html
- ngs.plot https://github.com/shenlab-sinai/ngsplot
- profileplyr https://bioconductor.org/packages/release/bioc/html/profileplyr.html
- metagene https://bioconductor.org/packages/release/bioc/html/metagene.html
- metagene2 https://bioconductor.org/packages/release/bioc/html/metagene2.html
- heatmaps https://bioconductor.org/packages/release/bioc/html/heatmaps.html


#### deeptools

Merge bam files, index and convert to bigwig using bamCoverage:

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/hisat2

for id in empty HA SKOempty SKOHA WTempty WTHA
do
  bams=`echo $id*.dedup.bam`
#  echo $id, $bams
  sbatch -J $id -o $id.log --mem 16G -t 1-0 --wrap "samtools merge -@ 20 $id.dedup.bam $bams && \
  samtools index $id.dedup.bam && \
  bamCoverage -b $id.dedup.bam -o $id.dedup.bw -of bigwig --binSize 1 -p 20 --normalizeUsing CPM"
done

# empty SKOempty
sbatch -J empty.SKOempty -o empty.SKOempty.log --mem 16G -t 1-0 --wrap "samtools merge -@ 20 empty.SKOempty.dedup.bam empty.dedup.bam SKOempty.dedup.bam && \
samtools index empty.SKOempty.dedup.bam && \
bamCoverage -b empty.SKOempty.dedup.bam -o empty.SKOempty.dedup.bw -of bigwig --binSize 1 -p 20 --normalizeUsing CPM"

# HA SKOHA
sbatch -J HA.SKOHA -o HA.SKOHA.log --mem 16G -t 1-0 --wrap "samtools merge -@ 20 HA.SKOHA.dedup.bam HA.dedup.bam SKOHA.dedup.bam && \
samtools index HA.SKOHA.dedup.bam && \
bamCoverage -b HA.SKOHA.dedup.bam -o HA.SKOHA.dedup.bw -of bigwig --binSize 1 -p 20 --normalizeUsing CPM"
```

Calculate log2 ratios using bamCompare:

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/hisat2

# --operation log2
## HA.SKOHA vs empty.SKOempty
sbatch -J HA.SKOHA.empty.SKOempty.log2 -o HA.SKOHA.empty.SKOempty.log2.log --mem 16G -t 1-0 --wrap "bamCompare -b1 HA.SKOHA.dedup.bam -b2 empty.SKOempty.dedup.bam -o HA.SKOHA.empty.SKOempty.dedup.log2.bw -of bigwig --operation log2 --binSize 1 -p 20"

## WTHA vs WTempty
sbatch -J WTHA.WTempty.log2 -o WTHA.WTempty.log2.log --mem 16G -t 1-0 --wrap "bamCompare -b1 WTHA.dedup.bam -b2 WTempty.dedup.bam -o WTHA.WTempty.dedup.log2.bw -of bigwig --operation log2 --binSize 1 -p 20"


# --operation ratio
## HA.SKOHA vs empty.SKOempty
sbatch -J HA.SKOHA.empty.SKOempty.ratio -o HA.SKOHA.empty.SKOempty.ratio.log --mem 16G -t 1-0 --wrap "bamCompare -b1 HA.SKOHA.dedup.bam -b2 empty.SKOempty.dedup.bam -o HA.SKOHA.empty.SKOempty.dedup.ratio.bw -of bigwig --operation ratio --binSize 1 -p 20"

## WTHA vs WTempty
sbatch -J WTHA.WTempty.ratio -o WTHA.WTempty.ratio.log --mem 16G -t 1-0 --wrap "bamCompare -b1 WTHA.dedup.bam -b2 WTempty.dedup.bam -o WTHA.WTempty.dedup.ratio.bw -of bigwig --operation ratio --binSize 1 -p 20"

# other options:
# --scaleFactorsMethod {readCount,SES,None}
# --normalizeUsing {RPKM,CPM,BPM,RPGC,None}
# --skipNonCoveredRegions
```

Select gene groups:

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/annotation/

# processed_pseudogene and no L1 overlap
awk -v OFS="\t" '{if ($7=="processed_pseudogene" && $9=="n") print $0}' gencode.v35.annotation.gene_type.intronless.L1.bed > gencode.v35.annotation.gene_type.intronless.L1.processed_pseudogene.noL1.bed
wc -l gencode.v35.annotation.gene_type.intronless.L1.processed_pseudogene.noL1.bed # 9786

# protein_coding, intronless and no L1 overlap
awk -v OFS="\t" '{if ($7=="protein_coding" && $8=="y" && $9=="n") print $0}' gencode.v35.annotation.gene_type.intronless.L1.bed > gencode.v35.annotation.gene_type.intronless.L1.protein_coding.intronless.noL1.bed
wc -l gencode.v35.annotation.gene_type.intronless.L1.protein_coding.intronless.noL1.bed # 891

# protein_coding, introncontaining and no L1 overlap
awk -v OFS="\t" '{if ($7=="protein_coding" && $8=="n" && $9=="n") print $0}' gencode.v35.annotation.gene_type.intronless.L1.bed > gencode.v35.annotation.gene_type.intronless.L1.protein_coding.introncontaining.noL1.bed
wc -l gencode.v35.annotation.gene_type.intronless.L1.protein_coding.introncontaining.noL1.bed # 4857
```

Select gene groups from Marta:

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/genes

wc -l *.bed
  # 2455 introncontaining.bed
  #  275 intronless.bed
  # 2648 pseudogenes.bed
```

computeMatrix, plotHeatmap and plotProfile:

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/hisat2

####################
# --operation log2 #
####################

# computeMatrix and plotProfile
sbatch -J computeMatrix_plotProfile_log2 -o ../deeptools/20210720_computeMatrix_plotProfile_log2.log --mem 16G -t 1-0 --wrap "computeMatrix scale-regions \
-R ../genes/pseudogenes.bed ../genes/intronless.bed ../genes/introncontaining.bed \
-S HA.SKOHA.empty.SKOempty.dedup.log2.bw WTHA.WTempty.dedup.log2.bw \
-o ../deeptools/20210720_computeMatrix.log2.mat.gz \
--outFileNameMatrix ../deeptools/20210720_computeMatrix.log2.tab \
-m 1000 \
--startLabel TSS \
--endLabel TTS \
-b 1500 \
-a 1500 \
-bs 1 \
-p 'max' && \
plotProfile \
-m ../deeptools/20210720_computeMatrix.log2.mat.gz \
-o ../deeptools/20210720_plotProfile.log2.pdf \
--dpi 300 \
--averageType mean \
--plotType 'lines' \
--startLabel 'TSS' \
--endLabel 'TTS' \
--regionsLabel 'processed pseudogenes' 'intronless protein-coding genes' 'intron-containing protein-coding genes' \
--samplesLabel 'SETDB1-KO' 'WT' \
--plotTitle '' \
--colors 'royalblue' 'black' \
-y 'log2 FC (PPHLN/control)' \
--perGroup"

# plotHeatmap
plotHeatmap \
-m ../deeptools/20210720_computeMatrix.log2.mat.gz \
-o ../deeptools/20210720_plotHeatmap.log2.pdf \
--dpi 300 \
--averageTypeSummaryPlot mean \
--heatmapWidth 7 \
--startLabel 'TSS' \
--endLabel 'TTS' \
--regionsLabel 'processed pseudogenes' 'intronless protein-coding genes' 'intron-containing protein-coding genes' \
--samplesLabel 'SETDB1-KO' 'WT' \
--plotTitle '' \
-y 'log2 FC (PPHLN/control)' \
--perGroup


#####################
# --operation ratio #
#####################

# computeMatrix and plotProfile
sbatch -J computeMatrix_plotProfile_ratio -o ../deeptools/20210720_computeMatrix_plotProfile_ratio.log --mem 16G -t 1-0 --wrap "computeMatrix scale-regions \
-R ../genes/pseudogenes.bed ../genes/intronless.bed ../genes/introncontaining.bed \
-S HA.SKOHA.empty.SKOempty.dedup.ratio.bw WTHA.WTempty.dedup.ratio.bw \
-o ../deeptools/20210720_computeMatrix.ratio.mat.gz \
--outFileNameMatrix ../deeptools/20210720_computeMatrix.ratio.tab \
-m 1000 \
--startLabel TSS \
--endLabel TTS \
-b 1500 \
-a 1500 \
-bs 1 \
-p 'max' && \
plotProfile \
-m ../deeptools/20210720_computeMatrix.ratio.mat.gz \
-o ../deeptools/20210720_plotProfile.ratio.pdf \
--dpi 300 \
--averageType mean \
--plotType 'lines' \
--startLabel 'TSS' \
--endLabel 'TTS' \
--regionsLabel 'processed pseudogenes' 'intronless protein-coding genes' 'intron-containing protein-coding genes' \
--samplesLabel 'SETDB1-KO' 'WT' \
--plotTitle '' \
--colors 'royalblue' 'black' \
-y 'FC (PPHLN/control)' \
--perGroup"

# plotHeatmap
plotHeatmap \
-m ../deeptools/20210720_computeMatrix.ratio.mat.gz \
-o ../deeptools/20210720_plotHeatmap.ratio.pdf \
--dpi 300 \
--averageTypeSummaryPlot mean \
--heatmapWidth 7 \
--startLabel 'TSS' \
--endLabel 'TTS' \
--regionsLabel 'processed pseudogenes' 'intronless protein-coding genes' 'intron-containing protein-coding genes' \
--samplesLabel 'SETDB1-KO' 'WT' \
--plotTitle '' \
-y 'FC (PPHLN/control)' \
--perGroup


# computeMatrix and plotProfile --skipZeros
sbatch -J computeMatrix_plotProfile_ratio_skipZeros -o ../deeptools/20210720_computeMatrix_plotProfile_ratio_skipZeros.log --mem 16G -t 1-0 --wrap "computeMatrix scale-regions \
-R ../genes/pseudogenes.bed ../genes/intronless.bed ../genes/introncontaining.bed \
-S HA.SKOHA.empty.SKOempty.dedup.ratio.bw WTHA.WTempty.dedup.ratio.bw \
-o ../deeptools/20210720_computeMatrix.ratio.skipZeros.mat.gz \
--outFileNameMatrix ../deeptools/20210720_computeMatrix.ratio.skipZeros.tab \
-m 1000 \
--startLabel TSS \
--endLabel TTS \
-b 1500 \
-a 1500 \
-bs 1 \
--skipZeros \
-p 'max' && \
plotProfile \
-m ../deeptools/20210720_computeMatrix.ratio.skipZeros.mat.gz \
-o ../deeptools/20210720_plotProfile.ratio.skipZeros.pdf \
--dpi 300 \
--averageType mean \
--plotType 'lines' \
--startLabel 'TSS' \
--endLabel 'TTS' \
--regionsLabel 'processed pseudogenes' 'intronless protein-coding genes' 'intron-containing protein-coding genes' \
--samplesLabel 'SETDB1-KO' 'WT' \
--plotTitle '' \
--colors 'royalblue' 'black' \
-y 'FC (PPHLN/control)' \
--perGroup"

# plotHeatmap --skipZeros
plotHeatmap \
-m ../deeptools/20210720_computeMatrix.ratio.skipZeros.mat.gz \
-o ../deeptools/20210720_plotHeatmap.ratio.skipZeros.pdf \
--dpi 300 \
--averageTypeSummaryPlot mean \
--heatmapWidth 7 \
--startLabel 'TSS' \
--endLabel 'TTS' \
--regionsLabel 'processed pseudogenes' 'intronless protein-coding genes' 'intron-containing protein-coding genes' \
--samplesLabel 'SETDB1-KO' 'WT' \
--plotTitle '' \
-y 'FC (PPHLN/control)' \
--perGroup
```


#### deeptools - new settings

Merge bam files, index and convert to bigwig using bamCoverage:

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/hisat2

for bam in empty.SKOempty.dedup.bam HA.SKOHA.dedup.bam WTempty.dedup.bam WTHA.dedup.bam
do
  bname=${bam%.bam}
  sbatch -J ${bname}.normalizeUsingNone -o ${bname}.normalizeUsingNone.log --mem 16G -t 1-0 --wrap "bamCoverage -b $bam -o ${bname}.normalizeUsingNone.bw -of bigwig --binSize 1 -p 20"
done

# --normalizeUsing CPM
ls -lh empty.SKOempty.dedup.bw HA.SKOHA.dedup.bw WTempty.dedup.bw WTHA.dedup.bw
# -rw-rw---- 1 kzqv978 xem-scp-qbio 183M Jul 18 17:41 empty.SKOempty.dedup.bw
# -rw-rw---- 1 kzqv978 xem-scp-qbio 347M Jul 18 17:47 HA.SKOHA.dedup.bw
# -rw-rw---- 1 kzqv978 xem-scp-qbio 144M Jul 18 16:19 WTempty.dedup.bw
# -rw-rw---- 1 kzqv978 xem-scp-qbio 234M Jul 18 16:25 WTHA.dedup.bw

# --normalizeUsing None
ls -lh empty.SKOempty.dedup.normalizeUsingNone.bw HA.SKOHA.dedup.normalizeUsingNone.bw WTempty.dedup.normalizeUsingNone.bw WTHA.dedup.normalizeUsingNone.bw
# -rw-rw---- 1 kzqv978 xem-scp-qbio 171M Jul 24 07:24 empty.SKOempty.dedup.normalizeUsingNone.bw
# -rw-rw---- 1 kzqv978 xem-scp-qbio 324M Jul 24 07:36 HA.SKOHA.dedup.normalizeUsingNone.bw
# -rw-rw---- 1 kzqv978 xem-scp-qbio 136M Jul 24 07:22 WTempty.dedup.normalizeUsingNone.bw
# -rw-rw---- 1 kzqv978 xem-scp-qbio 219M Jul 24 07:27 WTHA.dedup.normalizeUsingNone.bw
```

Select gene groups from Marta (selected and all):

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/genes

wc -l *.bed
 # 19004 introncontaining_all.bed
 #  2454 introncontaining.bed
 #   935 intronless_all.bed
 #   274 intronless.bed
 # 10670 pseudogenes_all.bed
 #  2648 pseudogenes.bed
```

computeMatrix and plotHeatmap:

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/hisat2

# https://matplotlib.org/2.0.2/users/colormaps.html

############
# setdb1ko #
############
# +/- 3kb all
sbatch -J computeMatrix_plotHeatmap_setdb1ko_3kb -o ../deeptools/20210723_computeMatrix_plotHeatmap_setdb1ko_3kb.log --mem 16G -t 1-0 --wrap "computeMatrix scale-regions \
-R ../genes/pseudogenes_all.bed ../genes/intronless_all.bed ../genes/introncontaining_all.bed \
-S HA.SKOHA.dedup.bw empty.SKOempty.dedup.bw \
-o ../deeptools/20210723_computeMatrix_plotHeatmap_setdb1ko_3kb.mat.gz \
--outFileNameMatrix ../deeptools/20210723_computeMatrix_plotHeatmap_setdb1ko_3kb.tab \
-m 1000 \
--startLabel TSS \
--endLabel TTS \
-b 3000 \
-a 3000 \
-bs 1 \
-p 'max' && \
plotHeatmap \
-m ../deeptools/20210723_computeMatrix_plotHeatmap_setdb1ko_3kb.mat.gz \
-o ../deeptools/20210723_computeMatrix_plotHeatmap_setdb1ko_3kb.pdf \
--dpi 300 \
--averageTypeSummaryPlot mean \
--colorMap coolwarm \
--heatmapWidth 7 \
--startLabel 'TSS' \
--endLabel 'TTS' \
--regionsLabel 'processed pseudogenes' 'intronless protein-coding genes' 'intron-containing protein-coding genes' \
--samplesLabel 'PPHLN' 'control' \
--plotTitle '' \
-y 'CPM' \
--perGroup"

# +/- 3kb all sortUsingSamples 1
sbatch -J computeMatrix_plotHeatmap_setdb1ko_3kb_sortUsingSamples -o ../deeptools/20210725_computeMatrix_plotHeatmap_setdb1ko_3kb_sortUsingSamples.log --mem 16G -t 1-0 --wrap "computeMatrix scale-regions \
-R ../genes/pseudogenes_all.bed ../genes/intronless_all.bed ../genes/introncontaining_all.bed \
-S HA.SKOHA.dedup.bw empty.SKOempty.dedup.bw \
-o ../deeptools/20210725_computeMatrix_plotHeatmap_setdb1ko_3kb_sortUsingSamples.mat.gz \
--outFileNameMatrix ../deeptools/20210725_computeMatrix_plotHeatmap_setdb1ko_3kb_sortUsingSamples.tab \
-m 1000 \
--startLabel TSS \
--endLabel TTS \
-b 3000 \
-a 3000 \
-bs 1 \
-p 'max' && \
plotHeatmap \
-m ../deeptools/20210725_computeMatrix_plotHeatmap_setdb1ko_3kb_sortUsingSamples.mat.gz \
-o ../deeptools/20210725_computeMatrix_plotHeatmap_setdb1ko_3kb_sortUsingSamples.pdf \
--dpi 300 \
--sortUsingSamples 1 \
--averageTypeSummaryPlot mean \
--colorMap coolwarm \
--heatmapWidth 7 \
--startLabel 'TSS' \
--endLabel 'TTS' \
--regionsLabel 'processed pseudogenes' 'intronless protein-coding genes' 'intron-containing protein-coding genes' \
--samplesLabel 'PPHLN' 'control' \
--plotTitle '' \
-y 'CPM' \
--perGroup"

# +/- 3kb all sortUsingSamples 2
sbatch -J computeMatrix_plotHeatmap_setdb1ko_3kb_sortUsingSamples2 -o ../deeptools/20210725_computeMatrix_plotHeatmap_setdb1ko_3kb_sortUsingSamples2.log --mem 16G -t 1-0 --wrap "computeMatrix scale-regions \
-R ../genes/pseudogenes_all.bed ../genes/intronless_all.bed ../genes/introncontaining_all.bed \
-S HA.SKOHA.dedup.bw empty.SKOempty.dedup.bw \
-o ../deeptools/20210725_computeMatrix_plotHeatmap_setdb1ko_3kb_sortUsingSamples2.mat.gz \
--outFileNameMatrix ../deeptools/20210725_computeMatrix_plotHeatmap_setdb1ko_3kb_sortUsingSamples2.tab \
-m 1000 \
--startLabel TSS \
--endLabel TTS \
-b 3000 \
-a 3000 \
-bs 1 \
-p 'max' && \
plotHeatmap \
-m ../deeptools/20210725_computeMatrix_plotHeatmap_setdb1ko_3kb_sortUsingSamples2.mat.gz \
-o ../deeptools/20210725_computeMatrix_plotHeatmap_setdb1ko_3kb_sortUsingSamples2.pdf \
--dpi 300 \
--sortUsingSamples 2 \
--averageTypeSummaryPlot mean \
--colorMap coolwarm \
--heatmapWidth 7 \
--startLabel 'TSS' \
--endLabel 'TTS' \
--regionsLabel 'processed pseudogenes' 'intronless protein-coding genes' 'intron-containing protein-coding genes' \
--samplesLabel 'PPHLN' 'control' \
--plotTitle '' \
-y 'CPM' \
--perGroup"

# +/- 3kb all normalizeUsingNone
sbatch -J computeMatrix_plotHeatmap_setdb1ko_3kb_normalizeUsingNone -o ../deeptools/20210723_computeMatrix_plotHeatmap_setdb1ko_3kb_normalizeUsingNone.log --mem 16G -t 1-0 --wrap "computeMatrix scale-regions \
-R ../genes/pseudogenes_all.bed ../genes/intronless_all.bed ../genes/introncontaining_all.bed \
-S HA.SKOHA.dedup.normalizeUsingNone.bw empty.SKOempty.dedup.normalizeUsingNone.bw \
-o ../deeptools/20210723_computeMatrix_plotHeatmap_setdb1ko_3kb_normalizeUsingNone.mat.gz \
--outFileNameMatrix ../deeptools/20210723_computeMatrix_plotHeatmap_setdb1ko_3kb_normalizeUsingNone.tab \
-m 1000 \
--startLabel TSS \
--endLabel TTS \
-b 3000 \
-a 3000 \
-bs 1 \
-p 'max' && \
plotHeatmap \
-m ../deeptools/20210723_computeMatrix_plotHeatmap_setdb1ko_3kb_normalizeUsingNone.mat.gz \
-o ../deeptools/20210723_computeMatrix_plotHeatmap_setdb1ko_3kb_normalizeUsingNone.pdf \
--dpi 300 \
--averageTypeSummaryPlot mean \
--colorMap coolwarm \
--heatmapWidth 7 \
--startLabel 'TSS' \
--endLabel 'TTS' \
--regionsLabel 'processed pseudogenes' 'intronless protein-coding genes' 'intron-containing protein-coding genes' \
--samplesLabel 'PPHLN' 'control' \
--plotTitle '' \
-y 'CPM' \
--perGroup"

# +/- 3kb selected
sbatch -J computeMatrix_plotHeatmap_setdb1ko_3kb_selected -o ../deeptools/20210723_computeMatrix_plotHeatmap_setdb1ko_3kb_selected.log --mem 16G -t 1-0 --wrap "computeMatrix scale-regions \
-R ../genes/pseudogenes.bed ../genes/intronless.bed ../genes/introncontaining.bed \
-S HA.SKOHA.dedup.bw empty.SKOempty.dedup.bw \
-o ../deeptools/20210723_computeMatrix_plotHeatmap_setdb1ko_3kb_selected.mat.gz \
--outFileNameMatrix ../deeptools/20210723_computeMatrix_plotHeatmap_setdb1ko_3kb_selected.tab \
-m 1000 \
--startLabel TSS \
--endLabel TTS \
-b 3000 \
-a 3000 \
-bs 1 \
-p 'max' && \
plotHeatmap \
-m ../deeptools/20210723_computeMatrix_plotHeatmap_setdb1ko_3kb_selected.mat.gz \
-o ../deeptools/20210723_computeMatrix_plotHeatmap_setdb1ko_3kb_selected.pdf \
--dpi 300 \
--averageTypeSummaryPlot mean \
--colorMap coolwarm \
--heatmapWidth 7 \
--startLabel 'TSS' \
--endLabel 'TTS' \
--regionsLabel 'processed pseudogenes' 'intronless protein-coding genes' 'intron-containing protein-coding genes' \
--samplesLabel 'PPHLN' 'control' \
--plotTitle '' \
-y 'CPM' \
--perGroup"

# +/- 10kb all
sbatch -J computeMatrix_plotHeatmap_setdb1ko_10kb -o ../deeptools/20210723_computeMatrix_plotHeatmap_setdb1ko_10kb.log --mem 64G -t 2-0 --wrap "computeMatrix scale-regions \
-R ../genes/pseudogenes_all.bed ../genes/intronless_all.bed ../genes/introncontaining_all.bed \
-S HA.SKOHA.dedup.bw empty.SKOempty.dedup.bw \
-o ../deeptools/20210723_computeMatrix_plotHeatmap_setdb1ko_10kb.mat.gz \
--outFileNameMatrix ../deeptools/20210723_computeMatrix_plotHeatmap_setdb1ko_10kb.tab \
-m 1000 \
--startLabel TSS \
--endLabel TTS \
-b 10000 \
-a 10000 \
-bs 1 \
-p 'max' && \
plotHeatmap \
-m ../deeptools/20210723_computeMatrix_plotHeatmap_setdb1ko_10kb.mat.gz \
-o ../deeptools/20210723_computeMatrix_plotHeatmap_setdb1ko_10kb.pdf \
--dpi 300 \
--averageTypeSummaryPlot mean \
--colorMap coolwarm \
--heatmapWidth 7 \
--startLabel 'TSS' \
--endLabel 'TTS' \
--regionsLabel 'processed pseudogenes' 'intronless protein-coding genes' 'intron-containing protein-coding genes' \
--samplesLabel 'PPHLN' 'control' \
--plotTitle '' \
-y 'CPM' \
--perGroup"

# +/- 0kb all
sbatch -J computeMatrix_plotHeatmap_setdb1ko_0kb -o ../deeptools/20210725_computeMatrix_plotHeatmap_setdb1ko_0kb.log --mem 16G -t 1-0 --wrap "computeMatrix scale-regions \
-R ../genes/pseudogenes_all.bed ../genes/intronless_all.bed ../genes/introncontaining_all.bed \
-S HA.SKOHA.dedup.bw empty.SKOempty.dedup.bw \
-o ../deeptools/20210725_computeMatrix_plotHeatmap_setdb1ko_0kb.mat.gz \
--outFileNameMatrix ../deeptools/20210725_computeMatrix_plotHeatmap_setdb1ko_0kb.tab \
-m 1000 \
--startLabel TSS \
--endLabel TTS \
-b 0 \
-a 0 \
-bs 1 \
-p 'max' && \
plotHeatmap \
-m ../deeptools/20210725_computeMatrix_plotHeatmap_setdb1ko_0kb.mat.gz \
-o ../deeptools/20210725_computeMatrix_plotHeatmap_setdb1ko_0kb.pdf \
--dpi 300 \
--averageTypeSummaryPlot mean \
--colorMap coolwarm \
--heatmapWidth 7 \
--startLabel 'TSS' \
--endLabel 'TTS' \
--regionsLabel 'processed pseudogenes' 'intronless protein-coding genes' 'intron-containing protein-coding genes' \
--samplesLabel 'PPHLN' 'control' \
--plotTitle '' \
-y 'CPM' \
--perGroup"



######
# wt #
######
# +/- 3kb all
sbatch -J computeMatrix_plotHeatmap_wt_3kb -o ../deeptools/20210723_computeMatrix_plotHeatmap_wt_3kb.log --mem 16G -t 1-0 --wrap "computeMatrix scale-regions \
-R ../genes/pseudogenes_all.bed ../genes/intronless_all.bed ../genes/introncontaining_all.bed \
-S WTHA.dedup.bw WTempty.dedup.bw \
-o ../deeptools/20210723_computeMatrix_plotHeatmap_wt_3kb.mat.gz \
--outFileNameMatrix ../deeptools/20210723_computeMatrix_plotHeatmap_wt_3kb.tab \
-m 1000 \
--startLabel TSS \
--endLabel TTS \
-b 3000 \
-a 3000 \
-bs 1 \
-p 'max' && \
plotHeatmap \
-m ../deeptools/20210723_computeMatrix_plotHeatmap_wt_3kb.mat.gz \
-o ../deeptools/20210723_computeMatrix_plotHeatmap_wt_3kb.pdf \
--dpi 300 \
--averageTypeSummaryPlot mean \
--colorMap coolwarm \
--heatmapWidth 7 \
--startLabel 'TSS' \
--endLabel 'TTS' \
--regionsLabel 'processed pseudogenes' 'intronless protein-coding genes' 'intron-containing protein-coding genes' \
--samplesLabel 'PPHLN' 'control' \
--plotTitle '' \
-y 'CPM' \
--perGroup"

# +/- 3kb all sortUsingSamples 1
sbatch -J computeMatrix_plotHeatmap_wt_3kb_sortUsingSamples -o ../deeptools/20210725_computeMatrix_plotHeatmap_wt_3kb_sortUsingSamples.log --mem 16G -t 1-0 --wrap "computeMatrix scale-regions \
-R ../genes/pseudogenes_all.bed ../genes/intronless_all.bed ../genes/introncontaining_all.bed \
-S WTHA.dedup.bw WTempty.dedup.bw \
-o ../deeptools/20210725_computeMatrix_plotHeatmap_wt_3kb_sortUsingSamples.mat.gz \
--outFileNameMatrix ../deeptools/20210725_computeMatrix_plotHeatmap_wt_3kb_sortUsingSamples.tab \
-m 1000 \
--startLabel TSS \
--endLabel TTS \
-b 3000 \
-a 3000 \
-bs 1 \
-p 'max' && \
plotHeatmap \
-m ../deeptools/20210725_computeMatrix_plotHeatmap_wt_3kb_sortUsingSamples.mat.gz \
-o ../deeptools/20210725_computeMatrix_plotHeatmap_wt_3kb_sortUsingSamples.pdf \
--dpi 300 \
--sortUsingSamples 1 \
--averageTypeSummaryPlot mean \
--colorMap coolwarm \
--heatmapWidth 7 \
--startLabel 'TSS' \
--endLabel 'TTS' \
--regionsLabel 'processed pseudogenes' 'intronless protein-coding genes' 'intron-containing protein-coding genes' \
--samplesLabel 'PPHLN' 'control' \
--plotTitle '' \
-y 'CPM' \
--perGroup"

# +/- 3kb all sortUsingSamples 1
sbatch -J computeMatrix_plotHeatmap_wt_3kb_sortUsingSamples2 -o ../deeptools/20210725_computeMatrix_plotHeatmap_wt_3kb_sortUsingSamples2.log --mem 16G -t 1-0 --wrap "computeMatrix scale-regions \
-R ../genes/pseudogenes_all.bed ../genes/intronless_all.bed ../genes/introncontaining_all.bed \
-S WTHA.dedup.bw WTempty.dedup.bw \
-o ../deeptools/20210725_computeMatrix_plotHeatmap_wt_3kb_sortUsingSamples2.mat.gz \
--outFileNameMatrix ../deeptools/20210725_computeMatrix_plotHeatmap_wt_3kb_sortUsingSamples2.tab \
-m 1000 \
--startLabel TSS \
--endLabel TTS \
-b 3000 \
-a 3000 \
-bs 1 \
-p 'max' && \
plotHeatmap \
-m ../deeptools/20210725_computeMatrix_plotHeatmap_wt_3kb_sortUsingSamples2.mat.gz \
-o ../deeptools/20210725_computeMatrix_plotHeatmap_wt_3kb_sortUsingSamples2.pdf \
--dpi 300 \
--sortUsingSamples 2 \
--averageTypeSummaryPlot mean \
--colorMap coolwarm \
--heatmapWidth 7 \
--startLabel 'TSS' \
--endLabel 'TTS' \
--regionsLabel 'processed pseudogenes' 'intronless protein-coding genes' 'intron-containing protein-coding genes' \
--samplesLabel 'PPHLN' 'control' \
--plotTitle '' \
-y 'CPM' \
--perGroup"

# +/- 3kb all normalizeUsingNone
sbatch -J computeMatrix_plotHeatmap_wt_3kb_normalizeUsingNone -o ../deeptools/20210723_computeMatrix_plotHeatmap_wt_3kb_normalizeUsingNone.log --mem 16G -t 1-0 --wrap "computeMatrix scale-regions \
-R ../genes/pseudogenes_all.bed ../genes/intronless_all.bed ../genes/introncontaining_all.bed \
-S WTHA.dedup.normalizeUsingNone.bw WTempty.dedup.normalizeUsingNone.bw \
-o ../deeptools/20210723_computeMatrix_plotHeatmap_wt_3kb_normalizeUsingNone.mat.gz \
--outFileNameMatrix ../deeptools/20210723_computeMatrix_plotHeatmap_wt_3kb_normalizeUsingNone.tab \
-m 1000 \
--startLabel TSS \
--endLabel TTS \
-b 3000 \
-a 3000 \
-bs 1 \
-p 'max' && \
plotHeatmap \
-m ../deeptools/20210723_computeMatrix_plotHeatmap_wt_3kb_normalizeUsingNone.mat.gz \
-o ../deeptools/20210723_computeMatrix_plotHeatmap_wt_3kb_normalizeUsingNone.pdf \
--dpi 300 \
--averageTypeSummaryPlot mean \
--colorMap coolwarm \
--heatmapWidth 7 \
--startLabel 'TSS' \
--endLabel 'TTS' \
--regionsLabel 'processed pseudogenes' 'intronless protein-coding genes' 'intron-containing protein-coding genes' \
--samplesLabel 'PPHLN' 'control' \
--plotTitle '' \
-y 'CPM' \
--perGroup"

# +/- 3kb selected
sbatch -J computeMatrix_plotHeatmap_wt_3kb_selected -o ../deeptools/20210723_computeMatrix_plotHeatmap_wt_3kb_selected.log --mem 16G -t 1-0 --wrap "computeMatrix scale-regions \
-R ../genes/pseudogenes.bed ../genes/intronless.bed ../genes/introncontaining.bed \
-S WTHA.dedup.bw WTempty.dedup.bw \
-o ../deeptools/20210723_computeMatrix_plotHeatmap_wt_3kb_selected.mat.gz \
--outFileNameMatrix ../deeptools/20210723_computeMatrix_plotHeatmap_wt_3kb_selected.tab \
-m 1000 \
--startLabel TSS \
--endLabel TTS \
-b 3000 \
-a 3000 \
-bs 1 \
-p 'max' && \
plotHeatmap \
-m ../deeptools/20210723_computeMatrix_plotHeatmap_wt_3kb_selected.mat.gz \
-o ../deeptools/20210723_computeMatrix_plotHeatmap_wt_3kb_selected.pdf \
--dpi 300 \
--averageTypeSummaryPlot mean \
--colorMap coolwarm \
--heatmapWidth 7 \
--startLabel 'TSS' \
--endLabel 'TTS' \
--regionsLabel 'processed pseudogenes' 'intronless protein-coding genes' 'intron-containing protein-coding genes' \
--samplesLabel 'PPHLN' 'control' \
--plotTitle '' \
-y 'CPM' \
--perGroup"

# +/- 10kb all
sbatch -J computeMatrix_plotHeatmap_wt_10kb -o ../deeptools/20210723_computeMatrix_plotHeatmap_wt_10kb.log --mem 64G -t 2-0 --wrap "computeMatrix scale-regions \
-R ../genes/pseudogenes_all.bed ../genes/intronless_all.bed ../genes/introncontaining_all.bed \
-S WTHA.dedup.bw WTempty.dedup.bw \
-o ../deeptools/20210723_computeMatrix_plotHeatmap_wt_10kb.mat.gz \
--outFileNameMatrix ../deeptools/20210723_computeMatrix_plotHeatmap_wt_10kb.tab \
-m 1000 \
--startLabel TSS \
--endLabel TTS \
-b 10000 \
-a 10000 \
-bs 1 \
-p 'max' && \
plotHeatmap \
-m ../deeptools/20210723_computeMatrix_plotHeatmap_wt_10kb.mat.gz \
-o ../deeptools/20210723_computeMatrix_plotHeatmap_wt_10kb.pdf \
--dpi 300 \
--averageTypeSummaryPlot mean \
--colorMap coolwarm \
--heatmapWidth 7 \
--startLabel 'TSS' \
--endLabel 'TTS' \
--regionsLabel 'processed pseudogenes' 'intronless protein-coding genes' 'intron-containing protein-coding genes' \
--samplesLabel 'PPHLN' 'control' \
--plotTitle '' \
-y 'CPM' \
--perGroup"

# +/- 0kb all
sbatch -J computeMatrix_plotHeatmap_wt_0kb -o ../deeptools/20210725_computeMatrix_plotHeatmap_wt_0kb.log --mem 16G -t 1-0 --wrap "computeMatrix scale-regions \
-R ../genes/pseudogenes_all.bed ../genes/intronless_all.bed ../genes/introncontaining_all.bed \
-S WTHA.dedup.bw WTempty.dedup.bw \
-o ../deeptools/20210725_computeMatrix_plotHeatmap_wt_0kb.mat.gz \
--outFileNameMatrix ../deeptools/20210725_computeMatrix_plotHeatmap_wt_0kb.tab \
-m 1000 \
--startLabel TSS \
--endLabel TTS \
-b 0 \
-a 0 \
-bs 1 \
-p 'max' && \
plotHeatmap \
-m ../deeptools/20210725_computeMatrix_plotHeatmap_wt_0kb.mat.gz \
-o ../deeptools/20210725_computeMatrix_plotHeatmap_wt_0kb.pdf \
--dpi 300 \
--averageTypeSummaryPlot mean \
--colorMap coolwarm \
--heatmapWidth 7 \
--startLabel 'TSS' \
--endLabel 'TTS' \
--regionsLabel 'processed pseudogenes' 'intronless protein-coding genes' 'intron-containing protein-coding genes' \
--samplesLabel 'PPHLN' 'control' \
--plotTitle '' \
-y 'CPM' \
--perGroup"
```

computeMatrix and plotProfile:

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/ripseq/hisat2

sbatch -J computeMatrix_plotProfile_ratio_3kb -o ../deeptools/20210723_computeMatrix_plotProfile_ratio_3kb.log --mem 16G -t 1-0 --wrap "computeMatrix scale-regions \
-R ../genes/pseudogenes.bed ../genes/intronless.bed ../genes/introncontaining.bed \
-S HA.SKOHA.empty.SKOempty.dedup.ratio.bw WTHA.WTempty.dedup.ratio.bw \
-o ../deeptools/20210723_computeMatrix_plotProfile_ratio_3kb.mat.gz \
--outFileNameMatrix ../deeptools/20210723_computeMatrix_plotProfile_ratio_3kb.tab \
-m 1000 \
--startLabel TSS \
--endLabel TTS \
-b 3000 \
-a 3000 \
-bs 1 \
-p 'max' && \
plotProfile \
-m ../deeptools/20210723_computeMatrix_plotProfile_ratio_3kb.mat.gz \
-o ../deeptools/20210723_computeMatrix_plotProfile_ratio_3kb.pdf \
--dpi 300 \
--averageType mean \
--plotType 'lines' \
--startLabel 'TSS' \
--endLabel 'TTS' \
--regionsLabel 'processed pseudogenes' 'intronless protein-coding genes' 'intron-containing protein-coding genes' \
--samplesLabel 'SETDB1-KO' 'WT' \
--plotTitle '' \
--colors 'royalblue' 'black' \
-y 'FC (PPHLN/control)' \
--perGroup"
```

