# Laboratory 3 Handout: Genome Annotation

**Course:** Techniki w genomice i transkryptomice
**Date:** 18.03.2026
**Instructor:** dr Mikołaj Dziurzyński
**Repository:** [https://github.com/mdziurzynski/TGT_2026](https://github.com/mdziurzynski/TGT_2026)

---

# 1. Access and Environment Setup

## Logging into the Server

```bash
ssh anthriscus_jupyter
```

```bash
conda activate genome_assembly_lab1
```

```bash
jupyter lab --no-browser --port=your_port
```

Open in browser:

```
localhost:your_port
```

---

# 2. Introduction to Genome Annotation

Genome annotation consists of:

* **Structural annotation** (identifying genes, CDS, tRNA, etc.)
* **Functional annotation** (assigning biological function)

---

# 3. Structural Annotation

## Tools Used

* **Augustus**
* **GeneMark-ES**

---

## Augustus Installation

```bash
conda create -n augustus_env -c bioconda -c conda-forge augustus gsl=2.5 boost=1.60
conda activate augustus_env
augustus
```

---

## GeneMark-ES Installation

1. Download license and software:
   [http://topaz.gatech.edu/GeneMark/license_download.cgi](http://topaz.gatech.edu/GeneMark/license_download.cgi)

2. Setup:

```bash
gzip -d gm_key_64.gz
mv gm_key_64 .gm_key
```

```bash
tar -xvzf gmes_linux_64_4.tar.gz
```

```bash
echo 'export PATH=$HOME/gmes_linux_64_4:$PATH' >> ~/.bashrc
source ~/.bashrc
```

3. Configure Perl path:

```bash
export PERL5LIB=$CONDA_PREFIX/lib/site_perl:$CONDA_PREFIX/lib/perl5/site_perl:$PERL5LIB
```

4. Create environment:

- Download genemark_env.yml from GitHub - https://github.com/mdziurzynski/TGT_2026

```bash
conda env create -f genemark_env.yml
conda activate genemark
```

Run GeneMark:

```bash
gmes_petap.pl
```

---

## Running Structural Annotation

```bash
cp /opt/teaching/tgt_2026/lab_3/biggest_contig.fasta <your_location>
```

### Augustus

```bash
conda activate augustus_env
augustus --species=rhizopus_oryzae --gff3=on --outfile=biggest_contig.augustus_output.gff3 biggest_contig.fasta
```

### GeneMark-ES

```bash
conda activate genemark
gmes_petap.pl --ES --sequence biggest_contig.fasta --cores 8
```

---

# 4. RNA-seq Evidence for Structural Annotation

## Install tools

```bash
conda activate genome_assembly_lab1
conda install -c conda-forge -c bioconda bwa gffread
```

## Build BAM alignments

```bash
bwa index biggest_contig.fasta
```

```bash
bwa mem -t 8 biggest_contig.fasta \
/opt/teaching/tgt_2026/lab_3/Umbe_pre_R1_001.fastq.gz \
/opt/teaching/tgt_2026/lab_3/Umbe_pre_R2_001.fastq.gz > biggest_contig.sam
```

```bash
samtools view -bS biggest_contig.sam | samtools sort -o biggest_contig.sorted.bam
```

```bash
samtools view -b -F 4 biggest_contig.sorted.bam > biggest_contig.sorted.mapped.bam
```

```bash
samtools index biggest_contig.sorted.mapped.bam
samtools faidx biggest_contig.fasta
```

---

## Visualization in IGV

Data location:

```
/opt/teaching/tgt_2026/lab_3/igv_data
```

Open:

[https://igv.org/app/](https://igv.org/app/)

Load:

* Genome (FASTA + FAI)
* Gene predictions
* BAM + index

---

## Analysis Questions

* How do Augustus and GeneMark predictions differ?
* Do RNA-seq reads support predicted genes?
* Are there genes without transcript support?

---

## Key Takeaways

* De novo predictions vary between tools
* RNA-seq evidence improves accuracy
* Pipelines (Braker3, PASA, MAKER) integrate multiple data sources

---

# 5. Functional Annotation

Functional annotation assigns biological meaning to predicted genes.

---

## Prepare Protein Sequences

```bash
gffread -g biggest_contig.fasta -y biggest_contig.protein.fa biggest_contig.augustus_output.gff3
```

Select up to 10 proteins with strong RNA-seq support.

---

## InterProScan

[https://www.ebi.ac.uk/interpro/search/sequence/](https://www.ebi.ac.uk/interpro/search/sequence/)

* Domain-based annotation
* Submit selected protein sequences

---

## EggNOG-mapper

[http://eggnog-mapper.embl.de/](http://eggnog-mapper.embl.de/)

* Orthology-based annotation

### Questions

* Do results overlap with InterPro?
* How do methods complement each other?

---

## KAAS (KEGG Annotation)

[https://www.genome.jp/kaas-bin/kaas_main](https://www.genome.jp/kaas-bin/kaas_main)

Use dataset:

```
/opt/teaching/tgt_2026/lab_3/Umbelopsis.proteins.fa
```

Select:

* Eukaryotic reference set

---

## Biological Questions

* Is the Krebs cycle functional?
* Can the organism assimilate ethanol?

---

# 6. Contact

For questions or collaboration:

**dr Mikołaj Dziurzyński**
[m.dziurzynski@uw.edu.pl](mailto:m.dziurzynski@uw.edu.pl)
