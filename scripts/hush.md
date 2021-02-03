
- Author: Sergio Martínez Cuesta
- Date: December 2020
- Code version: v1
- Description: Developed in collaboration with Marta Seczynska
- Input data available in [GSE95374](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE95374)



## Pseudogenes and intronless

### Pseudogenes

Parsing encode gtf and extract gene names, gene ids and gene types (pseudogenes ...)

```python
#cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/annotation
#python

ifile = open("/projects/qbio/bifo/users/sergio/reference/gencode/release35_GRCh38.p13/gtf/gencode.v35.annotation.gtf")
ilines = ifile.readlines()
ifile.close()

genes = []

for l in ilines:
  if not l.startswith("##"):
    fields = l.split("\t")
    if fields[2] == "gene":
      chr = fields[0]
      start = fields[3]
      end = fields[4]
      strand = fields[6]
      annotations = fields[8].split("; ")[:-1]
      for a in annotations:
        if a.startswith("gene_id"):
          gene_id=a.split('"')[1]
        if a.startswith("gene_type"):
          gene_type=a.split('"')[1]
        if a.startswith("gene_name"):
          gene_name=a.split('"')[1]
      genes.append((chr, start, end, gene_name, gene_id, strand, gene_type))


ofile = open("gencode.v35.annotation.gene_type.bed", "w")
ofile.write("\n".join(["\t".join(g) for g in genes]) + "\n")
ofile.close()
```



### Intronless

