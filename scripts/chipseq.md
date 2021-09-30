
## ChIP-seq

### obtain fastq files

- data is available at CI's 10.20.192.25:/media/staging/210527_A00489_0912_AHCT7YDRXY/fastq/SLX-19690*
- I copied the data to the ftp using [these instructions](https://github.com/sblab-bioinformatics/projects/blob/master/admin/ftp_cruk.md)
- I downloaded from the ftp to my local machine (curl worked in my local machine but not in scp)
- Finally I copied from local machine to scp

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/chipseq2/fastq

ls -lh *
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  286K May 29 13:48 SLX-19690.HCT7YDRXY.s_1.bcl2fastq.zip
# -rwxr-x--- 1 kzqv978 xem-scp-qbio   632 May 29 13:48 SLX-19690.HCT7YDRXY.s_1.contents.csv
# -rwxr-x--- 1 kzqv978 xem-scp-qbio   156 May 29 13:48 SLX-19690.HCT7YDRXY.s_1.lostreads.md5sums.txt
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  417M May 29 13:49 SLX-19690.HCT7YDRXY.s_1.r_1.lostreads.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  434M May 29 13:49 SLX-19690.HCT7YDRXY.s_1.r_2.lostreads.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio   156 May 29 13:49 SLX-19690.NEBNext09.HCT7YDRXY.s_1.md5sums.txt
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  860M May 29 13:49 SLX-19690.NEBNext09.HCT7YDRXY.s_1.r_1.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  886M May 29 13:49 SLX-19690.NEBNext09.HCT7YDRXY.s_1.r_2.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio   156 May 29 13:49 SLX-19690.NEBNext10.HCT7YDRXY.s_1.md5sums.txt
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  1.1G May 29 13:49 SLX-19690.NEBNext10.HCT7YDRXY.s_1.r_1.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  1.1G May 29 13:49 SLX-19690.NEBNext10.HCT7YDRXY.s_1.r_2.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio   156 May 29 13:49 SLX-19690.NEBNext11.HCT7YDRXY.s_1.md5sums.txt
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  1.1G May 29 13:49 SLX-19690.NEBNext11.HCT7YDRXY.s_1.r_1.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  1.1G May 29 13:49 SLX-19690.NEBNext11.HCT7YDRXY.s_1.r_2.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio   156 May 29 13:49 SLX-19690.NEBNext12.HCT7YDRXY.s_1.md5sums.txt
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  905M May 29 13:49 SLX-19690.NEBNext12.HCT7YDRXY.s_1.r_1.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  950M May 29 13:49 SLX-19690.NEBNext12.HCT7YDRXY.s_1.r_2.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio   156 May 29 13:49 SLX-19690.NEBNext15.HCT7YDRXY.s_1.md5sums.txt
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  917M May 29 13:49 SLX-19690.NEBNext15.HCT7YDRXY.s_1.r_1.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  966M May 29 13:49 SLX-19690.NEBNext15.HCT7YDRXY.s_1.r_2.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio   156 May 29 13:49 SLX-19690.NEBNext19.HCT7YDRXY.s_1.md5sums.txt
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  730M May 29 13:49 SLX-19690.NEBNext19.HCT7YDRXY.s_1.r_1.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  754M May 29 13:49 SLX-19690.NEBNext19.HCT7YDRXY.s_1.r_2.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio   156 May 29 13:49 SLX-19690.NEBNext20.HCT7YDRXY.s_1.md5sums.txt
# -rwxr-x--- 1 kzqv978 xem-scp-qbio 1008M May 29 13:49 SLX-19690.NEBNext20.HCT7YDRXY.s_1.r_1.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  1.1G May 29 13:49 SLX-19690.NEBNext20.HCT7YDRXY.s_1.r_2.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio   156 May 29 13:49 SLX-19690.NEBNext21.HCT7YDRXY.s_1.md5sums.txt
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  745M May 29 13:49 SLX-19690.NEBNext21.HCT7YDRXY.s_1.r_1.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  776M May 29 13:49 SLX-19690.NEBNext21.HCT7YDRXY.s_1.r_2.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio   156 May 29 13:49 SLX-19690.NEBNext31.HCT7YDRXY.s_1.md5sums.txt
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  985M May 29 13:49 SLX-19690.NEBNext31.HCT7YDRXY.s_1.r_1.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio 1012M May 29 13:49 SLX-19690.NEBNext31.HCT7YDRXY.s_1.r_2.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio   156 May 29 13:49 SLX-19690.NEBNext32.HCT7YDRXY.s_1.md5sums.txt
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  858M May 29 13:49 SLX-19690.NEBNext32.HCT7YDRXY.s_1.r_1.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  891M May 29 13:49 SLX-19690.NEBNext32.HCT7YDRXY.s_1.r_2.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio   156 May 29 13:49 SLX-19690.NEBNext33.HCT7YDRXY.s_1.md5sums.txt
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  876M May 29 13:50 SLX-19690.NEBNext33.HCT7YDRXY.s_1.r_1.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  901M May 29 13:50 SLX-19690.NEBNext33.HCT7YDRXY.s_1.r_2.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio   156 May 29 13:50 SLX-19690.NEBNext34.HCT7YDRXY.s_1.md5sums.txt
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  669M May 29 13:50 SLX-19690.NEBNext34.HCT7YDRXY.s_1.r_1.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  697M May 29 13:50 SLX-19690.NEBNext34.HCT7YDRXY.s_1.r_2.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio   156 May 29 13:50 SLX-19690.NEBNext36.HCT7YDRXY.s_1.md5sums.txt
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  809M May 29 13:51 SLX-19690.NEBNext36.HCT7YDRXY.s_1.r_1.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  835M May 29 13:51 SLX-19690.NEBNext36.HCT7YDRXY.s_1.r_2.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio   156 May 29 13:51 SLX-19690.NEBNext37.HCT7YDRXY.s_1.md5sums.txt
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  925M May 29 13:51 SLX-19690.NEBNext37.HCT7YDRXY.s_1.r_1.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  954M May 29 13:52 SLX-19690.NEBNext37.HCT7YDRXY.s_1.r_2.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio   156 May 29 13:52 SLX-19690.NEBNext38.HCT7YDRXY.s_1.md5sums.txt
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  826M May 29 13:52 SLX-19690.NEBNext38.HCT7YDRXY.s_1.r_1.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  853M May 29 13:52 SLX-19690.NEBNext38.HCT7YDRXY.s_1.r_2.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio   156 May 29 13:52 SLX-19690.NEBNext39.HCT7YDRXY.s_1.md5sums.txt
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  808M May 29 13:53 SLX-19690.NEBNext39.HCT7YDRXY.s_1.r_1.fq.gz
# -rwxr-x--- 1 kzqv978 xem-scp-qbio  832M May 29 13:53 SLX-19690.NEBNext39.HCT7YDRXY.s_1.r_2.fq.gz

for fq in SLX-19690.NEBNext*.r_1.fq.gz; do echo $fq, `zcat $fq | paste - - - - | wc -l`; done
# SLX-19690.NEBNext09.HCT7YDRXY.s_1.r_1.fq.gz, 33411826
# SLX-19690.NEBNext10.HCT7YDRXY.s_1.r_1.fq.gz, 40739392
# SLX-19690.NEBNext11.HCT7YDRXY.s_1.r_1.fq.gz, 40605513
# SLX-19690.NEBNext12.HCT7YDRXY.s_1.r_1.fq.gz, 35213044
# SLX-19690.NEBNext15.HCT7YDRXY.s_1.r_1.fq.gz, 35643250
# SLX-19690.NEBNext19.HCT7YDRXY.s_1.r_1.fq.gz, 28226413
# SLX-19690.NEBNext20.HCT7YDRXY.s_1.r_1.fq.gz, 39157181
# SLX-19690.NEBNext21.HCT7YDRXY.s_1.r_1.fq.gz, 28858150
# SLX-19690.NEBNext31.HCT7YDRXY.s_1.r_1.fq.gz, 38420810
# SLX-19690.NEBNext32.HCT7YDRXY.s_1.r_1.fq.gz, 33340238
# SLX-19690.NEBNext33.HCT7YDRXY.s_1.r_1.fq.gz, 33998289
# SLX-19690.NEBNext34.HCT7YDRXY.s_1.r_1.fq.gz, 25911961
# SLX-19690.NEBNext36.HCT7YDRXY.s_1.r_1.fq.gz, 31476780
# SLX-19690.NEBNext37.HCT7YDRXY.s_1.r_1.fq.gz, 36089855
# SLX-19690.NEBNext38.HCT7YDRXY.s_1.r_1.fq.gz, 32113099
# SLX-19690.NEBNext39.HCT7YDRXY.s_1.r_1.fq.gz, 31415661
```



### quality check

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/chipseq2/fastq

mkdir ../fastqc

for fq in *.fq.gz
do
  bname=${fq%.fq.gz}
  sbatch -J $bname -o ../fastqc/$bname.log --mem 4G -t 1-0 --wrap "fastqc --noextract --nogroup -q -o ../fastqc $fq"
done
```

One of the cleanest sequencing runs that I have ever seen. High base quality scores and no adaptor contamination



### rename

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/chipseq2/fastq

cat SLX-19690.HCT7YDRXY.s_1.contents.csv
# Pool,Barcode,Sequence,Sample name
# SLX-19690,NEBNext09,GATCAG,"TAFwtK9"
# SLX-19690,NEBNext10,TAGCTT,"TAFkoK9"
# SLX-19690,NEBNext11,GGCTAC,"TAF5K9"
# SLX-19690,NEBNext12,CTTGTA,"TAF15K9"
# SLX-19690,NEBNext15,ATGTCA,"TAFwtIN"
# SLX-19690,NEBNext19,GTGAAA,"TAFkoIN"
# SLX-19690,NEBNext20,GTGGCC,"TAF5IN"
# SLX-19690,NEBNext21,GTTTCG,"TAF15IN"
# SLX-19690,NEBNext31,CACGAT,"L1wtK9"
# SLX-19690,NEBNext32,CACTCA,"L1koK9"
# SLX-19690,NEBNext33,CAGGCG,"L1wtsffvK9"
# SLX-19690,NEBNext34,CATGGC,"L1kosffvK9"
# SLX-19690,NEBNext36,CCAACA,"L1wtIN"
# SLX-19690,NEBNext37,CGGAAT,"L1koIN"
# SLX-19690,NEBNext38,CTAGCT,"L1wtsffvIN"
# SLX-19690,NEBNext39,CTATAC,"L1kosffvIN"

declare -A pairs=(\
[NEBNext09]=TAFwtK9 \
[NEBNext10]=TAFkoK9 \
[NEBNext11]=TAF5K9 \
[NEBNext12]=TAF15K9 \
[NEBNext15]=TAFwtIN \
[NEBNext19]=TAFkoIN \
[NEBNext20]=TAF5IN \
[NEBNext21]=TAF15IN \
[NEBNext31]=L1wtK9 \
[NEBNext32]=L1koK9 \
[NEBNext33]=L1wtsffvK9 \
[NEBNext34]=L1kosffvK9 \
[NEBNext36]=L1wtIN \
[NEBNext37]=L1koIN \
[NEBNext38]=L1wtsffvIN \
[NEBNext39]=L1kosffvIN)

for id in ${!pairs[@]}
do
  for filename in SLX-19690.${id}.HCT7YDRXY.s_1.r_{1,2}.fq.gz; do mv "$filename" "${pairs[$id]}.$filename"; done;
done

for fq in *.fq.gz; do echo $fq, `zcat $fq | paste - - - - | wc -l`; done
# L1koIN.SLX-19690.NEBNext37.HCT7YDRXY.s_1.r_1.fq.gz, 36089855
# L1koK9.SLX-19690.NEBNext32.HCT7YDRXY.s_1.r_1.fq.gz, 33340238
# L1kosffvIN.SLX-19690.NEBNext39.HCT7YDRXY.s_1.r_1.fq.gz, 31415661
# L1kosffvK9.SLX-19690.NEBNext34.HCT7YDRXY.s_1.r_1.fq.gz, 25911961
# L1wtIN.SLX-19690.NEBNext36.HCT7YDRXY.s_1.r_1.fq.gz, 31476780
# L1wtK9.SLX-19690.NEBNext31.HCT7YDRXY.s_1.r_1.fq.gz, 38420810
# L1wtsffvIN.SLX-19690.NEBNext38.HCT7YDRXY.s_1.r_1.fq.gz, 32113099
# L1wtsffvK9.SLX-19690.NEBNext33.HCT7YDRXY.s_1.r_1.fq.gz, 33998289
# TAF15IN.SLX-19690.NEBNext21.HCT7YDRXY.s_1.r_1.fq.gz, 28858150
# TAF15K9.SLX-19690.NEBNext12.HCT7YDRXY.s_1.r_1.fq.gz, 35213044
# TAF5IN.SLX-19690.NEBNext20.HCT7YDRXY.s_1.r_1.fq.gz, 39157181
# TAF5K9.SLX-19690.NEBNext11.HCT7YDRXY.s_1.r_1.fq.gz, 40605513
# TAFkoIN.SLX-19690.NEBNext19.HCT7YDRXY.s_1.r_1.fq.gz, 28226413
# TAFkoK9.SLX-19690.NEBNext10.HCT7YDRXY.s_1.r_1.fq.gz, 40739392
# TAFwtIN.SLX-19690.NEBNext15.HCT7YDRXY.s_1.r_1.fq.gz, 35643250
# TAFwtK9.SLX-19690.NEBNext09.HCT7YDRXY.s_1.r_1.fq.gz, 33411826
```



### cutadapt

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/chipseq2/fastq

mkdir ../fastq_trimmed

for fq1 in *.SLX-19690.NEBNext*.r_1.fq.gz
do
  bname=${fq1%.r_1.fq.gz}
  fq2=${fq1/.r_1./.r_2.}
  #echo $fq1, $fq2, $bname
  sbatch -J $bname -o ../fastq_trimmed/$bname.log --mem 8G -t 1-0 --wrap "cutadapt -a AGATCGGAAGAGC -A AGATCGGAAGAGC -m 15 -q 20 -o ../fastq_trimmed/$fq1 -p ../fastq_trimmed/$fq2 $fq1 $fq2 > ../fastq_trimmed/${bname}.txt"
done

cd ../fastq_trimmed

tail *.log

grep -A 16 "Summary" *.txt
# L1koIN.SLX-19690.NEBNext37.HCT7YDRXY.s_1.txt:=== Summary ===
# L1koIN.SLX-19690.NEBNext37.HCT7YDRXY.s_1.txt-
# L1koIN.SLX-19690.NEBNext37.HCT7YDRXY.s_1.txt-Total read pairs processed:         36,089,855
# L1koIN.SLX-19690.NEBNext37.HCT7YDRXY.s_1.txt-  Read 1 with adapter:               1,022,897 (2.8%)
# L1koIN.SLX-19690.NEBNext37.HCT7YDRXY.s_1.txt-  Read 2 with adapter:               1,012,327 (2.8%)
# L1koIN.SLX-19690.NEBNext37.HCT7YDRXY.s_1.txt-Pairs that were too short:              22,239 (0.1%)
# L1koIN.SLX-19690.NEBNext37.HCT7YDRXY.s_1.txt-Pairs written (passing filters):    36,067,616 (99.9%)
# L1koIN.SLX-19690.NEBNext37.HCT7YDRXY.s_1.txt-
# L1koIN.SLX-19690.NEBNext37.HCT7YDRXY.s_1.txt-Total basepairs processed: 3,608,985,500 bp
# L1koIN.SLX-19690.NEBNext37.HCT7YDRXY.s_1.txt-  Read 1: 1,804,492,750 bp
# L1koIN.SLX-19690.NEBNext37.HCT7YDRXY.s_1.txt-  Read 2: 1,804,492,750 bp
# L1koIN.SLX-19690.NEBNext37.HCT7YDRXY.s_1.txt-Quality-trimmed:               3,040,076 bp (0.1%)
# L1koIN.SLX-19690.NEBNext37.HCT7YDRXY.s_1.txt-  Read 1:       942,918 bp
# L1koIN.SLX-19690.NEBNext37.HCT7YDRXY.s_1.txt-  Read 2:     2,097,158 bp
# L1koIN.SLX-19690.NEBNext37.HCT7YDRXY.s_1.txt-Total written (filtered):  3,597,715,833 bp (99.7%)
# L1koIN.SLX-19690.NEBNext37.HCT7YDRXY.s_1.txt-  Read 1: 1,799,096,000 bp
# L1koIN.SLX-19690.NEBNext37.HCT7YDRXY.s_1.txt-  Read 2: 1,798,619,833 bp
# --
# L1koK9.SLX-19690.NEBNext32.HCT7YDRXY.s_1.txt:=== Summary ===
# L1koK9.SLX-19690.NEBNext32.HCT7YDRXY.s_1.txt-
# L1koK9.SLX-19690.NEBNext32.HCT7YDRXY.s_1.txt-Total read pairs processed:         33,340,238
# L1koK9.SLX-19690.NEBNext32.HCT7YDRXY.s_1.txt-  Read 1 with adapter:                 930,443 (2.8%)
# L1koK9.SLX-19690.NEBNext32.HCT7YDRXY.s_1.txt-  Read 2 with adapter:                 922,821 (2.8%)
# L1koK9.SLX-19690.NEBNext32.HCT7YDRXY.s_1.txt-Pairs that were too short:              21,251 (0.1%)
# L1koK9.SLX-19690.NEBNext32.HCT7YDRXY.s_1.txt-Pairs written (passing filters):    33,318,987 (99.9%)
# L1koK9.SLX-19690.NEBNext32.HCT7YDRXY.s_1.txt-
# L1koK9.SLX-19690.NEBNext32.HCT7YDRXY.s_1.txt-Total basepairs processed: 3,334,023,800 bp
# L1koK9.SLX-19690.NEBNext32.HCT7YDRXY.s_1.txt-  Read 1: 1,667,011,900 bp
# L1koK9.SLX-19690.NEBNext32.HCT7YDRXY.s_1.txt-  Read 2: 1,667,011,900 bp
# L1koK9.SLX-19690.NEBNext32.HCT7YDRXY.s_1.txt-Quality-trimmed:               3,127,958 bp (0.1%)
# L1koK9.SLX-19690.NEBNext32.HCT7YDRXY.s_1.txt-  Read 1:       954,479 bp
# L1koK9.SLX-19690.NEBNext32.HCT7YDRXY.s_1.txt-  Read 2:     2,173,479 bp
# L1koK9.SLX-19690.NEBNext32.HCT7YDRXY.s_1.txt-Total written (filtered):  3,323,404,706 bp (99.7%)
# L1koK9.SLX-19690.NEBNext32.HCT7YDRXY.s_1.txt-  Read 1: 1,661,998,889 bp
# L1koK9.SLX-19690.NEBNext32.HCT7YDRXY.s_1.txt-  Read 2: 1,661,405,817 bp
# --
# L1kosffvIN.SLX-19690.NEBNext39.HCT7YDRXY.s_1.txt:=== Summary ===
# L1kosffvIN.SLX-19690.NEBNext39.HCT7YDRXY.s_1.txt-
# L1kosffvIN.SLX-19690.NEBNext39.HCT7YDRXY.s_1.txt-Total read pairs processed:         31,415,661
# L1kosffvIN.SLX-19690.NEBNext39.HCT7YDRXY.s_1.txt-  Read 1 with adapter:                 890,172 (2.8%)
# L1kosffvIN.SLX-19690.NEBNext39.HCT7YDRXY.s_1.txt-  Read 2 with adapter:                 880,811 (2.8%)
# L1kosffvIN.SLX-19690.NEBNext39.HCT7YDRXY.s_1.txt-Pairs that were too short:              18,575 (0.1%)
# L1kosffvIN.SLX-19690.NEBNext39.HCT7YDRXY.s_1.txt-Pairs written (passing filters):    31,397,086 (99.9%)
# L1kosffvIN.SLX-19690.NEBNext39.HCT7YDRXY.s_1.txt-
# L1kosffvIN.SLX-19690.NEBNext39.HCT7YDRXY.s_1.txt-Total basepairs processed: 3,141,566,100 bp
# L1kosffvIN.SLX-19690.NEBNext39.HCT7YDRXY.s_1.txt-  Read 1: 1,570,783,050 bp
# L1kosffvIN.SLX-19690.NEBNext39.HCT7YDRXY.s_1.txt-  Read 2: 1,570,783,050 bp
# L1kosffvIN.SLX-19690.NEBNext39.HCT7YDRXY.s_1.txt-Quality-trimmed:               2,629,263 bp (0.1%)
# L1kosffvIN.SLX-19690.NEBNext39.HCT7YDRXY.s_1.txt-  Read 1:       854,272 bp
# L1kosffvIN.SLX-19690.NEBNext39.HCT7YDRXY.s_1.txt-  Read 2:     1,774,991 bp
# L1kosffvIN.SLX-19690.NEBNext39.HCT7YDRXY.s_1.txt-Total written (filtered):  3,131,816,225 bp (99.7%)
# L1kosffvIN.SLX-19690.NEBNext39.HCT7YDRXY.s_1.txt-  Read 1: 1,566,097,180 bp
# L1kosffvIN.SLX-19690.NEBNext39.HCT7YDRXY.s_1.txt-  Read 2: 1,565,719,045 bp
# --
# L1kosffvK9.SLX-19690.NEBNext34.HCT7YDRXY.s_1.txt:=== Summary ===
# L1kosffvK9.SLX-19690.NEBNext34.HCT7YDRXY.s_1.txt-
# L1kosffvK9.SLX-19690.NEBNext34.HCT7YDRXY.s_1.txt-Total read pairs processed:         25,911,961
# L1kosffvK9.SLX-19690.NEBNext34.HCT7YDRXY.s_1.txt-  Read 1 with adapter:                 721,614 (2.8%)
# L1kosffvK9.SLX-19690.NEBNext34.HCT7YDRXY.s_1.txt-  Read 2 with adapter:                 712,774 (2.8%)
# L1kosffvK9.SLX-19690.NEBNext34.HCT7YDRXY.s_1.txt-Pairs that were too short:              18,180 (0.1%)
# L1kosffvK9.SLX-19690.NEBNext34.HCT7YDRXY.s_1.txt-Pairs written (passing filters):    25,893,781 (99.9%)
# L1kosffvK9.SLX-19690.NEBNext34.HCT7YDRXY.s_1.txt-
# L1kosffvK9.SLX-19690.NEBNext34.HCT7YDRXY.s_1.txt-Total basepairs processed: 2,591,196,100 bp
# L1kosffvK9.SLX-19690.NEBNext34.HCT7YDRXY.s_1.txt-  Read 1: 1,295,598,050 bp
# L1kosffvK9.SLX-19690.NEBNext34.HCT7YDRXY.s_1.txt-  Read 2: 1,295,598,050 bp
# L1kosffvK9.SLX-19690.NEBNext34.HCT7YDRXY.s_1.txt-Quality-trimmed:               2,492,599 bp (0.1%)
# L1kosffvK9.SLX-19690.NEBNext34.HCT7YDRXY.s_1.txt-  Read 1:       710,321 bp
# L1kosffvK9.SLX-19690.NEBNext34.HCT7YDRXY.s_1.txt-  Read 2:     1,782,278 bp
# L1kosffvK9.SLX-19690.NEBNext34.HCT7YDRXY.s_1.txt-Total written (filtered):  2,582,792,260 bp (99.7%)
# L1kosffvK9.SLX-19690.NEBNext34.HCT7YDRXY.s_1.txt-  Read 1: 1,291,638,500 bp
# L1kosffvK9.SLX-19690.NEBNext34.HCT7YDRXY.s_1.txt-  Read 2: 1,291,153,760 bp
# --
# L1wtIN.SLX-19690.NEBNext36.HCT7YDRXY.s_1.txt:=== Summary ===
# L1wtIN.SLX-19690.NEBNext36.HCT7YDRXY.s_1.txt-
# L1wtIN.SLX-19690.NEBNext36.HCT7YDRXY.s_1.txt-Total read pairs processed:         31,476,780
# L1wtIN.SLX-19690.NEBNext36.HCT7YDRXY.s_1.txt-  Read 1 with adapter:                 885,241 (2.8%)
# L1wtIN.SLX-19690.NEBNext36.HCT7YDRXY.s_1.txt-  Read 2 with adapter:                 882,267 (2.8%)
# L1wtIN.SLX-19690.NEBNext36.HCT7YDRXY.s_1.txt-Pairs that were too short:              15,669 (0.0%)
# L1wtIN.SLX-19690.NEBNext36.HCT7YDRXY.s_1.txt-Pairs written (passing filters):    31,461,111 (100.0%)
# L1wtIN.SLX-19690.NEBNext36.HCT7YDRXY.s_1.txt-
# L1wtIN.SLX-19690.NEBNext36.HCT7YDRXY.s_1.txt-Total basepairs processed: 3,147,678,000 bp
# L1wtIN.SLX-19690.NEBNext36.HCT7YDRXY.s_1.txt-  Read 1: 1,573,839,000 bp
# L1wtIN.SLX-19690.NEBNext36.HCT7YDRXY.s_1.txt-  Read 2: 1,573,839,000 bp
# L1wtIN.SLX-19690.NEBNext36.HCT7YDRXY.s_1.txt-Quality-trimmed:               2,615,334 bp (0.1%)
# L1wtIN.SLX-19690.NEBNext36.HCT7YDRXY.s_1.txt-  Read 1:       837,416 bp
# L1wtIN.SLX-19690.NEBNext36.HCT7YDRXY.s_1.txt-  Read 2:     1,777,918 bp
# L1wtIN.SLX-19690.NEBNext36.HCT7YDRXY.s_1.txt-Total written (filtered):  3,138,209,391 bp (99.7%)
# L1wtIN.SLX-19690.NEBNext36.HCT7YDRXY.s_1.txt-  Read 1: 1,569,322,686 bp
# L1wtIN.SLX-19690.NEBNext36.HCT7YDRXY.s_1.txt-  Read 2: 1,568,886,705 bp
# --
# L1wtK9.SLX-19690.NEBNext31.HCT7YDRXY.s_1.txt:=== Summary ===
# L1wtK9.SLX-19690.NEBNext31.HCT7YDRXY.s_1.txt-
# L1wtK9.SLX-19690.NEBNext31.HCT7YDRXY.s_1.txt-Total read pairs processed:         38,420,810
# L1wtK9.SLX-19690.NEBNext31.HCT7YDRXY.s_1.txt-  Read 1 with adapter:               1,069,947 (2.8%)
# L1wtK9.SLX-19690.NEBNext31.HCT7YDRXY.s_1.txt-  Read 2 with adapter:               1,062,412 (2.8%)
# L1wtK9.SLX-19690.NEBNext31.HCT7YDRXY.s_1.txt-Pairs that were too short:              16,598 (0.0%)
# L1wtK9.SLX-19690.NEBNext31.HCT7YDRXY.s_1.txt-Pairs written (passing filters):    38,404,212 (100.0%)
# L1wtK9.SLX-19690.NEBNext31.HCT7YDRXY.s_1.txt-
# L1wtK9.SLX-19690.NEBNext31.HCT7YDRXY.s_1.txt-Total basepairs processed: 3,842,081,000 bp
# L1wtK9.SLX-19690.NEBNext31.HCT7YDRXY.s_1.txt-  Read 1: 1,921,040,500 bp
# L1wtK9.SLX-19690.NEBNext31.HCT7YDRXY.s_1.txt-  Read 2: 1,921,040,500 bp
# L1wtK9.SLX-19690.NEBNext31.HCT7YDRXY.s_1.txt-Quality-trimmed:               3,097,310 bp (0.1%)
# L1wtK9.SLX-19690.NEBNext31.HCT7YDRXY.s_1.txt-  Read 1:     1,020,150 bp
# L1wtK9.SLX-19690.NEBNext31.HCT7YDRXY.s_1.txt-  Read 2:     2,077,160 bp
# L1wtK9.SLX-19690.NEBNext31.HCT7YDRXY.s_1.txt-Total written (filtered):  3,830,907,211 bp (99.7%)
# L1wtK9.SLX-19690.NEBNext31.HCT7YDRXY.s_1.txt-  Read 1: 1,915,700,631 bp
# L1wtK9.SLX-19690.NEBNext31.HCT7YDRXY.s_1.txt-  Read 2: 1,915,206,580 bp
# --
# L1wtsffvIN.SLX-19690.NEBNext38.HCT7YDRXY.s_1.txt:=== Summary ===
# L1wtsffvIN.SLX-19690.NEBNext38.HCT7YDRXY.s_1.txt-
# L1wtsffvIN.SLX-19690.NEBNext38.HCT7YDRXY.s_1.txt-Total read pairs processed:         32,113,099
# L1wtsffvIN.SLX-19690.NEBNext38.HCT7YDRXY.s_1.txt-  Read 1 with adapter:                 915,025 (2.8%)
# L1wtsffvIN.SLX-19690.NEBNext38.HCT7YDRXY.s_1.txt-  Read 2 with adapter:                 900,591 (2.8%)
# L1wtsffvIN.SLX-19690.NEBNext38.HCT7YDRXY.s_1.txt-Pairs that were too short:              23,879 (0.1%)
# L1wtsffvIN.SLX-19690.NEBNext38.HCT7YDRXY.s_1.txt-Pairs written (passing filters):    32,089,220 (99.9%)
# L1wtsffvIN.SLX-19690.NEBNext38.HCT7YDRXY.s_1.txt-
# L1wtsffvIN.SLX-19690.NEBNext38.HCT7YDRXY.s_1.txt-Total basepairs processed: 3,211,309,900 bp
# L1wtsffvIN.SLX-19690.NEBNext38.HCT7YDRXY.s_1.txt-  Read 1: 1,605,654,950 bp
# L1wtsffvIN.SLX-19690.NEBNext38.HCT7YDRXY.s_1.txt-  Read 2: 1,605,654,950 bp
# L1wtsffvIN.SLX-19690.NEBNext38.HCT7YDRXY.s_1.txt-Quality-trimmed:               2,850,883 bp (0.1%)
# L1wtsffvIN.SLX-19690.NEBNext38.HCT7YDRXY.s_1.txt-  Read 1:       854,321 bp
# L1wtsffvIN.SLX-19690.NEBNext38.HCT7YDRXY.s_1.txt-  Read 2:     1,996,562 bp
# L1wtsffvIN.SLX-19690.NEBNext38.HCT7YDRXY.s_1.txt-Total written (filtered):  3,200,826,203 bp (99.7%)
# L1wtsffvIN.SLX-19690.NEBNext38.HCT7YDRXY.s_1.txt-  Read 1: 1,600,634,694 bp
# L1wtsffvIN.SLX-19690.NEBNext38.HCT7YDRXY.s_1.txt-  Read 2: 1,600,191,509 bp
# --
# L1wtsffvK9.SLX-19690.NEBNext33.HCT7YDRXY.s_1.txt:=== Summary ===
# L1wtsffvK9.SLX-19690.NEBNext33.HCT7YDRXY.s_1.txt-
# L1wtsffvK9.SLX-19690.NEBNext33.HCT7YDRXY.s_1.txt-Total read pairs processed:         33,998,289
# L1wtsffvK9.SLX-19690.NEBNext33.HCT7YDRXY.s_1.txt-  Read 1 with adapter:                 947,464 (2.8%)
# L1wtsffvK9.SLX-19690.NEBNext33.HCT7YDRXY.s_1.txt-  Read 2 with adapter:                 936,062 (2.8%)
# L1wtsffvK9.SLX-19690.NEBNext33.HCT7YDRXY.s_1.txt-Pairs that were too short:              22,630 (0.1%)
# L1wtsffvK9.SLX-19690.NEBNext33.HCT7YDRXY.s_1.txt-Pairs written (passing filters):    33,975,659 (99.9%)
# L1wtsffvK9.SLX-19690.NEBNext33.HCT7YDRXY.s_1.txt-
# L1wtsffvK9.SLX-19690.NEBNext33.HCT7YDRXY.s_1.txt-Total basepairs processed: 3,399,828,900 bp
# L1wtsffvK9.SLX-19690.NEBNext33.HCT7YDRXY.s_1.txt-  Read 1: 1,699,914,450 bp
# L1wtsffvK9.SLX-19690.NEBNext33.HCT7YDRXY.s_1.txt-  Read 2: 1,699,914,450 bp
# L1wtsffvK9.SLX-19690.NEBNext33.HCT7YDRXY.s_1.txt-Quality-trimmed:               3,104,387 bp (0.1%)
# L1wtsffvK9.SLX-19690.NEBNext33.HCT7YDRXY.s_1.txt-  Read 1:       948,369 bp
# L1wtsffvK9.SLX-19690.NEBNext33.HCT7YDRXY.s_1.txt-  Read 2:     2,156,018 bp
# L1wtsffvK9.SLX-19690.NEBNext33.HCT7YDRXY.s_1.txt-Total written (filtered):  3,389,039,666 bp (99.7%)
# L1wtsffvK9.SLX-19690.NEBNext33.HCT7YDRXY.s_1.txt-  Read 1: 1,694,770,124 bp
# L1wtsffvK9.SLX-19690.NEBNext33.HCT7YDRXY.s_1.txt-  Read 2: 1,694,269,542 bp
# --
# TAF15IN.SLX-19690.NEBNext21.HCT7YDRXY.s_1.txt:=== Summary ===
# TAF15IN.SLX-19690.NEBNext21.HCT7YDRXY.s_1.txt-
# TAF15IN.SLX-19690.NEBNext21.HCT7YDRXY.s_1.txt-Total read pairs processed:         28,858,150
# TAF15IN.SLX-19690.NEBNext21.HCT7YDRXY.s_1.txt-  Read 1 with adapter:                 817,268 (2.8%)
# TAF15IN.SLX-19690.NEBNext21.HCT7YDRXY.s_1.txt-  Read 2 with adapter:                 809,409 (2.8%)
# TAF15IN.SLX-19690.NEBNext21.HCT7YDRXY.s_1.txt-Pairs that were too short:              19,699 (0.1%)
# TAF15IN.SLX-19690.NEBNext21.HCT7YDRXY.s_1.txt-Pairs written (passing filters):    28,838,451 (99.9%)
# TAF15IN.SLX-19690.NEBNext21.HCT7YDRXY.s_1.txt-
# TAF15IN.SLX-19690.NEBNext21.HCT7YDRXY.s_1.txt-Total basepairs processed: 2,885,815,000 bp
# TAF15IN.SLX-19690.NEBNext21.HCT7YDRXY.s_1.txt-  Read 1: 1,442,907,500 bp
# TAF15IN.SLX-19690.NEBNext21.HCT7YDRXY.s_1.txt-  Read 2: 1,442,907,500 bp
# TAF15IN.SLX-19690.NEBNext21.HCT7YDRXY.s_1.txt-Quality-trimmed:               2,695,624 bp (0.1%)
# TAF15IN.SLX-19690.NEBNext21.HCT7YDRXY.s_1.txt-  Read 1:       788,171 bp
# TAF15IN.SLX-19690.NEBNext21.HCT7YDRXY.s_1.txt-  Read 2:     1,907,453 bp
# TAF15IN.SLX-19690.NEBNext21.HCT7YDRXY.s_1.txt-Total written (filtered):  2,876,426,579 bp (99.7%)
# TAF15IN.SLX-19690.NEBNext21.HCT7YDRXY.s_1.txt-  Read 1: 1,438,492,896 bp
# TAF15IN.SLX-19690.NEBNext21.HCT7YDRXY.s_1.txt-  Read 2: 1,437,933,683 bp
# --
# TAF15K9.SLX-19690.NEBNext12.HCT7YDRXY.s_1.txt:=== Summary ===
# TAF15K9.SLX-19690.NEBNext12.HCT7YDRXY.s_1.txt-
# TAF15K9.SLX-19690.NEBNext12.HCT7YDRXY.s_1.txt-Total read pairs processed:         35,213,044
# TAF15K9.SLX-19690.NEBNext12.HCT7YDRXY.s_1.txt-  Read 1 with adapter:                 983,439 (2.8%)
# TAF15K9.SLX-19690.NEBNext12.HCT7YDRXY.s_1.txt-  Read 2 with adapter:                 972,347 (2.8%)
# TAF15K9.SLX-19690.NEBNext12.HCT7YDRXY.s_1.txt-Pairs that were too short:              25,301 (0.1%)
# TAF15K9.SLX-19690.NEBNext12.HCT7YDRXY.s_1.txt-Pairs written (passing filters):    35,187,743 (99.9%)
# TAF15K9.SLX-19690.NEBNext12.HCT7YDRXY.s_1.txt-
# TAF15K9.SLX-19690.NEBNext12.HCT7YDRXY.s_1.txt-Total basepairs processed: 3,521,304,400 bp
# TAF15K9.SLX-19690.NEBNext12.HCT7YDRXY.s_1.txt-  Read 1: 1,760,652,200 bp
# TAF15K9.SLX-19690.NEBNext12.HCT7YDRXY.s_1.txt-  Read 2: 1,760,652,200 bp
# TAF15K9.SLX-19690.NEBNext12.HCT7YDRXY.s_1.txt-Quality-trimmed:               3,640,484 bp (0.1%)
# TAF15K9.SLX-19690.NEBNext12.HCT7YDRXY.s_1.txt-  Read 1:       948,649 bp
# TAF15K9.SLX-19690.NEBNext12.HCT7YDRXY.s_1.txt-  Read 2:     2,691,835 bp
# TAF15K9.SLX-19690.NEBNext12.HCT7YDRXY.s_1.txt-Total written (filtered):  3,509,598,216 bp (99.7%)
# TAF15K9.SLX-19690.NEBNext12.HCT7YDRXY.s_1.txt-  Read 1: 1,755,260,124 bp
# TAF15K9.SLX-19690.NEBNext12.HCT7YDRXY.s_1.txt-  Read 2: 1,754,338,092 bp
# --
# TAF5IN.SLX-19690.NEBNext20.HCT7YDRXY.s_1.txt:=== Summary ===
# TAF5IN.SLX-19690.NEBNext20.HCT7YDRXY.s_1.txt-
# TAF5IN.SLX-19690.NEBNext20.HCT7YDRXY.s_1.txt-Total read pairs processed:         39,157,181
# TAF5IN.SLX-19690.NEBNext20.HCT7YDRXY.s_1.txt-  Read 1 with adapter:               1,100,818 (2.8%)
# TAF5IN.SLX-19690.NEBNext20.HCT7YDRXY.s_1.txt-  Read 2 with adapter:               1,099,286 (2.8%)
# TAF5IN.SLX-19690.NEBNext20.HCT7YDRXY.s_1.txt-Pairs that were too short:              16,735 (0.0%)
# TAF5IN.SLX-19690.NEBNext20.HCT7YDRXY.s_1.txt-Pairs written (passing filters):    39,140,446 (100.0%)
# TAF5IN.SLX-19690.NEBNext20.HCT7YDRXY.s_1.txt-
# TAF5IN.SLX-19690.NEBNext20.HCT7YDRXY.s_1.txt-Total basepairs processed: 3,915,718,100 bp
# TAF5IN.SLX-19690.NEBNext20.HCT7YDRXY.s_1.txt-  Read 1: 1,957,859,050 bp
# TAF5IN.SLX-19690.NEBNext20.HCT7YDRXY.s_1.txt-  Read 2: 1,957,859,050 bp
# TAF5IN.SLX-19690.NEBNext20.HCT7YDRXY.s_1.txt-Quality-trimmed:               3,344,868 bp (0.1%)
# TAF5IN.SLX-19690.NEBNext20.HCT7YDRXY.s_1.txt-  Read 1:     1,062,584 bp
# TAF5IN.SLX-19690.NEBNext20.HCT7YDRXY.s_1.txt-  Read 2:     2,282,284 bp
# TAF5IN.SLX-19690.NEBNext20.HCT7YDRXY.s_1.txt-Total written (filtered):  3,904,065,366 bp (99.7%)
# TAF5IN.SLX-19690.NEBNext20.HCT7YDRXY.s_1.txt-  Read 1: 1,952,350,264 bp
# TAF5IN.SLX-19690.NEBNext20.HCT7YDRXY.s_1.txt-  Read 2: 1,951,715,102 bp
# --
# TAF5K9.SLX-19690.NEBNext11.HCT7YDRXY.s_1.txt:=== Summary ===
# TAF5K9.SLX-19690.NEBNext11.HCT7YDRXY.s_1.txt-
# TAF5K9.SLX-19690.NEBNext11.HCT7YDRXY.s_1.txt-Total read pairs processed:         40,605,513
# TAF5K9.SLX-19690.NEBNext11.HCT7YDRXY.s_1.txt-  Read 1 with adapter:               1,121,760 (2.8%)
# TAF5K9.SLX-19690.NEBNext11.HCT7YDRXY.s_1.txt-  Read 2 with adapter:               1,122,422 (2.8%)
# TAF5K9.SLX-19690.NEBNext11.HCT7YDRXY.s_1.txt-Pairs that were too short:              17,974 (0.0%)
# TAF5K9.SLX-19690.NEBNext11.HCT7YDRXY.s_1.txt-Pairs written (passing filters):    40,587,539 (100.0%)
# TAF5K9.SLX-19690.NEBNext11.HCT7YDRXY.s_1.txt-
# TAF5K9.SLX-19690.NEBNext11.HCT7YDRXY.s_1.txt-Total basepairs processed: 4,060,551,300 bp
# TAF5K9.SLX-19690.NEBNext11.HCT7YDRXY.s_1.txt-  Read 1: 2,030,275,650 bp
# TAF5K9.SLX-19690.NEBNext11.HCT7YDRXY.s_1.txt-  Read 2: 2,030,275,650 bp
# TAF5K9.SLX-19690.NEBNext11.HCT7YDRXY.s_1.txt-Quality-trimmed:               3,893,506 bp (0.1%)
# TAF5K9.SLX-19690.NEBNext11.HCT7YDRXY.s_1.txt-  Read 1:     1,096,581 bp
# TAF5K9.SLX-19690.NEBNext11.HCT7YDRXY.s_1.txt-  Read 2:     2,796,925 bp
# TAF5K9.SLX-19690.NEBNext11.HCT7YDRXY.s_1.txt-Total written (filtered):  4,048,193,475 bp (99.7%)
# TAF5K9.SLX-19690.NEBNext11.HCT7YDRXY.s_1.txt-  Read 1: 2,024,609,721 bp
# TAF5K9.SLX-19690.NEBNext11.HCT7YDRXY.s_1.txt-  Read 2: 2,023,583,754 bp
# --
# TAFkoIN.SLX-19690.NEBNext19.HCT7YDRXY.s_1.txt:=== Summary ===
# TAFkoIN.SLX-19690.NEBNext19.HCT7YDRXY.s_1.txt-
# TAFkoIN.SLX-19690.NEBNext19.HCT7YDRXY.s_1.txt-Total read pairs processed:         28,226,413
# TAFkoIN.SLX-19690.NEBNext19.HCT7YDRXY.s_1.txt-  Read 1 with adapter:                 793,608 (2.8%)
# TAFkoIN.SLX-19690.NEBNext19.HCT7YDRXY.s_1.txt-  Read 2 with adapter:                 792,042 (2.8%)
# TAFkoIN.SLX-19690.NEBNext19.HCT7YDRXY.s_1.txt-Pairs that were too short:              13,366 (0.0%)
# TAFkoIN.SLX-19690.NEBNext19.HCT7YDRXY.s_1.txt-Pairs written (passing filters):    28,213,047 (100.0%)
# TAFkoIN.SLX-19690.NEBNext19.HCT7YDRXY.s_1.txt-
# TAFkoIN.SLX-19690.NEBNext19.HCT7YDRXY.s_1.txt-Total basepairs processed: 2,822,641,300 bp
# TAFkoIN.SLX-19690.NEBNext19.HCT7YDRXY.s_1.txt-  Read 1: 1,411,320,650 bp
# TAFkoIN.SLX-19690.NEBNext19.HCT7YDRXY.s_1.txt-  Read 2: 1,411,320,650 bp
# TAFkoIN.SLX-19690.NEBNext19.HCT7YDRXY.s_1.txt-Quality-trimmed:               2,467,961 bp (0.1%)
# TAFkoIN.SLX-19690.NEBNext19.HCT7YDRXY.s_1.txt-  Read 1:       773,740 bp
# TAFkoIN.SLX-19690.NEBNext19.HCT7YDRXY.s_1.txt-  Read 2:     1,694,221 bp
# TAFkoIN.SLX-19690.NEBNext19.HCT7YDRXY.s_1.txt-Total written (filtered):  2,814,086,558 bp (99.7%)
# TAFkoIN.SLX-19690.NEBNext19.HCT7YDRXY.s_1.txt-  Read 1: 1,407,282,190 bp
# TAFkoIN.SLX-19690.NEBNext19.HCT7YDRXY.s_1.txt-  Read 2: 1,406,804,368 bp
# --
# TAFkoK9.SLX-19690.NEBNext10.HCT7YDRXY.s_1.txt:=== Summary ===
# TAFkoK9.SLX-19690.NEBNext10.HCT7YDRXY.s_1.txt-
# TAFkoK9.SLX-19690.NEBNext10.HCT7YDRXY.s_1.txt-Total read pairs processed:         40,739,392
# TAFkoK9.SLX-19690.NEBNext10.HCT7YDRXY.s_1.txt-  Read 1 with adapter:               1,122,930 (2.8%)
# TAFkoK9.SLX-19690.NEBNext10.HCT7YDRXY.s_1.txt-  Read 2 with adapter:               1,124,121 (2.8%)
# TAFkoK9.SLX-19690.NEBNext10.HCT7YDRXY.s_1.txt-Pairs that were too short:              15,404 (0.0%)
# TAFkoK9.SLX-19690.NEBNext10.HCT7YDRXY.s_1.txt-Pairs written (passing filters):    40,723,988 (100.0%)
# TAFkoK9.SLX-19690.NEBNext10.HCT7YDRXY.s_1.txt-
# TAFkoK9.SLX-19690.NEBNext10.HCT7YDRXY.s_1.txt-Total basepairs processed: 4,073,939,200 bp
# TAFkoK9.SLX-19690.NEBNext10.HCT7YDRXY.s_1.txt-  Read 1: 2,036,969,600 bp
# TAFkoK9.SLX-19690.NEBNext10.HCT7YDRXY.s_1.txt-  Read 2: 2,036,969,600 bp
# TAFkoK9.SLX-19690.NEBNext10.HCT7YDRXY.s_1.txt-Quality-trimmed:               3,579,510 bp (0.1%)
# TAFkoK9.SLX-19690.NEBNext10.HCT7YDRXY.s_1.txt-  Read 1:     1,074,664 bp
# TAFkoK9.SLX-19690.NEBNext10.HCT7YDRXY.s_1.txt-  Read 2:     2,504,846 bp
# TAFkoK9.SLX-19690.NEBNext10.HCT7YDRXY.s_1.txt-Total written (filtered):  4,062,034,940 bp (99.7%)
# TAFkoK9.SLX-19690.NEBNext10.HCT7YDRXY.s_1.txt-  Read 1: 2,031,450,653 bp
# TAFkoK9.SLX-19690.NEBNext10.HCT7YDRXY.s_1.txt-  Read 2: 2,030,584,287 bp
# --
# TAFwtIN.SLX-19690.NEBNext15.HCT7YDRXY.s_1.txt:=== Summary ===
# TAFwtIN.SLX-19690.NEBNext15.HCT7YDRXY.s_1.txt-
# TAFwtIN.SLX-19690.NEBNext15.HCT7YDRXY.s_1.txt-Total read pairs processed:         35,643,250
# TAFwtIN.SLX-19690.NEBNext15.HCT7YDRXY.s_1.txt-  Read 1 with adapter:               1,027,411 (2.9%)
# TAFwtIN.SLX-19690.NEBNext15.HCT7YDRXY.s_1.txt-  Read 2 with adapter:               1,007,442 (2.8%)
# TAFwtIN.SLX-19690.NEBNext15.HCT7YDRXY.s_1.txt-Pairs that were too short:              33,162 (0.1%)
# TAFwtIN.SLX-19690.NEBNext15.HCT7YDRXY.s_1.txt-Pairs written (passing filters):    35,610,088 (99.9%)
# TAFwtIN.SLX-19690.NEBNext15.HCT7YDRXY.s_1.txt-
# TAFwtIN.SLX-19690.NEBNext15.HCT7YDRXY.s_1.txt-Total basepairs processed: 3,564,325,000 bp
# TAFwtIN.SLX-19690.NEBNext15.HCT7YDRXY.s_1.txt-  Read 1: 1,782,162,500 bp
# TAFwtIN.SLX-19690.NEBNext15.HCT7YDRXY.s_1.txt-  Read 2: 1,782,162,500 bp
# TAFwtIN.SLX-19690.NEBNext15.HCT7YDRXY.s_1.txt-Quality-trimmed:               3,646,558 bp (0.1%)
# TAFwtIN.SLX-19690.NEBNext15.HCT7YDRXY.s_1.txt-  Read 1:       993,654 bp
# TAFwtIN.SLX-19690.NEBNext15.HCT7YDRXY.s_1.txt-  Read 2:     2,652,904 bp
# TAFwtIN.SLX-19690.NEBNext15.HCT7YDRXY.s_1.txt-Total written (filtered):  3,551,512,719 bp (99.6%)
# TAFwtIN.SLX-19690.NEBNext15.HCT7YDRXY.s_1.txt-  Read 1: 1,776,163,250 bp
# TAFwtIN.SLX-19690.NEBNext15.HCT7YDRXY.s_1.txt-  Read 2: 1,775,349,469 bp
# --
# TAFwtK9.SLX-19690.NEBNext09.HCT7YDRXY.s_1.txt:=== Summary ===
# TAFwtK9.SLX-19690.NEBNext09.HCT7YDRXY.s_1.txt-
# TAFwtK9.SLX-19690.NEBNext09.HCT7YDRXY.s_1.txt-Total read pairs processed:         33,411,826
# TAFwtK9.SLX-19690.NEBNext09.HCT7YDRXY.s_1.txt-  Read 1 with adapter:                 923,843 (2.8%)
# TAFwtK9.SLX-19690.NEBNext09.HCT7YDRXY.s_1.txt-  Read 2 with adapter:                 920,121 (2.8%)
# TAFwtK9.SLX-19690.NEBNext09.HCT7YDRXY.s_1.txt-Pairs that were too short:              15,406 (0.0%)
# TAFwtK9.SLX-19690.NEBNext09.HCT7YDRXY.s_1.txt-Pairs written (passing filters):    33,396,420 (100.0%)
# TAFwtK9.SLX-19690.NEBNext09.HCT7YDRXY.s_1.txt-
# TAFwtK9.SLX-19690.NEBNext09.HCT7YDRXY.s_1.txt-Total basepairs processed: 3,341,182,600 bp
# TAFwtK9.SLX-19690.NEBNext09.HCT7YDRXY.s_1.txt-  Read 1: 1,670,591,300 bp
# TAFwtK9.SLX-19690.NEBNext09.HCT7YDRXY.s_1.txt-  Read 2: 1,670,591,300 bp
# TAFwtK9.SLX-19690.NEBNext09.HCT7YDRXY.s_1.txt-Quality-trimmed:               3,040,546 bp (0.1%)
# TAFwtK9.SLX-19690.NEBNext09.HCT7YDRXY.s_1.txt-  Read 1:       901,448 bp
# TAFwtK9.SLX-19690.NEBNext09.HCT7YDRXY.s_1.txt-  Read 2:     2,139,098 bp
# TAFwtK9.SLX-19690.NEBNext09.HCT7YDRXY.s_1.txt-Total written (filtered):  3,331,130,891 bp (99.7%)
# TAFwtK9.SLX-19690.NEBNext09.HCT7YDRXY.s_1.txt-  Read 1: 1,665,901,000 bp
# TAFwtK9.SLX-19690.NEBNext09.HCT7YDRXY.s_1.txt-  Read 2: 1,665,229,891 bp
```



### alignment hisat2 genome

Experiment 1:

TAFwtK9         
TAFkoK9
TAF5K9
TAF15K9
TAFwtIN
TAFkoIN
TAF5IN
TAF15IN

Mapping to the genome


#### align, sort, markdup, index and flagstat

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/chipseq2/fastq_trimmed

mkdir ../hisat2

idx=/projects/qbio/bifo/users/sergio/reference/gencode/release35_GRCh38.p13/fasta/GRCh38.p13.genome

for fq1 in TAF*.r_1.fq.gz
do
  fq2=${fq1/.r_1./.r_2.}
  bname=${fq1%.HCT7YDRXY.s_1.r_1.fq.gz}
  #echo $fq1, $fq2, $bname
  sbatch -J $bname -o ../hisat2/$bname.log --mem 64G -t 1-0 --wrap "hisat2 -p 20 -x $idx -1 $fq1 -2 $fq2 | \
  samtools view -@ 20 -bS - | \
  samtools sort -@ 20 -T /projects/qbio/bifo/users/sergio/tmp/$bname -o ../hisat2/$bname.tmp.bam - && \
  sambamba markdup -t 20 ../hisat2/$bname.tmp.bam ../hisat2/$bname.bam 2> ../hisat2/$bname.markdup.txt && \
  samtools flagstat ../hisat2/$bname.bam > ../hisat2/$bname.flagstat.txt"
done

cd ../hisat2

tail *.log

grep "overall alignment rate" *.log
# TAF15IN.SLX-19690.NEBNext21.log:95.60% overall alignment rate
# TAF15K9.SLX-19690.NEBNext12.log:94.19% overall alignment rate
# TAF5IN.SLX-19690.NEBNext20.log:95.58% overall alignment rate
# TAF5K9.SLX-19690.NEBNext11.log:93.97% overall alignment rate
# TAFkoIN.SLX-19690.NEBNext19.log:95.55% overall alignment rate
# TAFkoK9.SLX-19690.NEBNext10.log:94.18% overall alignment rate
# TAFwtIN.SLX-19690.NEBNext15.log:95.42% overall alignment rate
# TAFwtK9.SLX-19690.NEBNext09.log:94.13% overall alignment rate

grep -E "total|duplicates|mapped \(" *.flagstat.txt
# TAF15IN.SLX-19690.NEBNext21.flagstat.txt:65322806 + 0 in total (QC-passed reads + QC-failed reads)
# TAF15IN.SLX-19690.NEBNext21.flagstat.txt:6434305 + 0 duplicates
# TAF15IN.SLX-19690.NEBNext21.flagstat.txt:62787651 + 0 mapped (96.12% : N/A)
# TAF15K9.SLX-19690.NEBNext12.flagstat.txt:81000059 + 0 in total (QC-passed reads + QC-failed reads)
# TAF15K9.SLX-19690.NEBNext12.flagstat.txt:8933627 + 0 duplicates
# TAF15K9.SLX-19690.NEBNext12.flagstat.txt:76908437 + 0 mapped (94.95% : N/A)
# TAF5IN.SLX-19690.NEBNext20.flagstat.txt:88964210 + 0 in total (QC-passed reads + QC-failed reads)
# TAF5IN.SLX-19690.NEBNext20.flagstat.txt:11178946 + 0 duplicates
# TAF5IN.SLX-19690.NEBNext20.flagstat.txt:85504220 + 0 mapped (96.11% : N/A)
# TAF5K9.SLX-19690.NEBNext11.flagstat.txt:93931950 + 0 in total (QC-passed reads + QC-failed reads)
# TAF5K9.SLX-19690.NEBNext11.flagstat.txt:10165168 + 0 duplicates
# TAF5K9.SLX-19690.NEBNext11.flagstat.txt:89033734 + 0 mapped (94.79% : N/A)
# TAFkoIN.SLX-19690.NEBNext19.flagstat.txt:64002291 + 0 in total (QC-passed reads + QC-failed reads)
# TAFkoIN.SLX-19690.NEBNext19.flagstat.txt:6336338 + 0 duplicates
# TAFkoIN.SLX-19690.NEBNext19.flagstat.txt:61492032 + 0 mapped (96.08% : N/A)
# TAFkoK9.SLX-19690.NEBNext10.flagstat.txt:93932130 + 0 in total (QC-passed reads + QC-failed reads)
# TAFkoK9.SLX-19690.NEBNext10.flagstat.txt:11458842 + 0 duplicates
# TAFkoK9.SLX-19690.NEBNext10.flagstat.txt:89193250 + 0 mapped (94.95% : N/A)
# TAFwtIN.SLX-19690.NEBNext15.flagstat.txt:80797468 + 0 in total (QC-passed reads + QC-failed reads)
# TAFwtIN.SLX-19690.NEBNext15.flagstat.txt:8709598 + 0 duplicates
# TAFwtIN.SLX-19690.NEBNext15.flagstat.txt:77535259 + 0 mapped (95.96% : N/A)
# TAFwtK9.SLX-19690.NEBNext09.flagstat.txt:77060148 + 0 in total (QC-passed reads + QC-failed reads)
# TAFwtK9.SLX-19690.NEBNext09.flagstat.txt:7772539 + 0 duplicates
# TAFwtK9.SLX-19690.NEBNext09.flagstat.txt:73138158 + 0 mapped (94.91% : N/A)

tail -n +1 *.flagstat.txt
```


#### bigwig

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/chipseq2/hisat2

for bam in `ls *.bam | grep -v "tmp"`
do
  bname=${bam%.bam}
  sbatch -J $bname -o $bname.bigwig.log --mem 32G -t 1-0 --wrap "bamCoverage -b $bam -o ${bname}.bw -of bigwig --binSize 1 --normalizeUsing CPM"
done

tail *.bigwig.log
```


#### bigwig - options 1

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/chipseq2/hisat2

for bam in `ls *.bam | grep -v "tmp"`
do
  bname=${bam%.bam}
  sbatch -J $bname -o $bname.bigwig.options1.log --mem 32G -t 1-0 --wrap "bamCoverage -b $bam -o ${bname}.options1.bw -of bigwig --binSize 5 --normalizeUsing CPM --centerReads"
done

tail *.bigwig.options1.log
```


#### bigwig - options 2

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/chipseq2/hisat2

for bam in `ls *.bam | grep -v "tmp"`
do
  bname=${bam%.bam}
  sbatch -J $bname -o $bname.bigwig.options2.log --mem 32G -t 1-0 --wrap "bamCoverage -b $bam -o ${bname}.options2.bw -of bigwig --binSize 5 --normalizeUsing CPM --centerReads --skipNonCoveredRegions"
done

tail *.bigwig.options2.log
```



### alignment hisat2 genome and fragment

Experiment 2:

L1wtK9
L1koK9
L1wtsffvK9
L1kosffvK9
L1wtIN         
L1koIN
L1wtsffvIN        
L1kosffvIN

Mapping to the genome + reporter construct – but only P2A-iRFP fragment of the reporter is unique


#### prepare index

Adding sequence of the unique fragment (P2A-iRFP) to the reference genome:

```bash
cd /projects/qbio/bifo/users/sergio/reference/gencode/release35_GRCh38.p13/fasta
cat GRCh38.p13.genome.fa p2airfp.fa > GRCh38.p13.genome.p2airfp.fa
sbatch -J hisat2-build -o hisat2-build.log --mem 8G -t 1-0 --wrap "hisat2-build GRCh38.p13.genome.p2airfp.fa GRCh38.p13.genome.p2airfp"
rm hisat2-build.log
```


#### align, sort, markdup, index and flagstat

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/chipseq2/fastq_trimmed

mkdir ../hisat2_fragment

idx=/projects/qbio/bifo/users/sergio/reference/gencode/release35_GRCh38.p13/fasta/GRCh38.p13.genome.p2airfp

for fq1 in L1*.r_1.fq.gz
do
  fq2=${fq1/.r_1./.r_2.}
  bname=${fq1%.HCT7YDRXY.s_1.r_1.fq.gz}
  #echo $fq1, $fq2, $bname
  sbatch -J $bname -o ../hisat2_fragment/$bname.log --mem 64G -t 1-0 --wrap "hisat2 -p 20 -x $idx -1 $fq1 -2 $fq2 | \
  samtools view -@ 20 -bS - | \
  samtools sort -@ 20 -T /projects/qbio/bifo/users/sergio/tmp/$bname -o ../hisat2_fragment/$bname.tmp.bam - && \
  sambamba markdup -t 20 ../hisat2_fragment/$bname.tmp.bam ../hisat2_fragment/$bname.bam 2> ../hisat2_fragment/$bname.markdup.txt && \
  samtools flagstat ../hisat2_fragment/$bname.bam > ../hisat2_fragment/$bname.flagstat.txt"
done

cd ../hisat2_fragment

tail *.log

grep "overall alignment rate" *.log
# L1koIN.SLX-19690.NEBNext37.log:95.96% overall alignment rate
# L1koK9.SLX-19690.NEBNext32.log:94.77% overall alignment rate
# L1kosffvIN.SLX-19690.NEBNext39.log:95.80% overall alignment rate
# L1kosffvK9.SLX-19690.NEBNext34.log:94.85% overall alignment rate
# L1wtIN.SLX-19690.NEBNext36.log:95.80% overall alignment rate
# L1wtK9.SLX-19690.NEBNext31.log:94.76% overall alignment rate
# L1wtsffvIN.SLX-19690.NEBNext38.log:95.84% overall alignment rate
# L1wtsffvK9.SLX-19690.NEBNext33.log:94.87% overall alignment rate

grep -E "total|duplicates|mapped \(" *.flagstat.txt
# L1koIN.SLX-19690.NEBNext37.flagstat.txt:81422461 + 0 in total (QC-passed reads + QC-failed reads)
# L1koIN.SLX-19690.NEBNext37.flagstat.txt:10032422 + 0 duplicates
# L1koIN.SLX-19690.NEBNext37.flagstat.txt:78508497 + 0 mapped (96.42% : N/A)
# L1koK9.SLX-19690.NEBNext32.flagstat.txt:76367967 + 0 in total (QC-passed reads + QC-failed reads)
# L1koK9.SLX-19690.NEBNext32.flagstat.txt:8108475 + 0 duplicates
# L1koK9.SLX-19690.NEBNext32.flagstat.txt:72883049 + 0 mapped (95.44% : N/A)
# L1kosffvIN.SLX-19690.NEBNext39.flagstat.txt:70970983 + 0 in total (QC-passed reads + QC-failed reads)
# L1kosffvIN.SLX-19690.NEBNext39.flagstat.txt:7332640 + 0 duplicates
# L1kosffvIN.SLX-19690.NEBNext39.flagstat.txt:68335984 + 0 mapped (96.29% : N/A)
# L1kosffvK9.SLX-19690.NEBNext34.flagstat.txt:59401517 + 0 in total (QC-passed reads + QC-failed reads)
# L1kosffvK9.SLX-19690.NEBNext34.flagstat.txt:5713807 + 0 duplicates
# L1kosffvK9.SLX-19690.NEBNext34.flagstat.txt:56735709 + 0 mapped (95.51% : N/A)
# L1wtIN.SLX-19690.NEBNext36.flagstat.txt:71274628 + 0 in total (QC-passed reads + QC-failed reads)
# L1wtIN.SLX-19690.NEBNext36.flagstat.txt:7203369 + 0 duplicates
# L1wtIN.SLX-19690.NEBNext36.flagstat.txt:68630901 + 0 mapped (96.29% : N/A)
# L1wtK9.SLX-19690.NEBNext31.flagstat.txt:88257091 + 0 in total (QC-passed reads + QC-failed reads)
# L1wtK9.SLX-19690.NEBNext31.flagstat.txt:10590150 + 0 duplicates
# L1wtK9.SLX-19690.NEBNext31.flagstat.txt:84231464 + 0 mapped (95.44% : N/A)
# L1wtsffvIN.SLX-19690.NEBNext38.flagstat.txt:72619077 + 0 in total (QC-passed reads + QC-failed reads)
# L1wtsffvIN.SLX-19690.NEBNext38.flagstat.txt:7614464 + 0 duplicates
# L1wtsffvIN.SLX-19690.NEBNext38.flagstat.txt:69950708 + 0 mapped (96.33% : N/A)
# L1wtsffvK9.SLX-19690.NEBNext33.flagstat.txt:78025021 + 0 in total (QC-passed reads + QC-failed reads)
# L1wtsffvK9.SLX-19690.NEBNext33.flagstat.txt:7342689 + 0 duplicates
# L1wtsffvK9.SLX-19690.NEBNext33.flagstat.txt:74541045 + 0 mapped (95.53% : N/A)
```


#### bigwig

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/chipseq2/hisat2_fragment

for bam in `ls *.bam | grep -v "tmp"`
do
  bname=${bam%.bam}
  sbatch -J $bname -o $bname.bigwig.log --mem 32G -t 1-0 --wrap "bamCoverage -b $bam -o ${bname}.bw -of bigwig --binSize 1 --normalizeUsing CPM"
done

tail *.bigwig.log
```


#### bigwig - options 1

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/chipseq2/hisat2_fragment

for bam in `ls *.bam | grep -v "tmp"`
do
  bname=${bam%.bam}
  sbatch -J $bname -o $bname.bigwig.options1.log --mem 32G -t 1-0 --wrap "bamCoverage -b $bam -o ${bname}.options1.bw -of bigwig --binSize 5 --normalizeUsing CPM --centerReads"
done

tail *.bigwig.options1.log
```


#### bigwig - options 2

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/chipseq2/hisat2_fragment

for bam in `ls *.bam | grep -v "tmp"`
do
  bname=${bam%.bam}
  sbatch -J $bname -o $bname.bigwig.options2.log --mem 32G -t 1-0 --wrap "bamCoverage -b $bam -o ${bname}.options2.bw -of bigwig --binSize 5 --normalizeUsing CPM --centerReads --skipNonCoveredRegions"
done

tail *.bigwig.options2.log
```

