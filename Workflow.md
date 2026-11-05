# Final-Project-Microbiome#
# Task: Tutorial for Gene Expression Analysis Using STAR, Gene Expression Quantification, and Differential Expression Analysis

## **Objective**
Students will:
1. Download RNA-seq data for **Not-Depressed** and **Depressed** human cell lines from the provided SRA project.
2. Perform RNA-seq alignment using **STAR**.
3. Quantify gene expression using **FeatureCounts**.
4. Perform differential expression analysis between mock-infected and COVID-19-infected cells using **DESeq2** (or equivalent).
5. Visualize results such as heatmaps and volcano plots to highlight key differentially expressed genes.

---
## **Software and Manuals**
### **Required Software**
1. **STAR**: Spliced Transcript Alignment to a Reference.
   - [STAR GitHub Repository](https://github.com/alexdobin/STAR)
   - [STAR Manual](https://github.com/alexdobin/STAR/blob/master/doc/STARmanual.pdf)
2. **SRA Toolkit**: To download RNA-seq data from SRA.
   - [SRA Toolkit Documentation](https://github.com/ncbi/sra-tools)
3. **FastQC**: For quality control of RNA-seq reads.
   - [FastQC Website](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)
4. **Samtools**: For sequence data manipulation.
   - [Samtools Documentation](http://www.htslib.org/doc/)
5. **Subread/FeatureCounts**: For gene-level quantification.
   - [Subread User Manual](http://bioinf.wehi.edu.au/subread-package/)
6. **R and RStudio**: For differential expression analysis and visualization.
   - [R Project](https://www.r-project.org/)
   - [RStudio Website](https://posit.co/downloads/)

---
## **Dataset Description**
The dataset comes from the **NCBI BioProject PRJNA615032**, which includes RNA-seq data for human cell lines:
- **Depressed**:
  - SRA Accessions: `SRR38158767`, `SRR38158768`, ` SRR38158770`, (3 replicates).
- **Non-Depressed**:
  - SRA Accessions: ` SRR38158776`, ` SRR38158783`,` SRR38158786` (3 replicates).
There are more data available in this project, students should be free to explore these data and modify SRA accessions accordingly.

## **Tasks and Deliverables**
### **Part 1: Data Preparation**
1. Download all replicates for not Depressed and Depressed samples:
        Mock-infected: SRR11412215 to SRR11412218.
        COVID-19-infected: SRR11412227 to SRR11412230.
### Step 1. Create a directory for your transcriptome data
0. Request resources:
```
interact -t 4:00:00 --ntasks-per-node=48
```

1. Navigate to your personal folder in the Ocean project directory:

   `cd /ocean/projects/bio260081p/your-username`

   Replace `your-username` with your actual username.

2. Create a new folder named `finalproject` and move into it:

   `mkdir -p finalproject`

   `cd finalproject`

3. Confirm that you are in the correct directory by typing:

   `pwd`

   Your path should look like this:

   `/ocean/projects/bio260081p/your-username/finalproject`

### Step 2. Create a file containing the SRA accession numbers

1. Open the `vi` text editor to create a file named `srr_accessions.txt`:

   `vi srr_accessions.txt`

2. Press lowercase **`i`** to enter *Insert Mode*.

3. Paste the following accession numbers, **one per line**:

   ```
   SRR38158767
   SRR38158768
   SRR38158770
   SRR38158776
   SRR38158783
   SRR38158786
   ```

4. Save the file and exit `vi`:
   - Press **Esc** to leave Insert Mode.
   - Type **`:wq`**.
   - Press **Enter** to save and exit.

5. Verify that your file was saved correctly:

   `cat srr_accessions.txt`

   You should see all nine accession numbers displayed, one per line.
```
module load anaconda3
conda create -n sra-tools -c conda-forge -c bioconda sra-tools -y
```
```
conda activate sra-tools
```
```
vi download_sra.sh
```
- Type I
```
#!/bin/bash
mkdir -p sra-data

while read -r SRR; do
    prefetch "$SRR" -O "$PWD/$SRR"
    fasterq-dump "$SRR" -e 8 --split-files -O sra-data/
done < srr_accessions.txt
```
-script may give you an error if anaconda3 was loaded before script was run, exit the HPC and run it again.
-Run script using
```
bash download_sra.sh &
```


