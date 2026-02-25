# Lab 1: Techniques in Genomics and Transcriptomics

**Instructor:** dr Mikołaj Dziurzyński

**Date:** 25.02.2026

**Institute of Evolutionary Biology, Faculty of Biology, University of Warsaw**

---

## 1. Server Infrastructure

The work will be performed on the **Anthriscus** server, hosted at CNBCh.

* **Specs:** 12 cores, 189 GB RAM
* **IP:** `212.87.6.113`

**Login:**

```bash
ssh your_username@212.87.6.113
```

**Password:** Change it immediately:

```bash
passwd your_username
```

**If you forget it, you cannot participate in class.**

---

## 2. Environment Setup (Conda)

### Step 1: Install Miniconda

```bash
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-py310_25.1.1-0-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm ~/miniconda3/miniconda.sh
```

### Step 2: Initialize

```bash
source ~/miniconda3/bin/activate
conda init --all
```

Close your terminal and reconnect. Your prompt should now start with `(base)`.

### Step 3: Create Lab Environment

```bash
conda create -n genome_assembly_lab1 -c bioconda -c conda-forge \
  flye spades quast megahit sra-tools fastp fastqc nanoplot jupyterlab
conda activate genome_assembly_lab1
```

---

## 3. JupyterLab Setup

### Step 1: Local SSH Config

On your local machine, edit `~/.ssh/config` and add:

```ssh
Host anthriscus_jupyter
    HostName 212.87.6.113
    User your_username
    LocalForward your_port 127.0.0.1:your_port

Host *
    ServerAliveInterval 40
```

### Step 2: Run Jupyter

Connect:

```bash
ssh anthriscus_jupyter
```

Activate environment:

```bash
conda activate genome_assembly_lab1
```

Launch JupyterLab:

```bash
jupyter lab --no-browser --port=your_port
```

Access via browser at `localhost:your_port`.

---

## 4. Data: *Umbelopsis* sp. WA50703

* **Organism:** Filamentous fungus (high lipid content for biodiesel)
* **Genome Size:** ~22 Mbp
* **Reads:** Illumina (short) and Nanopore (long)
    * **Short reads:** `SRR26874108_1.fastq.gz & SRR26874108_2.fastq.gz`
    * **Long reads:** `SRR26874107.fastq.gz`
* **Location:** `/opt/teaching/tgt_2026/lab_1`


**Symlink setup:**

```bash
ln -s /opt/teaching/tgt_2026/lab_1 data
```

---

## 5. Workflow: Quality Control

### Nanopore (NanoPlot)

```bash
NanoPlot --verbose --fastq nanopore_data --outdir nanoplot_output -t 4
```

### Illumina (fastp)

Trimming and adapter detection (ensure `.gz` output):

```bash
fastp \
  -i illumina_R1_input \
  -I illumina_R2_input \
  -o illumina_R1_output.gz \
  -O illumina_R2_output.gz \
  -q 20 \
  --detect_adapter_for_pe \
  --correction \
  -w 5
```

---

## 6. Workflow: _de novo_ Assembly

### Option A: Short-read (MEGAHIT)

```bash
megahit --min-contig 500 -t 8 -1 R1_after_fastp -2 R2_after_fastp -o megahit_illumina
```

### Option B: Short-read (SPAdes)

```bash
spades.py -t 10 -m 100 --only-assembler \
  -1 R1_after_fastp -2 R2_after_fastp \
  -k 21,33,55,77,99,121 \
  -o spades_illumina
```

### Option C: Hybrid (SPAdes)

```bash
spades.py --only-assembler \
  -1 R1_after_fastp -2 R2_after_fastp \
  --nanopore nanopore_data \
  -k 21,33,55,77,99,121 \
  -t 10 -m 100 \
  -o spades_hybrid_output
```

### Option D: Long-read (Flye)

```bash
flye --threads 10 --nano-raw nanopore_data -o flye_nanopore
```

---

## 7. Quality Assessment (QUAST)

Evaluate contig numbers, N50, and L50:

```bash
quast -o quast_output -t 8 \
  {path_to_assembly_fasta_1} {path_to_assembly_fasta_2}
```

### Download Results

```bash
mkdir quast_results
cd quast_results
scp -r anthriscus_jupyter:{path_to_results} .
```