As requested in [biostars](https://www.biostars.org/p/472156/)

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/annotation

awk '{ if ($3=="exon") print $1, $4, $5, $10 }' /projects/qbio/bifo/users/sergio/reference/gencode/release35_GRCh38.p13/gtf/gencode.v35.annotation.gtf | \
sort -u | \
cut -d'"' -f2 | \
sort | uniq -c | \
perl -lane '$F[0] == 1 and print $F[1]' > gencode.v35.annotation.intronless.txt

wc -l gencode.v35.annotation.intronless.txt # 22499
```



### Merge

```python
#cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/annotation
#python

ifile = open("gencode.v35.annotation.gene_type.bed")
ilines = ifile.readlines()
ifile.close()

ifile2 = open("gencode.v35.annotation.intronless.txt")
ilines2 = [l.replace("\n", "") for l in ifile2.readlines()]
ifile2.close()

genes = []

for l in ilines:
  fields=l.split()
  gene_id=fields[4]
  if gene_id in ilines2:
    intronless = "y"
  else:
    intronless = "n"
  fields.append(intronless)
  genes.append(fields)


ofile = open("gencode.v35.annotation.gene_type.intronless.bed", "w")
ofile.write("\n".join(["\t".join(g) for g in genes]) + "\n")
ofile.close()
```

Check gene types and intronless:

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/annotation

wc -l gencode.v35.annotation.gene_type.intronless.bed # 60656

head gencode.v35.annotation.gene_type.intronless.bed | column -t
# chr1  11869  14409   DDX11L1      ENSG00000223972.5  +  transcribed_unprocessed_pseudogene  n
# chr1  14404  29570   WASH7P       ENSG00000227232.5  -  unprocessed_pseudogene              n
# chr1  17369  17436   MIR6859-1    ENSG00000278267.1  -  miRNA                               y
# chr1  29554  31109   MIR1302-2HG  ENSG00000243485.5  +  lncRNA                              n
# chr1  30366  30503   MIR1302-2    ENSG00000284332.1  +  miRNA                               y
# chr1  34554  36081   FAM138A      ENSG00000237613.2  -  lncRNA                              n
# chr1  52473  53312   OR4G4P       ENSG00000268020.3  +  unprocessed_pseudogene              y
# chr1  57598  64116   OR4G11P      ENSG00000240361.2  +  transcribed_unprocessed_pseudogene  n
# chr1  65419  71585   OR4F5        ENSG00000186092.6  +  protein_coding                      n
# chr1  89295  133723  AL627309.1   ENSG00000238009.6  -  lncRNA                              n

cut -f7 gencode.v35.annotation.gene_type.intronless.bed | sort | uniq -c | sort -k1,1nr
  # 19954 protein_coding
  # 16899 lncRNA
  # 10169 processed_pseudogene
  #  2615 unprocessed_pseudogene
  #  2212 misc_RNA
  #  1901 snRNA
  #  1881 miRNA
  #  1058 TEC
  #   943 snoRNA
  #   941 transcribed_unprocessed_pseudogene
  #   500 transcribed_processed_pseudogene
  #   497 rRNA_pseudogene
  #   188 IG_V_pseudogene
  #   144 IG_V_gene
  #   138 transcribed_unitary_pseudogene
  #   106 TR_V_gene
  #    97 unitary_pseudogene
  #    79 TR_J_gene
  #    49 polymorphic_pseudogene
  #    49 scaRNA
  #    47 rRNA
  #    37 IG_D_gene
  #    33 TR_V_pseudogene
  #    22 Mt_tRNA
  #    18 IG_J_gene
  #    18 pseudogene
  #    14 IG_C_gene
  #     9 IG_C_pseudogene
  #     8 ribozyme
  #     6 TR_C_gene
  #     5 sRNA
  #     4 TR_D_gene
  #     4 TR_J_pseudogene
  #     3 IG_J_pseudogene
  #     2 Mt_rRNA
  #     2 translated_processed_pseudogene
  #     1 IG_pseudogene
  #     1 scRNA
  #     1 translated_unprocessed_pseudogene
  #     1 vault_RNA

cut -f8 gencode.v35.annotation.gene_type.intronless.bed | sort | uniq -c
  # 38157 n
  # 22499 y
```





## RNA-seq

K562 datasets:

GSE | GSM name | SRX | SRR
----|----------|-----|-----
GSE95374 | GSM2509511	RNA-seq_WT_rep1 | SRX2589983 | SRR5287124
GSE95374 | GSM2509512	RNA-seq_WT_rep2 | SRX2589984 | SRR5287125
GSE95374 | GSM2509513	RNA-seq_MORC2-KO_rep1 | SRX2589985 | SRR5287126
GSE95374 | GSM2509514	RNA-seq_MORC2-KO_rep2 | SRX2589986 | SRR5287127
GSE95374 | GSM2509515	RNA-seq_MPP8-KO_rep1 | SRX2589987 | SRR5287128
GSE95374 | GSM2509516	RNA-seq_MPP8-KO_rep2 | SRX2589988 | SRR5287129
GSE95374 | GSM2509517	RNA-seq_TASOR-KO_rep1 | SRX2589989 | SRR5287130
GSE95374 | GSM2509518	RNA-seq_TASOR-KO_rep2 | SRX2589990 | SRR5287131



### Download fastq files

```bash
cd /projects/qbio/bifo/users/sergio/repository

mkdir -p 20201122_hush/rnaseq/fastq && cd 20201122_hush/rnaseq/fastq

for run_id in SRR5287124 SRR5287125 SRR5287126 SRR5287127 SRR5287128 SRR5287129 SRR5287130 SRR5287131
do
  sbatch -J $run_id -o $run_id.log --mem 16G --wrap "fastq-dump --outdir . --gzip --split-files -F $run_id"
done

tail *.log
```



### Rename fastq files

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/rnaseq/fastq

# GSE95374 | GSM2509511	RNA-seq_WT_rep1 | SRX2589983 | SRR5287124
for filename in SRR5287124_{1,2}.fastq.gz; do mv "$filename" "WT_rep1_$filename"; done;
# GSE95374 | GSM2509512	RNA-seq_WT_rep2 | SRX2589984 | SRR5287125
for filename in SRR5287125_{1,2}.fastq.gz; do mv "$filename" "WT_rep2_$filename"; done;
# GSE95374 | GSM2509513	RNA-seq_MORC2-KO_rep1 | SRX2589985 | SRR5287126
for filename in SRR5287126_{1,2}.fastq.gz; do mv "$filename" "MORC2-KO_rep1_$filename"; done;
# GSE95374 | GSM2509514	RNA-seq_MORC2-KO_rep2 | SRX2589986 | SRR5287127
for filename in SRR5287127_{1,2}.fastq.gz; do mv "$filename" "MORC2-KO_rep2_$filename"; done;
# GSE95374 | GSM2509515	RNA-seq_MPP8-KO_rep1 | SRX2589987 | SRR5287128
for filename in SRR5287128_{1,2}.fastq.gz; do mv "$filename" "MPP8-KO_rep1_$filename"; done;
# GSE95374 | GSM2509516	RNA-seq_MPP8-KO_rep2 | SRX2589988 | SRR5287129
for filename in SRR5287129_{1,2}.fastq.gz; do mv "$filename" "MPP8-KO_rep2_$filename"; done;
# GSE95374 | GSM2509517	RNA-seq_TASOR-KO_rep1 | SRX2589989 | SRR5287130
for filename in SRR5287130_{1,2}.fastq.gz; do mv "$filename" "TASOR-KO_rep1_$filename"; done;
# GSE95374 | GSM2509518	RNA-seq_TASOR-KO_rep2 | SRX2589990 | SRR5287131
for filename in SRR5287131_{1,2}.fastq.gz; do mv "$filename" "TASOR-KO_rep2_$filename"; done;
```



### Quality check

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/rnaseq/fastq

mkdir ../fastqc

for fq in *.fastq.gz
do
  bname=${fq%.fastq.gz}
  sbatch -J $bname -o ../fastqc/$bname.log --mem 4G --wrap "fastqc --noextract --nogroup -q -o ../fastqc $fq"
done
```

Look at quality via samba connection. Remove `fastqc` folder:

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/rnaseq
rm -rf fastqc
```



### Trim illumina adapters and quality trimming

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/rnaseq/fastq

mkdir ../fastq_trimmed

for fq1 in *_1.fastq.gz
do
  fq2=${fq1/_1./_2.}
  bname=${fq1%_1.fastq.gz}
  #echo $fq1, $fq2, $bname
  sbatch -J $bname -o ../fastq_trimmed/$bname.log --mem 4G -t 1-0 --wrap "cutadapt -a AGATCGGAAGAGC -A AGATCGGAAGAGC -m 15 -q 20 -o ../fastq_trimmed/$fq1 -p ../fastq_trimmed/$fq2 $fq1 $fq2 > ../fastq_trimmed/$bname.txt"
done

cd ../fastq_trimmed

tail *.log

grep "Read 1 with adapter" *.txt
# MORC2-KO_rep1_SRR5287126.txt:  Read 1 with adapter:               6,450,834 (11.5%)
# MORC2-KO_rep2_SRR5287127.txt:  Read 1 with adapter:               1,954,529 (3.6%)
# MPP8-KO_rep1_SRR5287128.txt:  Read 1 with adapter:               1,557,796 (3.0%)
# MPP8-KO_rep2_SRR5287129.txt:  Read 1 with adapter:               1,652,623 (3.7%)
# TASOR-KO_rep1_SRR5287130.txt:  Read 1 with adapter:               3,046,624 (6.7%)
# TASOR-KO_rep2_SRR5287131.txt:  Read 1 with adapter:               1,442,540 (2.9%)
# WT_rep1_SRR5287124.txt:  Read 1 with adapter:               5,191,148 (9.8%)
# WT_rep2_SRR5287125.txt:  Read 1 with adapter:               4,993,244 (10.7%)

grep "Read 2 with adapter" *.txt
# MORC2-KO_rep1_SRR5287126.txt:  Read 2 with adapter:               6,417,371 (11.4%)
# MORC2-KO_rep2_SRR5287127.txt:  Read 2 with adapter:               1,907,367 (3.5%)
# MPP8-KO_rep1_SRR5287128.txt:  Read 2 with adapter:               1,532,002 (3.0%)
# MPP8-KO_rep2_SRR5287129.txt:  Read 2 with adapter:               1,626,117 (3.6%)
# TASOR-KO_rep1_SRR5287130.txt:  Read 2 with adapter:               3,014,115 (6.6%)
# TASOR-KO_rep2_SRR5287131.txt:  Read 2 with adapter:               1,408,176 (2.8%)
# WT_rep1_SRR5287124.txt:  Read 2 with adapter:               5,165,739 (9.8%)
# WT_rep2_SRR5287125.txt:  Read 2 with adapter:               4,937,481 (10.5%)

grep "Pairs written (passing filters):" *.txt
# MORC2-KO_rep1_SRR5287126.txt:Pairs written (passing filters):    55,991,285 (99.9%)
# MORC2-KO_rep2_SRR5287127.txt:Pairs written (passing filters):    54,129,935 (99.9%)
# MPP8-KO_rep1_SRR5287128.txt:Pairs written (passing filters):    51,583,419 (99.9%)
# MPP8-KO_rep2_SRR5287129.txt:Pairs written (passing filters):    44,603,458 (99.9%)
# TASOR-KO_rep1_SRR5287130.txt:Pairs written (passing filters):    45,417,761 (99.9%)
# TASOR-KO_rep2_SRR5287131.txt:Pairs written (passing filters):    49,681,079 (99.9%)
# WT_rep1_SRR5287124.txt:Pairs written (passing filters):    52,889,798 (99.9%)
# WT_rep2_SRR5287125.txt:Pairs written (passing filters):    46,752,959 (99.9%)
```



### Alignment

#### Prepare reference

```bash
cd /projects/qbio/bifo/users/sergio/reference/gencode/release35_GRCh38.p13

mkdir rsem_bowtie2

rsem-prepare-reference \
--gtf /projects/qbio/bifo/users/sergio/reference/gencode/release35_GRCh38.p13/gtf/gencode.v35.annotation.gtf \
--bowtie2 \
/projects/qbio/bifo/users/sergio/reference/gencode/release35_GRCh38.p13/fasta/GRCh38.p13.genome.fa \
/projects/qbio/bifo/users/sergio/reference/gencode/release35_GRCh38.p13/rsem_bowtie2/GRCh38.p13.genome
```


#### Align

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/rnaseq/fastq_trimmed

mkdir ../rsem

bwtidx=/projects/qbio/bifo/users/sergio/reference/gencode/release35_GRCh38.p13/rsem_bowtie2/GRCh38.p13.genome

for fq1 in *_1.fastq.gz
do
  fq2=${fq1/_1./_2.}
  bname=${fq1%_1.fastq.gz}
  #echo $fq1, $fq2, $bname
  sbatch -J $bname -o ../rsem/$bname.log --mem 32G -t 3-0 --wrap "rsem-calculate-expression \
  --paired-end \
  -p 16 \
  --bowtie2 \
  --append-names \
  --output-genome-bam \
  --sort-bam-by-coordinate \
  $fq1 \
  $fq2 \
  $bwtidx \
  ../rsem/$bname"
done

cd ../rsem

tail *.log

grep "overall alignment rate" *.log
# MORC2-KO_rep1_SRR5287126.log:44.62% overall alignment rate
# MORC2-KO_rep2_SRR5287127.log:40.10% overall alignment rate
# MPP8-KO_rep1_SRR5287128.log:43.14% overall alignment rate
# MPP8-KO_rep2_SRR5287129.log:45.78% overall alignment rate
# TASOR-KO_rep1_SRR5287130.log:47.10% overall alignment rate
# TASOR-KO_rep2_SRR5287131.log:47.48% overall alignment rate
# WT_rep1_SRR5287124.log:42.37% overall alignment rate
# WT_rep2_SRR5287125.log:39.16% overall alignment rate

wc -l *.genes.results
   # 60657 MORC2-KO_rep1_SRR5287126.genes.results
   # 60657 MORC2-KO_rep2_SRR5287127.genes.results
   # 60657 MPP8-KO_rep1_SRR5287128.genes.results
   # 60657 MPP8-KO_rep2_SRR5287129.genes.results
   # 60657 TASOR-KO_rep1_SRR5287130.genes.results
   # 60657 TASOR-KO_rep2_SRR5287131.genes.results
   # 60657 WT_rep1_SRR5287124.genes.results
   # 60657 WT_rep2_SRR5287125.genes.results
```





## ChIP-seq

K562 datasets:

GSE | GSM name | SRR
----|----------|-----
GSE95374 | GSM2509455	ChIP:MORC2_Cell:WT_rep1 | SRR5287068
GSE95374 | GSM2509456	ChIP:MORC2_Cell:WT_rep2 | SRR5287069
GSE95374 | GSM2509457	ChIP:MORC2_Cell:MORC2-KO_rep1 | SRR5287070
GSE95374 | GSM2509458	ChIP:MORC2_Cell:MORC2-KO_rep2 | SRR5287071
GSE95374 | GSM2509459	ChIP:MORC2_Cell:MPP8-KO_rep1 | SRR5287072
GSE95374 | GSM2509460	ChIP:MORC2_Cell:MPP8-KO_rep2 | SRR5287073
GSE95374 | GSM2509461	ChIP:MORC2_Cell:TASOR-KO_rep1 | SRR5287074
GSE95374 | GSM2509462	ChIP:MORC2_Cell:TASOR-KO_rep2 | SRR5287075
GSE95374 | GSM2509463	ChIP:MPP8_Cell:WT_rep1 | SRR5287076
GSE95374 | GSM2509464	ChIP:MPP8_Cell:WT_rep2 | SRR5287077
GSE95374 | GSM2509465	ChIP:MPP8_Cell:MORC2-KO_rep1 | SRR5287078
GSE95374 | GSM2509466	ChIP:MPP8_Cell:MORC2-KO_rep2 | SRR5287079
GSE95374 | GSM2509467	ChIP:MPP8_Cell:MPP8-KO_rep1 | SRR5287080
GSE95374 | GSM2509468	ChIP:MPP8_Cell:MPP8-KO_rep2 | SRR5287081
GSE95374 | GSM2509469	ChIP:MPP8_Cell:TASOR-KO_rep1 | SRR5287082
GSE95374 | GSM2509470	ChIP:MPP8_Cell:TASOR-KO_rep2 | SRR5287083
GSE95374 | GSM2509471	ChIP:TASOR_Cell:WT_rep1 | SRR5287084
GSE95374 | GSM2509472	ChIP:TASOR_Cell:WT_rep2 | SRR5287085
GSE95374 | GSM2509473	ChIP:TASOR_Cell:MORC2-KO_rep1 | SRR5287086
GSE95374 | GSM2509474	ChIP:TASOR_Cell:MORC2-KO_rep2 | SRR5287087
GSE95374 | GSM2509475	ChIP:TASOR_Cell:MPP8-KO_rep1 | SRR5287088
GSE95374 | GSM2509476	ChIP:TASOR_Cell:MPP8-KO_rep2 | SRR5287089
GSE95374 | GSM2509477	ChIP:TASOR_Cell:TASOR-KO_rep1 | SRR5287090
GSE95374 | GSM2509478	ChIP:TASOR_Cell:TASOR-KO_rep2 | SRR5287091
GSE95374 | GSM2509479	ChIP:Input(MORC2, MPP8, TASOR)_Cell:WT_rep1 | SRR5287092
GSE95374 | GSM2509480	ChIP:Input(MORC2, MPP8, TASOR)_Cell:WT_rep2 | SRR5287093
GSE95374 | GSM2509481	ChIP:Input(MORC2, MPP8, TASOR)_Cell:MORC2-KO_rep1 | SRR5287094
GSE95374 | GSM2509482	ChIP:Input(MORC2, MPP8, TASOR)_Cell:MORC2-KO_rep2 | SRR5287095
GSE95374 | GSM2509483	ChIP:Input(MORC2, MPP8, TASOR)_Cell:MPP8-KO_rep1 | SRR5287096
GSE95374 | GSM2509484	ChIP:Input(MORC2, MPP8, TASOR)_Cell:MPP8-KO_rep2 | SRR5287097
GSE95374 | GSM2509485	ChIP:Input(MORC2, MPP8, TASOR)_Cell:TASOR-KO_rep1 | SRR5287098
GSE95374 | GSM2509486	ChIP:Input(MORC2, MPP8, TASOR)_Cell:TASOR-KO_rep2 | SRR5287099
GSE95374 | GSM2509487	ChIP:H3K9me3_Cell:WT_rep1 | SRR5287100
GSE95374 | GSM2509488	ChIP:H3K9me3_Cell:WT_rep2 | SRR5287101
GSE95374 | GSM2509489	ChIP:H3K9me3_Cell:MORC2-KO_rep1 | SRR5287102
GSE95374 | GSM2509490	ChIP:H3K9me3_Cell:MORC2-KO_rep2 | SRR5287103
GSE95374 | GSM2509491	ChIP:H3K9me3_Cell:TASOR-KO_rep1 | SRR5287104
GSE95374 | GSM2509492	ChIP:H3K9me3_Cell:TASOR-KO_rep2 | SRR5287105
GSE95374 | GSM2509493	ChIP:Input(H3K9me3)_Cell:WT_rep1 | SRR5287106
GSE95374 | GSM2509494	ChIP:Input(H3K9me3)_Cell:WT_rep2 | SRR5287107
GSE95374 | GSM2509495	ChIP:Input(H3K9me3)_Cell:MORC2-KO_rep1 | SRR5287108
GSE95374 | GSM2509496	ChIP:Input(H3K9me3)_Cell:MORC2-KO_rep2 | SRR5287109
GSE95374 | GSM2509497	ChIP:Input(H3K9me3)_Cell:TASOR-KO_rep1 | SRR5287110
GSE95374 | GSM2509498	ChIP:Input(H3K9me3)_Cell:TASOR-KO_rep2 | SRR5287111
GSE95374 | GSM2509503	ChIP:H3K9me3_Cell:WT_rep3 | SRR5287116
GSE95374 | GSM2509504	ChIP:H3K9me3_Cell:WT_rep4 | SRR5287117
GSE95374 | GSM2509505	ChIP:H3K9me3_Cell:MPP8-KO_rep1 | SRR5287118
GSE95374 | GSM2509506	ChIP:H3K9me3_Cell:MPP8-KO_rep2 | SRR5287119
GSE95374 | GSM2509507	ChIP:Input(H3K9me2or3)_Cell:WT_rep1 | SRR5287120
GSE95374 | GSM2509508	ChIP:Input(H3K9me2or3)_Cell:WT_rep2 | SRR5287121
GSE95374 | GSM2509509	ChIP:Input(H3K9me2or3)_Cell:MPP8-KO_rep1 | SRR5287122
GSE95374 | GSM2509510	ChIP:Input(H3K9me2or3)_Cell:MPP8-KO_rep2 | SRR5287123



### Download fastq files

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush

mkdir -p chipseq/fastq && cd chipseq/fastq

for run_id in SRR52870{68..99} SRR52871{00..11} SRR52871{16..23}
do
  sbatch -J $run_id -o $run_id.log --mem 32G -t 3-0 --wrap "fastq-dump --outdir . --gzip --split-files -F $run_id"
done

for run_id in SRR5287093 SRR5287098 SRR5287099
do
  sbatch -J $run_id -o $run_id.log --mem 32G -t 3-0 --wrap "fastq-dump --outdir . --gzip --split-files -F $run_id"
done

tail *.log
```



### Rename fastq files

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/chipseq/fastq

declare -A pairs=(\
[SRR5287068]=ChIP_MORC2_WT_rep1 \
[SRR5287069]=ChIP_MORC2_WT_rep2 \
[SRR5287070]=ChIP_MORC2_MORC2-KO_rep1 \
[SRR5287071]=ChIP_MORC2_MORC2-KO_rep2 \
[SRR5287072]=ChIP_MORC2_MPP8-KO_rep1 \
[SRR5287073]=ChIP_MORC2_MPP8-KO_rep2 \
[SRR5287074]=ChIP_MORC2_TASOR-KO_rep1 \
[SRR5287075]=ChIP_MORC2_TASOR-KO_rep2 \
[SRR5287076]=ChIP_MPP8_WT_rep1 \
[SRR5287077]=ChIP_MPP8_WT_rep2 \
[SRR5287078]=ChIP_MPP8_MORC2-KO_rep1 \
[SRR5287079]=ChIP_MPP8_MORC2-KO_rep2 \
[SRR5287080]=ChIP_MPP8_MPP8-KO_rep1 \
[SRR5287081]=ChIP_MPP8_MPP8-KO_rep2 \
[SRR5287082]=ChIP_MPP8_TASOR-KO_rep1 \
[SRR5287083]=ChIP_MPP8_TASOR-KO_rep2 \
[SRR5287084]=ChIP_TASOR_WT_rep1 \
[SRR5287085]=ChIP_TASOR_WT_rep2 \
[SRR5287086]=ChIP_TASOR_MORC2-KO_rep1 \
[SRR5287087]=ChIP_TASOR_MORC2-KO_rep2 \
[SRR5287088]=ChIP_TASOR_MPP8-KO_rep1 \
[SRR5287089]=ChIP_TASOR_MPP8-KO_rep2 \
[SRR5287090]=ChIP_TASOR_TASOR-KO_rep1 \
[SRR5287091]=ChIP_TASOR_TASOR-KO_rep2 \
[SRR5287092]=Input_MORC2_WT_rep1 \
[SRR5287093]=Input_MORC2_WT_rep2 \
[SRR5287094]=Input_MORC2_MORC2-KO_rep1 \
[SRR5287095]=Input_MORC2_MORC2-KO_rep2 \
[SRR5287096]=Input_MORC2_MPP8-KO_rep1 \
[SRR5287097]=Input_MORC2_MPP8-KO_rep2 \
[SRR5287098]=Input_MORC2_TASOR-KO_rep1 \
[SRR5287099]=Input_MORC2_TASOR-KO_rep2 \
[SRR5287100]=ChIP_H3K9me3_WT_rep1 \
[SRR5287101]=ChIP_H3K9me3_WT_rep2 \
[SRR5287102]=ChIP_H3K9me3_MORC2-KO_rep1 \
[SRR5287103]=ChIP_H3K9me3_MORC2-KO_rep2 \
[SRR5287104]=ChIP_H3K9me3_TASOR-KO_rep1 \
[SRR5287105]=ChIP_H3K9me3_TASOR-KO_rep2 \
[SRR5287106]=Input_H3K9me3_WT_rep1 \
[SRR5287107]=Input_H3K9me3_WT_rep2 \
[SRR5287108]=Input_H3K9me3_MORC2-KO_rep1 \
[SRR5287109]=Input_H3K9me3_MORC2-KO_rep2 \
[SRR5287110]=Input_H3K9me3_TASOR-KO_rep1 \
[SRR5287111]=Input_H3K9me3_TASOR-KO_rep2 \
[SRR5287116]=ChIP_H3K9me3_WT_rep3 \
[SRR5287117]=ChIP_H3K9me3_WT_rep4 \
[SRR5287118]=ChIP_H3K9me3_MPP8-KO_rep1 \
[SRR5287119]=ChIP_H3K9me3_MPP8-KO_rep2 \
[SRR5287120]=Input_H3K9me2or3_WT_rep1 \
[SRR5287121]=Input_H3K9me2or3_WT_rep2 \
[SRR5287122]=Input_H3K9me2or3_MPP8-KO_rep1 \
[SRR5287123]=Input_H3K9me2or3_MPP8-KO_rep2)

for id in ${!pairs[@]}
do
  for filename in ${id}_{1,2}.fastq.gz; do mv "$filename" "${pairs[$id]}_$filename"; done;
done
```



### Quality check

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/chipseq/fastq

mkdir ../fastqc

for fq in *.fastq.gz
do
  bname=${fq%.fastq.gz}
  sbatch -J $bname -o ../fastqc/$bname.log --mem 4G --wrap "fastqc --noextract --nogroup -q -o ../fastqc $fq"
done
```



### Trim illumina adapters and quality trimming

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/chipseq/fastq

mkdir ../fastq_trimmed

for fq1 in *_1.fastq.gz
do
  fq2=${fq1/_1./_2.}
  bname=${fq1%_1.fastq.gz}
  #echo $fq1, $fq2, $bname
  sbatch -J $bname -o ../fastq_trimmed/$bname.log --mem 4G -t 1-0 --wrap "cutadapt -a AGATCGGAAGAGC -A AGATCGGAAGAGC -m 50 -q 10 -o ../fastq_trimmed/$fq1 -p ../fastq_trimmed/$fq2 $fq1 $fq2 > ../fastq_trimmed/$bname.txt"
done

cd ../fastq_trimmed

tail *.log

grep "Read 1 with adapter" *.txt
# ChIP_H3K9me3_MORC2-KO_rep1_SRR5287102.txt:  Read 1 with adapter:                 967,977 (3.6%)
# ChIP_H3K9me3_MORC2-KO_rep2_SRR5287103.txt:  Read 1 with adapter:                 914,451 (3.3%)
# ChIP_H3K9me3_MPP8-KO_rep1_SRR5287118.txt:  Read 1 with adapter:                 822,090 (3.1%)
# ChIP_H3K9me3_MPP8-KO_rep2_SRR5287119.txt:  Read 1 with adapter:                 904,388 (3.1%)
# ChIP_H3K9me3_TASOR-KO_rep1_SRR5287104.txt:  Read 1 with adapter:                 769,745 (3.3%)
# ChIP_H3K9me3_TASOR-KO_rep2_SRR5287105.txt:  Read 1 with adapter:                 770,138 (3.1%)
# ChIP_H3K9me3_WT_rep1_SRR5287100.txt:  Read 1 with adapter:               1,136,975 (3.1%)
# ChIP_H3K9me3_WT_rep2_SRR5287101.txt:  Read 1 with adapter:               1,007,607 (3.1%)
# ChIP_H3K9me3_WT_rep3_SRR5287116.txt:  Read 1 with adapter:                 770,442 (3.0%)
# ChIP_H3K9me3_WT_rep4_SRR5287117.txt:  Read 1 with adapter:                 699,252 (3.0%)
# ChIP_MORC2_MORC2-KO_rep1_SRR5287070.txt:  Read 1 with adapter:                 365,344 (3.1%)
# ChIP_MORC2_MORC2-KO_rep2_SRR5287071.txt:  Read 1 with adapter:                 349,441 (3.1%)
# ChIP_MORC2_MPP8-KO_rep1_SRR5287072.txt:  Read 1 with adapter:                 380,302 (3.0%)
# ChIP_MORC2_MPP8-KO_rep2_SRR5287073.txt:  Read 1 with adapter:                 326,631 (3.1%)
# ChIP_MORC2_TASOR-KO_rep1_SRR5287074.txt:  Read 1 with adapter:                 320,117 (3.1%)
# ChIP_MORC2_TASOR-KO_rep2_SRR5287075.txt:  Read 1 with adapter:                 515,231 (3.1%)
# ChIP_MORC2_WT_rep1_SRR5287068.txt:  Read 1 with adapter:                 386,872 (3.0%)
# ChIP_MORC2_WT_rep2_SRR5287069.txt:  Read 1 with adapter:                 364,177 (3.3%)
# ChIP_MPP8_MORC2-KO_rep1_SRR5287078.txt:  Read 1 with adapter:                 405,522 (3.0%)
# ChIP_MPP8_MORC2-KO_rep2_SRR5287079.txt:  Read 1 with adapter:                 398,145 (3.0%)
# ChIP_MPP8_MPP8-KO_rep1_SRR5287080.txt:  Read 1 with adapter:                 376,351 (3.0%)
# ChIP_MPP8_MPP8-KO_rep2_SRR5287081.txt:  Read 1 with adapter:                 476,951 (3.0%)
# ChIP_MPP8_TASOR-KO_rep1_SRR5287082.txt:  Read 1 with adapter:                 818,705 (3.0%)
# ChIP_MPP8_TASOR-KO_rep2_SRR5287083.txt:  Read 1 with adapter:                 455,165 (3.0%)
# ChIP_MPP8_WT_rep1_SRR5287076.txt:  Read 1 with adapter:                 548,098 (3.0%)
# ChIP_MPP8_WT_rep2_SRR5287077.txt:  Read 1 with adapter:                 403,184 (2.9%)
# ChIP_TASOR_MORC2-KO_rep1_SRR5287086.txt:  Read 1 with adapter:                 638,528 (3.1%)
# ChIP_TASOR_MORC2-KO_rep2_SRR5287087.txt:  Read 1 with adapter:                 445,515 (3.1%)
# ChIP_TASOR_MPP8-KO_rep1_SRR5287088.txt:  Read 1 with adapter:                 360,503 (3.1%)
# ChIP_TASOR_MPP8-KO_rep2_SRR5287089.txt:  Read 1 with adapter:                 415,808 (3.0%)
# ChIP_TASOR_TASOR-KO_rep1_SRR5287090.txt:  Read 1 with adapter:                 389,317 (3.1%)
# ChIP_TASOR_TASOR-KO_rep2_SRR5287091.txt:  Read 1 with adapter:                 389,012 (3.0%)
# ChIP_TASOR_WT_rep1_SRR5287084.txt:  Read 1 with adapter:                 516,225 (3.4%)
# ChIP_TASOR_WT_rep2_SRR5287085.txt:  Read 1 with adapter:                 454,151 (3.1%)
# Input_H3K9me2or3_MPP8-KO_rep1_SRR5287122.txt:  Read 1 with adapter:                 774,733 (3.1%)
# Input_H3K9me2or3_MPP8-KO_rep2_SRR5287123.txt:  Read 1 with adapter:                 455,610 (3.0%)
# Input_H3K9me2or3_WT_rep1_SRR5287120.txt:  Read 1 with adapter:               1,152,873 (3.2%)
# Input_H3K9me2or3_WT_rep2_SRR5287121.txt:  Read 1 with adapter:                 708,398 (3.1%)
# Input_H3K9me3_MORC2-KO_rep1_SRR5287108.txt:  Read 1 with adapter:                 547,331 (3.0%)
# Input_H3K9me3_MORC2-KO_rep2_SRR5287109.txt:  Read 1 with adapter:                 275,047 (2.8%)
# Input_H3K9me3_TASOR-KO_rep1_SRR5287110.txt:  Read 1 with adapter:                 675,834 (2.8%)
# Input_H3K9me3_TASOR-KO_rep2_SRR5287111.txt:  Read 1 with adapter:                 255,284 (2.9%)
# Input_H3K9me3_WT_rep1_SRR5287106.txt:  Read 1 with adapter:                 168,092 (2.9%)
# Input_H3K9me3_WT_rep2_SRR5287107.txt:  Read 1 with adapter:                 278,622 (2.9%)
# Input_MORC2_MORC2-KO_rep1_SRR5287094.txt:  Read 1 with adapter:                 355,153 (2.9%)
# Input_MORC2_MORC2-KO_rep2_SRR5287095.txt:  Read 1 with adapter:                 329,794 (2.9%)
# Input_MORC2_MPP8-KO_rep1_SRR5287096.txt:  Read 1 with adapter:                 333,820 (2.8%)
# Input_MORC2_MPP8-KO_rep2_SRR5287097.txt:  Read 1 with adapter:                 528,136 (3.0%)
# Input_MORC2_TASOR-KO_rep1_SRR5287098.txt:  Read 1 with adapter:                 375,608 (3.1%)
# Input_MORC2_TASOR-KO_rep2_SRR5287099.txt:  Read 1 with adapter:                 283,298 (3.1%)
# Input_MORC2_WT_rep1_SRR5287092.txt:  Read 1 with adapter:                 238,020 (3.0%)
# Input_MORC2_WT_rep2_SRR5287093.txt:  Read 1 with adapter:                 316,152 (3.0%)

grep "Read 2 with adapter" *.txt
# ChIP_H3K9me3_MORC2-KO_rep1_SRR5287102.txt:  Read 2 with adapter:                 831,957 (3.1%)
# ChIP_H3K9me3_MORC2-KO_rep2_SRR5287103.txt:  Read 2 with adapter:                 780,823 (2.8%)
# ChIP_H3K9me3_MPP8-KO_rep1_SRR5287118.txt:  Read 2 with adapter:                 675,770 (2.6%)
# ChIP_H3K9me3_MPP8-KO_rep2_SRR5287119.txt:  Read 2 with adapter:                 742,414 (2.5%)
# ChIP_H3K9me3_TASOR-KO_rep1_SRR5287104.txt:  Read 2 with adapter:                 659,359 (2.8%)
# ChIP_H3K9me3_TASOR-KO_rep2_SRR5287105.txt:  Read 2 with adapter:                 652,008 (2.6%)
# ChIP_H3K9me3_WT_rep1_SRR5287100.txt:  Read 2 with adapter:                 961,886 (2.7%)
# ChIP_H3K9me3_WT_rep2_SRR5287101.txt:  Read 2 with adapter:                 869,373 (2.7%)
# ChIP_H3K9me3_WT_rep3_SRR5287116.txt:  Read 2 with adapter:                 645,266 (2.5%)
# ChIP_H3K9me3_WT_rep4_SRR5287117.txt:  Read 2 with adapter:                 574,693 (2.5%)
# ChIP_MORC2_MORC2-KO_rep1_SRR5287070.txt:  Read 2 with adapter:                 308,488 (2.7%)
# ChIP_MORC2_MORC2-KO_rep2_SRR5287071.txt:  Read 2 with adapter:                 300,318 (2.6%)
# ChIP_MORC2_MPP8-KO_rep1_SRR5287072.txt:  Read 2 with adapter:                 332,882 (2.6%)
# ChIP_MORC2_MPP8-KO_rep2_SRR5287073.txt:  Read 2 with adapter:                 277,338 (2.6%)
# ChIP_MORC2_TASOR-KO_rep1_SRR5287074.txt:  Read 2 with adapter:                 274,209 (2.6%)
# ChIP_MORC2_TASOR-KO_rep2_SRR5287075.txt:  Read 2 with adapter:                 443,282 (2.6%)
# ChIP_MORC2_WT_rep1_SRR5287068.txt:  Read 2 with adapter:                 330,905 (2.6%)
# ChIP_MORC2_WT_rep2_SRR5287069.txt:  Read 2 with adapter:                 288,358 (2.6%)
# ChIP_MPP8_MORC2-KO_rep1_SRR5287078.txt:  Read 2 with adapter:                 359,222 (2.7%)
# ChIP_MPP8_MORC2-KO_rep2_SRR5287079.txt:  Read 2 with adapter:                 348,244 (2.6%)
# ChIP_MPP8_MPP8-KO_rep1_SRR5287080.txt:  Read 2 with adapter:                 333,042 (2.6%)
# ChIP_MPP8_MPP8-KO_rep2_SRR5287081.txt:  Read 2 with adapter:                 413,708 (2.6%)
# ChIP_MPP8_TASOR-KO_rep1_SRR5287082.txt:  Read 2 with adapter:                 693,873 (2.6%)
# ChIP_MPP8_TASOR-KO_rep2_SRR5287083.txt:  Read 2 with adapter:                 384,975 (2.6%)
# ChIP_MPP8_WT_rep1_SRR5287076.txt:  Read 2 with adapter:                 493,921 (2.7%)
# ChIP_MPP8_WT_rep2_SRR5287077.txt:  Read 2 with adapter:                 371,061 (2.7%)
# ChIP_TASOR_MORC2-KO_rep1_SRR5287086.txt:  Read 2 with adapter:                 518,598 (2.5%)
# ChIP_TASOR_MORC2-KO_rep2_SRR5287087.txt:  Read 2 with adapter:                 364,892 (2.5%)
# ChIP_TASOR_MPP8-KO_rep1_SRR5287088.txt:  Read 2 with adapter:                 291,921 (2.5%)
# ChIP_TASOR_MPP8-KO_rep2_SRR5287089.txt:  Read 2 with adapter:                 360,860 (2.6%)
# ChIP_TASOR_TASOR-KO_rep1_SRR5287090.txt:  Read 2 with adapter:                 323,276 (2.6%)
# ChIP_TASOR_TASOR-KO_rep2_SRR5287091.txt:  Read 2 with adapter:                 332,137 (2.6%)
# ChIP_TASOR_WT_rep1_SRR5287084.txt:  Read 2 with adapter:                 380,060 (2.5%)
# ChIP_TASOR_WT_rep2_SRR5287085.txt:  Read 2 with adapter:                 369,768 (2.5%)
# Input_H3K9me2or3_MPP8-KO_rep1_SRR5287122.txt:  Read 2 with adapter:                 664,850 (2.7%)
# Input_H3K9me2or3_MPP8-KO_rep2_SRR5287123.txt:  Read 2 with adapter:                 394,683 (2.6%)
# Input_H3K9me2or3_WT_rep1_SRR5287120.txt:  Read 2 with adapter:               1,004,194 (2.8%)
# Input_H3K9me2or3_WT_rep2_SRR5287121.txt:  Read 2 with adapter:                 617,400 (2.7%)
# Input_H3K9me3_MORC2-KO_rep1_SRR5287108.txt:  Read 2 with adapter:                 464,887 (2.6%)
# Input_H3K9me3_MORC2-KO_rep2_SRR5287109.txt:  Read 2 with adapter:                 230,707 (2.3%)
# Input_H3K9me3_TASOR-KO_rep1_SRR5287110.txt:  Read 2 with adapter:                 578,008 (2.4%)
# Input_H3K9me3_TASOR-KO_rep2_SRR5287111.txt:  Read 2 with adapter:                 217,391 (2.5%)
# Input_H3K9me3_WT_rep1_SRR5287106.txt:  Read 2 with adapter:                 146,174 (2.5%)
# Input_H3K9me3_WT_rep2_SRR5287107.txt:  Read 2 with adapter:                 244,661 (2.6%)
# Input_MORC2_MORC2-KO_rep1_SRR5287094.txt:  Read 2 with adapter:                 308,350 (2.5%)
# Input_MORC2_MORC2-KO_rep2_SRR5287095.txt:  Read 2 with adapter:                 282,519 (2.5%)
# Input_MORC2_MPP8-KO_rep1_SRR5287096.txt:  Read 2 with adapter:                 285,532 (2.4%)
# Input_MORC2_MPP8-KO_rep2_SRR5287097.txt:  Read 2 with adapter:                 434,158 (2.5%)
# Input_MORC2_TASOR-KO_rep1_SRR5287098.txt:  Read 2 with adapter:                 303,267 (2.5%)
# Input_MORC2_TASOR-KO_rep2_SRR5287099.txt:  Read 2 with adapter:                 219,556 (2.4%)
# Input_MORC2_WT_rep1_SRR5287092.txt:  Read 2 with adapter:                 208,124 (2.6%)
# Input_MORC2_WT_rep2_SRR5287093.txt:  Read 2 with adapter:                 268,851 (2.5%)

grep "Pairs written (passing filters):" *.txt
# ChIP_H3K9me3_MORC2-KO_rep1_SRR5287102.txt:Pairs written (passing filters):    27,204,453 (99.8%)
# ChIP_H3K9me3_MORC2-KO_rep2_SRR5287103.txt:Pairs written (passing filters):    27,923,286 (99.8%)
# ChIP_H3K9me3_MPP8-KO_rep1_SRR5287118.txt:Pairs written (passing filters):    26,352,877 (99.9%)
# ChIP_H3K9me3_MPP8-KO_rep2_SRR5287119.txt:Pairs written (passing filters):    29,343,533 (99.9%)
# ChIP_H3K9me3_TASOR-KO_rep1_SRR5287104.txt:Pairs written (passing filters):    23,525,622 (99.8%)
# ChIP_H3K9me3_TASOR-KO_rep2_SRR5287105.txt:Pairs written (passing filters):    24,671,721 (99.8%)
# ChIP_H3K9me3_WT_rep1_SRR5287100.txt:Pairs written (passing filters):    36,179,780 (99.8%)
# ChIP_H3K9me3_WT_rep2_SRR5287101.txt:Pairs written (passing filters):    32,566,532 (99.8%)
# ChIP_H3K9me3_WT_rep3_SRR5287116.txt:Pairs written (passing filters):    25,612,324 (99.9%)
# ChIP_H3K9me3_WT_rep4_SRR5287117.txt:Pairs written (passing filters):    23,415,111 (99.9%)
# ChIP_MORC2_MORC2-KO_rep1_SRR5287070.txt:Pairs written (passing filters):    11,592,535 (99.7%)
# ChIP_MORC2_MORC2-KO_rep2_SRR5287071.txt:Pairs written (passing filters):    11,315,707 (99.8%)
# ChIP_MORC2_MPP8-KO_rep1_SRR5287072.txt:Pairs written (passing filters):    12,672,733 (99.9%)
# ChIP_MORC2_MPP8-KO_rep2_SRR5287073.txt:Pairs written (passing filters):    10,526,876 (99.7%)
# ChIP_MORC2_TASOR-KO_rep1_SRR5287074.txt:Pairs written (passing filters):    10,372,455 (99.7%)
# ChIP_MORC2_TASOR-KO_rep2_SRR5287075.txt:Pairs written (passing filters):    16,705,892 (99.7%)
# ChIP_MORC2_WT_rep1_SRR5287068.txt:Pairs written (passing filters):    12,710,662 (99.9%)
# ChIP_MORC2_WT_rep2_SRR5287069.txt:Pairs written (passing filters):    11,090,545 (99.6%)
# ChIP_MPP8_MORC2-KO_rep1_SRR5287078.txt:Pairs written (passing filters):    13,326,050 (99.8%)
# ChIP_MPP8_MORC2-KO_rep2_SRR5287079.txt:Pairs written (passing filters):    13,124,287 (99.8%)
# ChIP_MPP8_MPP8-KO_rep1_SRR5287080.txt:Pairs written (passing filters):    12,608,977 (99.8%)
# ChIP_MPP8_MPP8-KO_rep2_SRR5287081.txt:Pairs written (passing filters):    15,683,547 (99.8%)
# ChIP_MPP8_TASOR-KO_rep1_SRR5287082.txt:Pairs written (passing filters):    26,842,520 (99.9%)
# ChIP_MPP8_TASOR-KO_rep2_SRR5287083.txt:Pairs written (passing filters):    15,035,872 (99.9%)
# ChIP_MPP8_WT_rep1_SRR5287076.txt:Pairs written (passing filters):    18,441,818 (99.8%)
# ChIP_MPP8_WT_rep2_SRR5287077.txt:Pairs written (passing filters):    13,659,941 (99.8%)
# ChIP_TASOR_MORC2-KO_rep1_SRR5287086.txt:Pairs written (passing filters):    20,604,596 (99.8%)
# ChIP_TASOR_MORC2-KO_rep2_SRR5287087.txt:Pairs written (passing filters):    14,420,814 (99.8%)
# ChIP_TASOR_MPP8-KO_rep1_SRR5287088.txt:Pairs written (passing filters):    11,654,863 (99.8%)
# ChIP_TASOR_MPP8-KO_rep2_SRR5287089.txt:Pairs written (passing filters):    13,849,786 (99.8%)
# ChIP_TASOR_TASOR-KO_rep1_SRR5287090.txt:Pairs written (passing filters):    12,471,700 (99.7%)
# ChIP_TASOR_TASOR-KO_rep2_SRR5287091.txt:Pairs written (passing filters):    12,834,705 (99.8%)
# ChIP_TASOR_WT_rep1_SRR5287084.txt:Pairs written (passing filters):    14,926,997 (99.5%)
# ChIP_TASOR_WT_rep2_SRR5287085.txt:Pairs written (passing filters):    14,615,603 (99.8%)
# Input_H3K9me2or3_MPP8-KO_rep1_SRR5287122.txt:Pairs written (passing filters):    24,911,365 (100.0%)
# Input_H3K9me2or3_MPP8-KO_rep2_SRR5287123.txt:Pairs written (passing filters):    15,249,634 (100.0%)
# Input_H3K9me2or3_WT_rep1_SRR5287120.txt:Pairs written (passing filters):    36,328,140 (100.0%)
# Input_H3K9me2or3_WT_rep2_SRR5287121.txt:Pairs written (passing filters):    22,681,472 (100.0%)
# Input_H3K9me3_MORC2-KO_rep1_SRR5287108.txt:Pairs written (passing filters):    17,946,404 (99.6%)
# Input_H3K9me3_MORC2-KO_rep2_SRR5287109.txt:Pairs written (passing filters):     9,956,021 (99.7%)
# Input_H3K9me3_TASOR-KO_rep1_SRR5287110.txt:Pairs written (passing filters):    24,360,046 (99.9%)
# Input_H3K9me3_TASOR-KO_rep2_SRR5287111.txt:Pairs written (passing filters):     8,849,862 (99.9%)
# Input_H3K9me3_WT_rep1_SRR5287106.txt:Pairs written (passing filters):     5,842,167 (99.8%)
# Input_H3K9me3_WT_rep2_SRR5287107.txt:Pairs written (passing filters):     9,453,904 (99.9%)
# Input_MORC2_MORC2-KO_rep1_SRR5287094.txt:Pairs written (passing filters):    12,371,234 (99.9%)
# Input_MORC2_MORC2-KO_rep2_SRR5287095.txt:Pairs written (passing filters):    11,431,272 (99.9%)
# Input_MORC2_MPP8-KO_rep1_SRR5287096.txt:Pairs written (passing filters):    11,788,158 (99.8%)
# Input_MORC2_MPP8-KO_rep2_SRR5287097.txt:Pairs written (passing filters):    17,447,740 (99.9%)
# Input_MORC2_TASOR-KO_rep1_SRR5287098.txt:Pairs written (passing filters):    12,166,568 (99.9%)
# Input_MORC2_TASOR-KO_rep2_SRR5287099.txt:Pairs written (passing filters):     9,058,762 (99.9%)
# Input_MORC2_WT_rep1_SRR5287092.txt:Pairs written (passing filters):     8,055,272 (99.9%)
# Input_MORC2_WT_rep2_SRR5287093.txt:Pairs written (passing filters):    10,615,246 (99.9%)
```



### Alignment

#### Prepare genome

```bash
cd /projects/qbio/bifo/users/sergio/reference/gencode/release35_GRCh38.p13/fasta
sbatch -J index --mem 16G -t 1-0 --wrap "bwa index GRCh38.p13.genome.fa"
rm slurm-49117021.out
```


#### Align and sort

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/chipseq/fastq_trimmed

mkdir ../bam

ref=/projects/qbio/bifo/users/sergio/reference/gencode/release35_GRCh38.p13/fasta/GRCh38.p13.genome.fa

for fq1 in *_1.fastq.gz
do
  bname=${fq1%_1.fastq.gz}
  fq2=${fq1/_1./_2.}
  #echo $fq1,$fq2
  sbatch -J $bname -o ../bam/$bname.log --mem 32G -t 1-0 --wrap "bwa mem -t 20 -M $ref $fq1 $fq2 | \
  samtools view -@ 20 -b - | \
  samtools sort -@ 20 -T /projects/qbio/bifo/users/sergio/tmp/$bname -o ../bam/$bname.tmp.bam -"
done

cd ../bam
tail *.log
```


#### Mark duplicates, index and flagstat

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/chipseq/bam

mkdir ../flagstat

for bam in *.tmp.bam
do
  id=${bam%.tmp.bam}
  sbatch -J $id -o $id.markdup.log --mem 16G -t 1-0 --wrap "sambamba markdup -t 20 $bam ${id}.bam 2> ${id}.markdup.txt && \
  samtools flagstat ${id}.bam > ../flagstat/${id}.txt"
done

ls -lh *.markdup.log

ls *.markdup.txt | xargs head

ls ../flagstat/*.txt | xargs head -n13
```



### htseq-count

In order for `htseq-count` to work, I need to remove `/projects/qbio/bifo/python3/lib/python3.6/site-packages` from PYTHONPATH so that `numpy` does not get confused.
The same applies to `deeptools`

```bash
cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/chipseq/bam

mkdir ../htseq

gtf='/projects/qbio/bifo/users/sergio/reference/gencode/release35_GRCh38.p13/gtf/gencode.v35.annotation.gtf'

for bam in `ls *.bam | grep -v "tmp"`
do
	bname=${bam%.bam}
	sbatch -J $bname -o ../htseq/$bname.log --mem 16G -t 1-0 --wrap "htseq-count -f bam -r pos -s no -t exon -i gene_id -m intersection-strict $bam $gtf > ../htseq/$bname.txt"
done

cd ../htseq
tail *.log # looks fine

ls *.txt | xargs wc -l
   # 60661 ChIP_H3K9me3_MORC2-KO_rep1_SRR5287102.txt
   # 60661 ChIP_H3K9me3_MORC2-KO_rep2_SRR5287103.txt
   # 60661 ChIP_H3K9me3_MPP8-KO_rep1_SRR5287118.txt
   # 60661 ChIP_H3K9me3_MPP8-KO_rep2_SRR5287119.txt
   # 60661 ChIP_H3K9me3_TASOR-KO_rep1_SRR5287104.txt
   # 60661 ChIP_H3K9me3_TASOR-KO_rep2_SRR5287105.txt
   # 60661 ChIP_H3K9me3_WT_rep1_SRR5287100.txt
   # 60661 ChIP_H3K9me3_WT_rep2_SRR5287101.txt
   # 60661 ChIP_H3K9me3_WT_rep3_SRR5287116.txt
   # 60661 ChIP_H3K9me3_WT_rep4_SRR5287117.txt
   # 60661 ChIP_MORC2_MORC2-KO_rep1_SRR5287070.txt
   # 60661 ChIP_MORC2_MORC2-KO_rep2_SRR5287071.txt
   # 60661 ChIP_MORC2_MPP8-KO_rep1_SRR5287072.txt
   # 60661 ChIP_MORC2_MPP8-KO_rep2_SRR5287073.txt
   # 60661 ChIP_MORC2_TASOR-KO_rep1_SRR5287074.txt
   # 60661 ChIP_MORC2_TASOR-KO_rep2_SRR5287075.txt
   # 60661 ChIP_MORC2_WT_rep1_SRR5287068.txt
   # 60661 ChIP_MORC2_WT_rep2_SRR5287069.txt
   # 60661 ChIP_MPP8_MORC2-KO_rep1_SRR5287078.txt
   # 60661 ChIP_MPP8_MORC2-KO_rep2_SRR5287079.txt
   # 60661 ChIP_MPP8_MPP8-KO_rep1_SRR5287080.txt
   # 60661 ChIP_MPP8_MPP8-KO_rep2_SRR5287081.txt
   # 60661 ChIP_MPP8_TASOR-KO_rep1_SRR5287082.txt
   # 60661 ChIP_MPP8_TASOR-KO_rep2_SRR5287083.txt
   # 60661 ChIP_MPP8_WT_rep1_SRR5287076.txt
   # 60661 ChIP_MPP8_WT_rep2_SRR5287077.txt
   # 60661 ChIP_TASOR_MORC2-KO_rep1_SRR5287086.txt
   # 60661 ChIP_TASOR_MORC2-KO_rep2_SRR5287087.txt
   # 60661 ChIP_TASOR_MPP8-KO_rep1_SRR5287088.txt
   # 60661 ChIP_TASOR_MPP8-KO_rep2_SRR5287089.txt
   # 60661 ChIP_TASOR_TASOR-KO_rep1_SRR5287090.txt
   # 60661 ChIP_TASOR_TASOR-KO_rep2_SRR5287091.txt
   # 60661 ChIP_TASOR_WT_rep1_SRR5287084.txt
   # 60661 ChIP_TASOR_WT_rep2_SRR5287085.txt
   # 60661 Input_H3K9me2or3_MPP8-KO_rep1_SRR5287122.txt
   # 60661 Input_H3K9me2or3_MPP8-KO_rep2_SRR5287123.txt
   # 60661 Input_H3K9me2or3_WT_rep1_SRR5287120.txt
   # 60661 Input_H3K9me2or3_WT_rep2_SRR5287121.txt
   # 60661 Input_H3K9me3_MORC2-KO_rep1_SRR5287108.txt
   # 60661 Input_H3K9me3_MORC2-KO_rep2_SRR5287109.txt
   # 60661 Input_H3K9me3_TASOR-KO_rep1_SRR5287110.txt
   # 60661 Input_H3K9me3_TASOR-KO_rep2_SRR5287111.txt
   # 60661 Input_H3K9me3_WT_rep1_SRR5287106.txt
   # 60661 Input_H3K9me3_WT_rep2_SRR5287107.txt
   # 60661 Input_MORC2_MORC2-KO_rep1_SRR5287094.txt
   # 60661 Input_MORC2_MORC2-KO_rep2_SRR5287095.txt
   # 60661 Input_MORC2_MPP8-KO_rep1_SRR5287096.txt
   # 60661 Input_MORC2_MPP8-KO_rep2_SRR5287097.txt
   # 60661 Input_MORC2_TASOR-KO_rep1_SRR5287098.txt
   # 60661 Input_MORC2_TASOR-KO_rep2_SRR5287099.txt
   # 60661 Input_MORC2_WT_rep1_SRR5287092.txt
   # 60661 Input_MORC2_WT_rep2_SRR5287093.txt
```





## Tables

### Gene info + RNA-seq

```r
#cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/table
#R

library(data.table)

# Enlarge the view width when printing tables
options(width = 250)

# Load genes info
genes <- fread("/projects/qbio/bifo/users/sergio/repository/20201122_hush/annotation/gencode.v35.annotation.gene_type.intronless.bed")
setnames(genes, c("chr", "start", "end", "gene_name", "gene_id", "strand", "gene_type", "intronless"))

# Load rnaseq
rnaseq <- fread("tableCat.py -i /projects/qbio/bifo/users/sergio/repository/20201122_hush/rnaseq/rsem/{WT,TASOR-KO}_*.genes.results -r .genes.results -H")
rnaseq[, file_id := sapply(file_id, function(x) paste(unlist(strsplit(x, "_"))[1:2], collapse="_"))]
rnaseq[file_id=="TASOR-KO_rep1", file_id := "TASORKO_rep1"]
rnaseq[file_id=="TASOR-KO_rep2", file_id := "TASORKO_rep2"]
rnaseq_dcast <- dcast(rnaseq, gene_id ~ file_id, value.var = c("TPM", "length"))
rnaseq_dcast[, gene_id := sapply(gene_id, function(x) unlist(strsplit(x, "_"))[1])]

# Merge
tab <- merge(genes, rnaseq_dcast, by="gene_id")[,.(gene_name, gene_id, strand, length_WT_rep1, gene_type, intronless, TPM_WT_rep1, TPM_WT_rep2, TPM_TASORKO_rep1, TPM_TASORKO_rep2)]

# Write
write.table(tab, "20201206_grandtable_v1.txt", sep="\t", quote = FALSE, row.names = FALSE, col.names = TRUE)
```



### Gene info + RNA-seq + ChIP-seq

```r
#cd /projects/qbio/bifo/users/sergio/repository/20201122_hush/table
#R

library(data.table)
library(GenomicFeatures)
library(edgeR)

# Enlarge the view width when printing tables
options(width = 250)

# Load genes info
genes <- fread("/projects/qbio/bifo/users/sergio/repository/20201122_hush/annotation/gencode.v35.annotation.gene_type.intronless.bed")
setnames(genes, c("chr", "start", "end", "gene_name", "gene_id", "strand", "gene_type", "intronless"))

# Load gene sizes
txdb <- makeTxDbFromGFF("/projects/qbio/bifo/users/sergio/reference/gencode/release35_GRCh38.p13/gtf/gencode.v35.annotation.gtf", format="gtf")
genes_length <- data.table(data.frame(genes(txdb)))[, c("gene_id", "width")]
genes_length[, length := width]
genes_length[, width := NULL]

# Load rnaseq
rnaseq <- fread("tableCat.py -i /projects/qbio/bifo/users/sergio/repository/20201122_hush/rnaseq/rsem/*.genes.results -r .genes.results -H")
rnaseq[, file_id := sapply(file_id, function(x) paste(unlist(strsplit(x, "_"))[1:2], collapse="_"))]
rnaseq[file_id=="MORC2-KO_rep1", file_id := "MORC2KO_rep1"]
rnaseq[file_id=="MORC2-KO_rep2", file_id := "MORC2KO_rep2"]
rnaseq[file_id=="MPP8-KO_rep1", file_id := "MPP8KO_rep1"]
rnaseq[file_id=="MPP8-KO_rep2", file_id := "MPP8KO_rep2"]
rnaseq[file_id=="TASOR-KO_rep1", file_id := "TASORKO_rep1"]
rnaseq[file_id=="TASOR-KO_rep2", file_id := "TASORKO_rep2"]
rnaseq_dcast <- dcast(rnaseq, gene_id ~ file_id, value.var = c("TPM", "length"))
rnaseq_dcast[, gene_id := sapply(gene_id, function(x) unlist(strsplit(x, "_"))[1])]

# Load chipseq
chipseq <- fread("tableCat.py -i /projects/qbio/bifo/users/sergio/repository/20201122_hush/chipseq/htseq/*.txt -r .txt")
chipseq <- chipseq[V1 %like% 'ENSG'] # omitting bottom five lines in each htseq file
setnames(chipseq, c("gene_id", "count", "file_id"))
chipseq[file_id=="ChIP_H3K9me3_MORC2-KO_rep1_SRR5287102", file_id := "ChIP_H3K9me3_MORC2KO_rep1"]
chipseq[file_id=="ChIP_H3K9me3_MORC2-KO_rep2_SRR5287103", file_id := "ChIP_H3K9me3_MORC2KO_rep2"]
chipseq[file_id=="ChIP_H3K9me3_MPP8-KO_rep1_SRR5287118", file_id := "ChIP_H3K9me3_MPP8KO_rep1"]
chipseq[file_id=="ChIP_H3K9me3_MPP8-KO_rep2_SRR5287119", file_id := "ChIP_H3K9me3_MPP8KO_rep2"]
chipseq[file_id=="ChIP_H3K9me3_TASOR-KO_rep1_SRR5287104", file_id := "ChIP_H3K9me3_TASORKO_rep1"]
chipseq[file_id=="ChIP_H3K9me3_TASOR-KO_rep2_SRR5287105", file_id := "ChIP_H3K9me3_TASORKO_rep2"]
chipseq[file_id=="ChIP_H3K9me3_WT_rep1_SRR5287100", file_id := "ChIP_H3K9me3_WT_rep1"]
chipseq[file_id=="ChIP_H3K9me3_WT_rep2_SRR5287101", file_id := "ChIP_H3K9me3_WT_rep2"]
chipseq[file_id=="ChIP_H3K9me3_WT_rep3_SRR5287116", file_id := "ChIP_H3K9me3_WT_rep3"]
chipseq[file_id=="ChIP_H3K9me3_WT_rep4_SRR5287117", file_id := "ChIP_H3K9me3_WT_rep4"]
chipseq[file_id=="ChIP_MORC2_MORC2-KO_rep1_SRR5287070", file_id := "ChIP_MORC2_MORC2KO_rep1"]
chipseq[file_id=="ChIP_MORC2_MORC2-KO_rep2_SRR5287071", file_id := "ChIP_MORC2_MORC2KO_rep2"]
chipseq[file_id=="ChIP_MORC2_MPP8-KO_rep1_SRR5287072", file_id := "ChIP_MORC2_MPP8KO_rep1"]
chipseq[file_id=="ChIP_MORC2_MPP8-KO_rep2_SRR5287073", file_id := "ChIP_MORC2_MPP8KO_rep2"]
chipseq[file_id=="ChIP_MORC2_TASOR-KO_rep1_SRR5287074", file_id := "ChIP_MORC2_TASORKO_rep1"]
chipseq[file_id=="ChIP_MORC2_TASOR-KO_rep2_SRR5287075", file_id := "ChIP_MORC2_TASORKO_rep2"]
chipseq[file_id=="ChIP_MORC2_WT_rep1_SRR5287068", file_id := "ChIP_MORC2_WT_rep1"]
chipseq[file_id=="ChIP_MORC2_WT_rep2_SRR5287069", file_id := "ChIP_MORC2_WT_rep2"]
chipseq[file_id=="ChIP_MPP8_MORC2-KO_rep1_SRR5287078", file_id := "ChIP_MPP8_MORC2KO_rep1"]
chipseq[file_id=="ChIP_MPP8_MORC2-KO_rep2_SRR5287079", file_id := "ChIP_MPP8_MORC2KO_rep2"]
chipseq[file_id=="ChIP_MPP8_MPP8-KO_rep1_SRR5287080", file_id := "ChIP_MPP8_MPP8KO_rep1"]
chipseq[file_id=="ChIP_MPP8_MPP8-KO_rep2_SRR5287081", file_id := "ChIP_MPP8_MPP8KO_rep2"]
chipseq[file_id=="ChIP_MPP8_TASOR-KO_rep1_SRR5287082", file_id := "ChIP_MPP8_TASORKO_rep1"]
chipseq[file_id=="ChIP_MPP8_TASOR-KO_rep2_SRR5287083", file_id := "ChIP_MPP8_TASORKO_rep2"]
chipseq[file_id=="ChIP_MPP8_WT_rep1_SRR5287076", file_id := "ChIP_MPP8_WT_rep1"]
chipseq[file_id=="ChIP_MPP8_WT_rep2_SRR5287077", file_id := "ChIP_MPP8_WT_rep2"]
chipseq[file_id=="ChIP_TASOR_MORC2-KO_rep1_SRR5287086", file_id := "ChIP_TASOR_MORC2KO_rep1"]
chipseq[file_id=="ChIP_TASOR_MORC2-KO_rep2_SRR5287087", file_id := "ChIP_TASOR_MORC2KO_rep2"]
chipseq[file_id=="ChIP_TASOR_MPP8-KO_rep1_SRR5287088", file_id := "ChIP_TASOR_MPP8KO_rep1"]
chipseq[file_id=="ChIP_TASOR_MPP8-KO_rep2_SRR5287089", file_id := "ChIP_TASOR_MPP8KO_rep2"]
chipseq[file_id=="ChIP_TASOR_TASOR-KO_rep1_SRR5287090", file_id := "ChIP_TASOR_TASORKO_rep1"]
chipseq[file_id=="ChIP_TASOR_TASOR-KO_rep2_SRR5287091", file_id := "ChIP_TASOR_TASORKO_rep2"]
chipseq[file_id=="ChIP_TASOR_WT_rep1_SRR5287084", file_id := "ChIP_TASOR_WT_rep1"]
chipseq[file_id=="ChIP_TASOR_WT_rep2_SRR5287085", file_id := "ChIP_TASOR_WT_rep2"]
chipseq[file_id=="Input_H3K9me2or3_MPP8-KO_rep1_SRR5287122", file_id := "Input_H3K9me2or3_MPP8KO_rep1"]
chipseq[file_id=="Input_H3K9me2or3_MPP8-KO_rep2_SRR5287123", file_id := "Input_H3K9me2or3_MPP8KO_rep2"]
chipseq[file_id=="Input_H3K9me2or3_WT_rep1_SRR5287120", file_id := "Input_H3K9me2or3_WT_rep1"]
chipseq[file_id=="Input_H3K9me2or3_WT_rep2_SRR5287121", file_id := "Input_H3K9me2or3_WT_rep2"]
chipseq[file_id=="Input_H3K9me3_MORC2-KO_rep1_SRR5287108", file_id := "Input_H3K9me3_MORC2KO_rep1"]
chipseq[file_id=="Input_H3K9me3_MORC2-KO_rep2_SRR5287109", file_id := "Input_H3K9me3_MORC2KO_rep2"]
chipseq[file_id=="Input_H3K9me3_TASOR-KO_rep1_SRR5287110", file_id := "Input_H3K9me3_TASORKO_rep1"]
chipseq[file_id=="Input_H3K9me3_TASOR-KO_rep2_SRR5287111", file_id := "Input_H3K9me3_TASORKO_rep2"]
chipseq[file_id=="Input_H3K9me3_WT_rep1_SRR5287106", file_id := "Input_H3K9me3_WT_rep1"]
chipseq[file_id=="Input_H3K9me3_WT_rep2_SRR5287107", file_id := "Input_H3K9me3_WT_rep2"]
chipseq[file_id=="Input_MORC2_MORC2-KO_rep1_SRR5287094", file_id := "Input_MORC2MPP8TASOR_MORC2KO_rep1"]
chipseq[file_id=="Input_MORC2_MORC2-KO_rep2_SRR5287095", file_id := "Input_MORC2MPP8TASOR_MORC2KO_rep2"]
chipseq[file_id=="Input_MORC2_MPP8-KO_rep1_SRR5287096", file_id := "Input_MORC2MPP8TASOR_MPP8KO_rep1"]
chipseq[file_id=="Input_MORC2_MPP8-KO_rep2_SRR5287097", file_id := "Input_MORC2MPP8TASOR_MPP8KO_rep2"]
chipseq[file_id=="Input_MORC2_TASOR-KO_rep1_SRR5287098", file_id := "Input_MORC2MPP8TASOR_TASORKO_rep1"]
chipseq[file_id=="Input_MORC2_TASOR-KO_rep2_SRR5287099", file_id := "Input_MORC2MPP8TASOR_TASORKO_rep2"]
chipseq[file_id=="Input_MORC2_WT_rep1_SRR5287092", file_id := "Input_MORC2MPP8TASOR_WT_rep1"]
chipseq[file_id=="Input_MORC2_WT_rep2_SRR5287093", file_id := "Input_MORC2MPP8TASOR_WT_rep2"]
chipseq_dcast <- dcast(chipseq, gene_id ~ file_id, value.var = "count")

# Load chipseq library sizes
lib_sizes <- fread("grep 'in total' /projects/qbio/bifo/users/sergio/repository/20201122_hush/chipseq/flagstat/*.txt | cut -f11 -d'/' | cut -f1 -d' ' | cut -f1,2 -d':'", header = FALSE)
lib_sizes[, file_id := sapply(V1, function(x) unlist(strsplit(x, ":"))[1])]
lib_sizes[, lib_size := sapply(V1, function(x) unlist(strsplit(x, ":"))[2])]
lib_sizes[, V1 := NULL]

# define edgeR object
geneids <- chipseq_dcast$gene_id
chipseq_dcast[, gene_id := NULL]
y <- DGEList(counts = chipseq_dcast, genes = genes_length)
y$samples$lib.size <- as.numeric(lib_sizes$lib_size)

chipseq_norm <- rpkm(y)
chipseq_norm <- data.table(cbind(chipseq_norm, gene_id = geneids))


# Merge
genes_rnaseq <- merge(genes, rnaseq_dcast, by="gene_id")[,.(gene_name, gene_id, strand, length_WT_rep1, length_WT_rep2, gene_type, intronless, TPM_WT_rep1, TPM_WT_rep2, TPM_TASORKO_rep1, TPM_TASORKO_rep2, TPM_MORC2KO_rep1, TPM_MORC2KO_rep2, TPM_MPP8KO_rep1, TPM_MPP8KO_rep2)]
genes_rnaseq_length <- merge(genes_rnaseq, genes_length, by="gene_id")[,.(gene_name, gene_id, strand, length, length_WT_rep1, length_WT_rep2, gene_type, intronless, TPM_WT_rep1, TPM_WT_rep2, TPM_TASORKO_rep1, TPM_TASORKO_rep2, TPM_MORC2KO_rep1, TPM_MORC2KO_rep2, TPM_MPP8KO_rep1, TPM_MPP8KO_rep2)]
genes_rnaseq_length_chipseq <- merge(genes_rnaseq_length, chipseq_norm, by="gene_id")[,.(gene_name, gene_id, strand, length, length_WT_rep1, length_WT_rep2, gene_type, intronless, TPM_WT_rep1, TPM_WT_rep2, TPM_TASORKO_rep1, TPM_TASORKO_rep2, TPM_MORC2KO_rep1, TPM_MORC2KO_rep2, TPM_MPP8KO_rep1, TPM_MPP8KO_rep2, ChIP_H3K9me3_WT_rep1, ChIP_H3K9me3_WT_rep2, ChIP_H3K9me3_WT_rep3, ChIP_H3K9me3_WT_rep4, ChIP_H3K9me3_TASORKO_rep1, ChIP_H3K9me3_TASORKO_rep2, ChIP_H3K9me3_MORC2KO_rep1, ChIP_H3K9me3_MORC2KO_rep2, ChIP_H3K9me3_MPP8KO_rep1, ChIP_H3K9me3_MPP8KO_rep2, Input_H3K9me3_WT_rep1, Input_H3K9me3_WT_rep2, Input_H3K9me2or3_WT_rep1, Input_H3K9me2or3_WT_rep2, Input_H3K9me3_TASORKO_rep1, Input_H3K9me3_TASORKO_rep2, Input_H3K9me3_MORC2KO_rep1, Input_H3K9me3_MORC2KO_rep2, Input_H3K9me2or3_MPP8KO_rep1, Input_H3K9me2or3_MPP8KO_rep2, ChIP_TASOR_WT_rep1, ChIP_TASOR_WT_rep2, ChIP_TASOR_TASORKO_rep1, ChIP_TASOR_TASORKO_rep2, ChIP_TASOR_MORC2KO_rep1, ChIP_TASOR_MORC2KO_rep2, ChIP_TASOR_MPP8KO_rep1, ChIP_TASOR_MPP8KO_rep2, ChIP_MORC2_WT_rep1, ChIP_MORC2_WT_rep2, ChIP_MORC2_TASORKO_rep1, ChIP_MORC2_TASORKO_rep2, ChIP_MORC2_MORC2KO_rep1, ChIP_MORC2_MORC2KO_rep2, ChIP_MORC2_MPP8KO_rep1, ChIP_MORC2_MPP8KO_rep2, ChIP_MPP8_WT_rep1, ChIP_MPP8_WT_rep2, ChIP_MPP8_TASORKO_rep1, ChIP_MPP8_TASORKO_rep2, ChIP_MPP8_MORC2KO_rep1, ChIP_MPP8_MORC2KO_rep2, ChIP_MPP8_MPP8KO_rep1, ChIP_MPP8_MPP8KO_rep2, Input_MORC2MPP8TASOR_WT_rep1, Input_MORC2MPP8TASOR_WT_rep2, Input_MORC2MPP8TASOR_TASORKO_rep1, Input_MORC2MPP8TASOR_TASORKO_rep2, Input_MORC2MPP8TASOR_MORC2KO_rep1, Input_MORC2MPP8TASOR_MORC2KO_rep2, Input_MORC2MPP8TASOR_MPP8KO_rep1, Input_MORC2MPP8TASOR_MPP8KO_rep2)]

# Write
write.table(genes_rnaseq_length_chipseq, "20201208_grandtable_v1.txt", sep="\t", quote = FALSE, row.names = FALSE, col.names = TRUE)
```
