# BRDC_case
Bacterial genomic assembly of a BRDC case study


## Workflow
### Short read trimming
```
module load bbtools/39.01
bbduk.sh in1=X_R1_001.fastq.gz in2=X_R2_001.fastq.gz out1=X_R1_050725_2.fq out2=X_R2_050725_2.fq qtrim=rl trimq=10 maq=10 ftm=5 ref=bbduk_adapters.fa
```
<br>

### Long read basecalling
Basecalled using MinKNOW v. 24.11.8
<br>

### Assembly & Polishing
Long reads were assembled with [flye v. 2.9.4](https://github.com/mikolmogorov/Flye/blob/flye/docs/USAGE.md).
```
module load flye
flye --nano-hq barcode11/BC10.fq --out-dir flye_hq/BC10 -g 1.2m --threads 40 --asm-coverage 50
```

<br>

The subsequent assemblies were polished with [medaka](https://github.com/nanoporetech/medaka).
```
medaka_consensus -i barcode10/BC10.fq -d BC10_consensus.fasta -o BC10_medaka -t 16 --bacteria
```

<br>

The medaka assemblies were polished with the [BBDUK](https://github.com/BioInfoTools/BBMap/blob/master/sh/bbduk.sh) trimmed short reads using [polypolish v. 0.6.0](https://github.com/rrwick/Polypolish/wiki/How-to-run-Polypolish).
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

### Analysis
Assemblies were annotated with NCBI's [pgap](https://github.com/ncbi/pgap/tree/master/bacterial_annot). 
```
module load pgap
pgap.py -g BC10_polished.fasta -s 'Mycoplasmopsis bovis' -n -D apptainer --no-self-update -o mbovOut -m 30g
```
<br>

### Comparison to NCBI genomes
1. NCBI assemblies reannotated with [prokka](https://github.com/tseemann/prokka)
```
module load perl
module load barrnap
module load blast+
module load prokka

#for M. bovis
for i in *.fna; do prokka $i --outdir ${i/_genomic.fna/} --prefix ${i/_genomic.fna/} --gcode 4 --cpus 40; done

#for M. haemolytica
for i in *.fna; do prokka $i --outdir ${i/_genomic.fna/} --prefix ${i/_genomic.fna/} --cpus 40; done
```

2. [Roary](https://sanger-pathogens.github.io/Roary/) run on .gff prokka output
   ```
   module load roary
   
   #for M. bovis
   roary -t 4 -p 40 -e *.gff
   
   #for M. haemolytica
   roary -p 40 -e *.gff
   ```

3. [raxml](https://github.com/stamatak/standard-RAxML) run on Roary output
   ```
   module load raxml
   raxmlHPC-PTHREADS-AVX -m GTRGAMMA -f a -n roary_Raxml_core -s ./core_gene_alignment.aln -T 40 -x 7 -N autoMRE -p 7
   ```


M. haemolytica genes were analyzed using a custom blast database using [ABRicate](https://github.com/boasvdp/extract_genes_ABRicate). 
```
module load blast+
module load perl
~/./abricate/bin/abricate --db Mh /project/mycoplasma/dnielsen/Mh_03.20.25_NoAtyp_MAGs_MIPs/ncbi_dataset/data/GCAassemblies/fna/*.fna > Mhoutput.tsv
```
See Mh.txt for the database.
