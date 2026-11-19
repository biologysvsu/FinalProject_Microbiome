# Final-Project-Microbiome
# Task: Tutorial for Gene Expression Analysis Using Qiime2 and MultiQC, Gene Expression Quantification, and Differential Expression Analysis

## **Objective**
Students will:
1. Download RNA-seq data for **Not-Depressed** and **Depressed** human cell lines from the provided SRA project.
2. Use Qiime2 and MultiQC to analyze and visualize data. 
4. Visualize results such as heatmaps and volcano plots to highlight key differentially expressed genes.

---
## **Dataset Description**
The dataset comes from the **NCBI BioProject PRJNA615032**, which includes RNA-seq data for human cell lines:
- **Depressed**:
  - SRA Accessions: `SRR38158767`, `SRR38158768`, ` SRR38158770`, (3 replicates).
- **Non-Depressed**:
  - SRA Accessions: ` SRR38158776`, ` SRR38158783`,` SRR38158786` (3 replicates).
There are more data available in this project, students should be free to explore these data and modify SRA accessions accordingly.

## **Tasks and Deliverables**
### **Data Preparation**
1. Download all replicates for non-depressed and Depressed samples:
        Mock-infected: SRR11412215 to SRR11412218.
        COVID-19-infected: SRR11412227 to SRR11412230.
## Install MultiQC
MultiQC is a bioinformatics tool that combines quality-control results from multiple sequencing samples into a single report. We will install MultiQC and FastQC using Conda on Bridges-2.
1. Load Anaconda: Anaconda allows us to create separate environments containing the software needed for our analyses.
```
module load anaconda3
```
2. Create a Conda environment: The following command creates an environment named multiqc and installs both MultiQC and FastQC.
```
conda create -n multiqc -c conda-forge -c bioconda multiqc fastqc -y
```
3. Activate the environment: Once installation is compleete, activate the environment:
```
conda activate multiqc
```
You should now see (multiqc) at the beginning of your terminal prompt.

4. Verify the installation: Check that both programs are available:
```
multiqc --version
```
```
fastqc --version
```
If both commands display version information, the installation was successful.
5. Deactivate the MultiQC Conda environment before installing the next software.
```
conda deactivate
```
## Install QIIME2

