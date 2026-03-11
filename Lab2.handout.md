# Lab 2: Techniques in Genomics and Transcriptomics

**Instructor:** dr Mikołaj Dziurzyński

**Date:** 11.03.2026

**Institute of Evolutionary Biology, Faculty of Biology, University of Warsaw**

---

## 1. Server Infrastructure

The work will be performed on the **Anthriscus** server, hosted at CNBCh.

* **Specs:** 12 cores, 189 GB RAM
* **IP:** `212.87.6.113`

**Login:**

```bash
ssh anthriscus_jupyter
```

## Logging into the Server

Open a new terminal and SSH into the server:

```bash
ssh anthriscus_jupyter
```

Activate the pre-configured conda environment:

```bash
conda activate genome_assembly_lab1
```

Launch JupyterLab (replace `your_port` with your assigned port number):

```bash
jupyter lab --no-browser --port=your_port
```

---

## Accessing the JupyterLab Interface

Open a browser (Chrome/Firefox) and navigate to:

```
localhost:your_port
```

Authentication options:

* Use the **token displayed in the terminal**
* Or set a **password**

To see running servers and tokens:

```bash
jupyter server list
```

---

# 2. Assembly Quality Analysis (QUAST)

**QUAST** is used to check numerical properties of genome assemblies, such as:

* Number of contigs
* Contig length statistics (mean, median)
* **N50**
* **L50**

---

## Execution Commands

Ensure the environment is active:

```bash
conda activate genome_assembly_lab1
```

Run QUAST on your assembly files:

```bash
quast -o quast_output -t 8 {path_to_assembly_fasta_1} {path_to_assembly_fasta_2}
```

Compare the numerical results between different assemblies.

**Note:** Previous assembly results are available at:

```
/opt/teaching/tgt_2026/lab_1/assemblies
```

---

# 3. Completeness Assessment (BUSCO)

**BUSCO** evaluates genome completeness by searching for **conserved universal single-copy orthologs**.

---

## Setup and Installation

Create a dedicated environment for BUSCO:

```bash
conda create -n busco_env -c conda-forge -c bioconda python=3.10 busco=5.8.2
```

---

## Running BUSCO

Run BUSCO on both your **"best"** and **"worst"** assemblies as determined by QUAST.

Refer to the official documentation for specific command flags:

[https://busco.ezlab.org](https://busco.ezlab.org)

---

## Analysis Questions

* Which **lineage dataset** is appropriate for your data?
* Are there significant differences in **completeness** or **duplication** between your assemblies?

---

# 4. Contamination Assessment (BlobTools)

**BlobTools** allows visualization and taxonomic partitioning of genome datasets to identify potential contamination.

---

## Dataset Locations

The required input data (BLAST hits and mappings) are located at:

```
/opt/teaching/tgt_2026/lab_2/blobtools/dataset_1
```

```
/opt/teaching/tgt_2026/lab_2/blobtools/dataset_2
```

---

## Execution Tasks

1. Run the `create` and `plot` commands (adjust commands according to the documentation).

   Documentation:

   [https://blobtools.readme.io](https://blobtools.readme.io)

2. Inspect the resulting **BlobPlot**:

   * GC content vs. Coverage

3. Identify whether any contigs belong to **non-target taxa**.

4. Determine how to handle the detected contamination.
