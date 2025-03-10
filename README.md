# Bulk RNA-seq Analysis

<details>
  <summary>### 1. FastQC</summary>

  **Installation & Execution**  
  Generic script for installing FastQC, setting up input and output paths, and running quality control on all **.fastq.gz** files in a specified directory.  

  1. Install FastQC (if not installed)  
  2. Set input and output paths  
     + Create output directory if it doesn't exist  
  3. Verify FASTQ files exist  
  4. Run FastQC on all .fastq.gz files  

  #### 1.1 Pre-processing (if needed)
  1. Install Required Programs and Libraries  
  2. Define Directory Paths for **.fastq.gz** files  
     + Create Necessary Directories  
  3. Decompress FASTQ Files  
  4. Adapter Trimming and *Quality Filtering* (**Cutadapt + Fastp**)  
  5. Deduplication with **FastUniq**  
  6. Second Quality Check with FastQC  

</details>

<details>
  <summary>### 2. Processing</summary>

  **Pipeline Steps**  

  1. Install Required Programs and Libraries (Run Once)  
     + *Verify Installations*  
     + Define Directory Paths  
     + Create Necessary Directories  
  2. **STAR** Genome Indexing (Run Once)  
     + STAR Alignment  
  3. BAM Quality Control using **SAMtools**  
     + Generate Alignment Summary (SAMtools Output)  
  4. Gene Expression Quantification using **FeatureCounts**  
  5. Add Gene Symbols to Gene Counts  

</details>

<details>
  <summary>### 3. GO Analysis (R) - Lines to Modify</summary>

  1. **Metadata file:** --- Line 68:  
  ```r
      file_path <- "/data/paula/Paula/R_studio/go_analysis/gene_counts.csv"