1. Load anaconda. If you are using the same terminal window from the MultiQC installation, skip this step. If you closed your terminal and logged back into Bridges-2, run:
```
module load anaconda3
```
2. Check that you do not have any environments installed
  ```
  conda env list
  ```
  - If you do not have any Conda environments created, you will likely see:
    - `base                  *  /opt/packages/anaconda3-2024.10-1'
  → Continue to Step 3.
  - If you do have an existing environment, you will see:
    - `qiime2-amplicon-2024.2     /jet/home/your-username/.conda/envs/qiime2-amplicon-2024.2`
    - `base                  *  /opt/packages/anaconda3-2024.10-1`
   → Skip to Step 5. 
3. Create a QIIME 2 environment: The installation may take several minutes. Wait until your terminal prompt reappears.

```bash
conda env create -n qiime2 --file https://raw.githubusercontent.com/qiime2/distributions/refs/heads/dev/2026.7/qiime2/released/rachis-qiime2-linux-64-conda.yml
```
4. Activate QIIME 2.

```bash
conda activate qiime2
```

Your terminal prompt should now begin with `(qiime2)`.

5. Check that QIIME 2 was installed successfully.

```bash
qiime info
```

    **(OPTIONAL) Troubleshooting: If QIIME2 Does Not Work**
    If QIIME2 does not work, there may be a Conda environment conflict.
    
    a. Check for Incomplete Installations
    ```
    ls -l /jet/home/your-username/.conda/envs/
    ```
    If you see an incomplete QIIME2 installation, proceed to the next step.
    b. Manually delete those installations
    ```
    rm -rf /jet/home/yourusername/.conda/envs/qiime2-amplicon-2024.2
    ```
    c. Clean Up Conda Cache
    ```
    conda clean --all -y
    ```
    d. Check how much disk space is being used by Conda's downloaded packages. Ideally, this should be less than 10 GB.
    ```
    du -sh ~/.conda/pkgs
    ```
    e. Reinstall QIIME2
    Repeat Steps 3-5 to reinstall QIIME2.

6. Deactivate the Conda environment for now. We will activate it again once our input files are ready.
```
conda deactivate
```
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

To test if all the files downloaded correctly, ls into the file
```
ls
```
You should see 6 SRR numbers, all lit up blue 

Make a new file for the fastq files 
```
mkdir -p fastq_files
```

***COME BACK HERE. DATA FOR CONVERTING SRA INTO FASTQ AND GZ***

Go into your fastq_files folder 
```
cd fastq_files
```

ls to make sure all files are there. You should see 6 Red files 
```
ls
```

Once the Fastq.gz filed have been created, we need to unzip them.
```
gunzip *.fastq.gz
```

### Step 3. Quality Control on the Data

1. Create an output directory for the fastqc output.
```
mkdir fastqc_microbiome_out 
```

2. Run fastqc
```
fastqc -t 4 microbiome/*.fastq.gz --outdir fastqc_microbiome_out
```

3. Run Multiqc
```
multiqc fastqc_microbiome_out --filename multiqc_raw_data.html
```


### Step 4. Run Quimme to study the Microbiome

1. Create a Symlink for the metadata file from the share folder
```
ln -s /ocean/projects/bio260081p/shared/Group4FinalFa26/FinalProject_metadata_reduced.tsv .
```

2. Inspect the metadata file
```
head FinalProject_metadata_reduced.tsv
```


### Step 5. Formatting steps for quimme

1. Create the following directory
```
mkdir FinalProjectReads_qza
```

2. Since the latest version of QIIME2 is installed already, the environment can be activated with the following command after deactivating the multiqc.
```
conda deactivate
```

Now activate qiime2
```
conda activate qiime2
```

3. Feed qiime the raw reads
```
qiime tools import \
  --type SampleData[PairedEndSequencesWithQuality] \
  --input-path raw_data/ \
  --output-path FinalProjectReads_qza/FinalProjectReads.qza \
  --input-format CasavaOneEightSingleLanePerSampleDirFmt
```

4. Remove primer sequences from the reads
```
qiime cutadapt trim-paired \
  --i-demultiplexed-sequences FinalProjectReads_qza/FinalProjectReads.qza \
  --p-cores 4 \
  --p-front-f ACGCGHNRAACCTTACC \
  --p-front-r ACGGGCRGTGWGTRCAA \
  --p-discard-untrimmed \
  --p-no-indels \
  --o-trimmed-sequences FinalProjectReads_qza/FinalProjectReads_trimmed.qza \
  --o-stats FinalProjectReads_qza/cutadapt_stats.qza
```

5. Visualize the data
```
qiime demux summarize \
  --i-data reads_qza/reads_trimmed.qza \
  --o-visualization reads_qza/reads_trimmed_summary.qzv
```

Now we will do a few steps to actually see our data.
- Download the File from Bridges2 On Demand
- Open the file in https://view.qiime2.org/


### Step 6. Denoising the reads 
1. Join pair-end reds
```         
qiime vsearch merge-pairs \
  --i-demultiplexed-seqs reads_qza/reads_trimmed.qza \
  --output-dir reads_qza/reads_joined
```
2. Filter out low-quality reads
```         
qiime quality-filter q-score \
  --i-demux reads_qza/reads_joined/merged_sequences.qza \
  --o-filter-stats filt_stats.qza \
  --o-filtered-sequences reads_qza/reads_trimmed_joined_filt.qza
```
3. Summarize results
```         
qiime demux summarize \
  --i-data reads_qza/reads_trimmed_joined_filt.qza \
  --o-visualization reads_qza/reads_trimmed_joined_filt_summary.qzv
```
- Git add, commit and push `reads_trimmed_joined_filt.qzv` Download and open in https://view.qiime2.org/
- Answer question 13
4. Actual denoising with deblur

```         
qiime deblur denoise-16S \
  --i-demultiplexed-seqs reads_qza/reads_trimmed_joined_filt.qza \
  --p-trim-length 390 \
  --p-sample-stats \
  --p-jobs-to-start 4 \
  --p-min-reads 1 \
  --output-dir deblur_output
```
Note: This command may take about 10 minutes to run. Keep your computer awake and prevent it from going to sleep, as this may disconnect you from the HPC session and interrupt the process.

4. Summarize dublur output
```         
qiime feature-table summarize \
  --i-table deblur_output/table.qza \
  --o-summary deblur_output/deblur_table_summary.qzv \
  --o-feature-frequencies deblur_output/feature_frequencies.qza \
  --o-sample-frequencies deblur_output/sample_frequencies.qza

```
- Git add, commit and push `deblur_table_summary.qzv` Download and open in https://view.qiime2.org/

###  Run taxonomic classification
1. Create symlink to taxonomic data
```
ln -s /ocean/projects/bio260081p/shared/week-7-data/taxa .
```

### Filtering resultant table
1. Filter out rare ASVs
```         
qiime feature-table filter-features \
  --i-table deblur_output/table.qza \
  --p-min-frequency 10 \
  --p-min-samples 1 \
  --o-filtered-table deblur_output/deblur_table_filt.qza
```
- replace with desired frequency:
- --p-min-frequency 10: Keep ASVs detected at least 10 times across all 12 samples combined.
- --p-min-samples 1: Allow an ASV to remain even if it occurs in only one sample.

2. Filter out contaminant and unclassified ASVs
```         
qiime taxa filter-table \
  --i-table deblur_output/deblur_table_filt.qza \
  --i-taxonomy taxa/classification.qza \
  --p-include p__ \
  --p-exclude mitochondria,chloroplast \
  --o-filtered-table deblur_output/deblur_table_filt_contam.qza
```
3. Subset and summarize filtered table
```         
qiime feature-table summarize \
  --i-table deblur_output/deblur_table_filt_contam.qza \
  --o-summary deblur_output/deblur_table_filt_contam_summary.qzv \
  --o-feature-frequencies deblur_output/contam_feature_freq.qza \
  --o-sample-frequencies deblur_output/contam_sample_freq.qza
```
- Git add, commit and push `deblur_table_filt_contam_summary.qzv` Download and open in https://view.qiime2.org/
- Answer question 16 What is the minimum and maximum sequencing depth across all samples?

4. Copy final table to current directory
```         
cp deblur_output/deblur_table_filt_contam.qza deblur_output/deblur_table_final.qza
```
5. Produce final table summary
```         
qiime feature-table filter-seqs \
  --i-data deblur_output/representative_sequences.qza \
  --i-table deblur_output/deblur_table_final.qza  \
  --o-filtered-data deblur_output/rep_seqs_final.qza
```
```         
qiime feature-table summarize \
  --i-table deblur_output/deblur_table_final.qza \
  --o-summary deblur_output/deblur_table_final_summary.qzv \
  --o-feature-frequencies deblur_output/final_feature_freq.qza \
  --o-sample-frequencies deblur_output/final_sample_freq.qza
```

- Git add, commit and push `deblur_table_final_summary.qzv` Download and open in https://view.qiime2.org/

# VISUALIZATION
1. Create a phylogenetic tree
```         
qiime phylogeny align-to-tree-mafft-fasttree \
  --i-sequences deblur_output/rep_seqs_final.qza \
  --o-alignment deblur_output/aligned-rep-seqs.qza \
  --o-masked-alignment deblur_output/masked-aligned-rep-seqs.qza \
  --o-tree deblur_output/unrooted-tree.qza \
  --o-rooted-tree asvs-tree.qza
```

2. Generate rarefaction curves (Replace X with appropriate depth) 
```
qiime diversity alpha-rarefaction \
  --i-table deblur_output/deblur_table_final.qza \
  --p-max-depth X \
  --p-steps 20 \
  --i-phylogeny asvs-tree.qza \
  --o-visualization rarefaction_curves.qzv
```

3. Calculating diversity metrics. (Replace X with appropriate depth)
```         
qiime diversity core-metrics-phylogenetic \
  --i-table deblur_output/deblur_table_final.qza \
  --i-phylogeny asvs-tree.qza \
  --p-sampling-depth X  \
  --m-metadata-file FinalProject_metadata_reduced.tsv \
  --p-n-jobs-or-threads 4 \
  --output-dir diversity
```

4. Produce boxplot for shannon
```         
qiime diversity alpha-group-significance \
  --i-alpha-diversity diversity/shannon_vector.qza \
  --m-metadata-file FinalProject_metadata_reduced.tsv \
  --o-visualization diversity/shannon_compare_groups.qzv
```

5. Generate stacked bar chart of taxa relative abundances
```         
qiime taxa barplot \
  --i-table deblur_output/deblur_table_final.qza \
  --i-taxonomy taxa/classification.qza \
  --m-metadata-file FinalProject_metadata_reduced.tsv \
  --o-visualization taxa_barplot.qzv
```
