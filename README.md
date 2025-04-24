# BRDC_case
Genomic assembly of a BRDC case study


## Workflow
### Short read trimming

<br>

### Long read basecalling

<br>

### Assembly & Polishing
Long reads were assembled with [flye v. 2.9.4](https://github.com/mikolmogorov/Flye/blob/flye/docs/USAGE.md).
``` ```
<br>

The subsequent assemblies were polished with [medaka](https://github.com/nanoporetech/medaka).

```
cd /project/mycoplasma/dnielsen/ONT_Feb2025/flye_hq/BC10_medaka
bwa index BC10_consensus.fasta
bwa mem -t 40 -a BC10_consensus.fasta /project/mycoplasma/dnielsen/2025-01-01/24-031063-24-549-10/*R1*.clean.fastq.gz > alignments_1.sam
bwa mem -t 40 -a BC10_consensus.fasta /project/mycoplasma/dnielsen/2025-01-01/24-031063-24-549-10/*R2*.clean.fastq.gz > alignments_2.sam
~/./Polypolish/target/release/polypolish filter --in1 alignments_1.sam --in2 alignments_2.sam --out1 filtered_1.sam --out2 filtered_2.sam
~/./Polypolish/target/release/polypolish polish  BC10_consensus.fasta filtered_1.sam filtered_2.sam > BC10_polished.fasta
rm *.amb *.ann *.bwt *.pac *.sa *.sam
```

<br>

The medaka assemblies were polished with the [BBDUK](https://github.com/BioInfoTools/BBMap/blob/master/sh/bbduk.sh) trimmed short reads.
``` ```

<br>

### Analysis
Assemblies were annotated with NCBI's [pgap](https://github.com/ncbi/pgap/tree/master/bacterial_annot). 

<br>

M. haemolytica genes were analyzed using a custom blast database using [ABRicate](https://github.com/boasvdp/extract_genes_ABRicate). 

See Mh.txt for the database.
