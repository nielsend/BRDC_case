# BRDC_case
Genomic assembly of a BRDC case study


## Workflow
### Short read trimming

### Long read basecalling


### Assembly & Polishing
Long reads were assembled with [flye v. 2.9.4](https://github.com/mikolmogorov/Flye/blob/flye/docs/USAGE.md).
``` ```

The subsequent assemblies were polished with [medaka](https://github.com/nanoporetech/medaka).
``` ```

The medaka assemblies were polished with the BBDUK trimmed short reads.
``` ```

### Analysis
Assemblies were annotated with NCBI's [pgap](https://github.com/ncbi/pgap/tree/master/bacterial_annot). 


M. haemolytica genes were analyzed using a custom blast database using [ABRicate](https://github.com/boasvdp/extract_genes_ABRicate). 
See Mh.txt for the database.
