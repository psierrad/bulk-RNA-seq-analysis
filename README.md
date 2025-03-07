# bulk-RNA-seq-analysis
### 1. FastQC Installation & Execution Script
Generic script for installing FastQC, setting up input and output paths, and running quality control on all **.fastq.gz** files in a specified directory.
1. Install FastQC (if not installed)
2. Set input and output paths
   + Create output directory if it doesn't exist
4. Verify FASTQ files exist
5. Run FastQC on all .fastq.gz files
#### 1.1 Pre-processing (if needed)

1. Install Required Programs and Libraries  
2. Define Directory Paths **.fastq.gz** files
   + Create Necessary Directories 
3. Decompress FASTQ Files
4. Adapter Trimming and *Quality Filtering* **(Cutadapt + Fastp)**  
5. Deduplication with **FastUniq**
   
7. Second Quality Check with FastQC  


### 2. Procesing
### **Pipeline Steps**  

1. Install Required Programs and Libraries (Run Once)
   + *Verify Installations*
   +  Define Directory Paths
   + Create Necessary Directories  
2. **STAR** Genome Indexing (Run Once)
   + STAR Alignment
3. BAM Quality Control using **SAMtools**
   + Generate Alignment Summary (SAMtools Output)  
5. Gene Expression Quantification using **FeatureCounts**  
6. Add Gene Symbols to Gene Counts

### 3. GO ANALYSIS-R

1. **Metadata file:** --- Line 68:
```
    file_path <- "/data/paula/Paula/R_studio/go_analysis/gene_counts.csv"
```
   - Contains columns: `"Sample"`, `"Condition"`, and `"Type"`  
   - Used to map experimental conditions and types  
   
2. **Gene counts file:** --- Line 68:
```
    file_path <- "/data/paula/Paula/R_studio/go_analysis/gene_counts.csv"
```  
   - Contains expression data for different conditions and types  
   - Used to extract gene sets for GO analysis  

   ---Line 98:
```
   output_file <- paste0("/data/paula/Paula/R_studio/go_analysis/", condition1, "_", type1, "_vs_", condition2, "_", type2, "_go_enrichment_results.csv")
```
#### **Processes:**
1. **Read and process metadata**
   - Maps `"Condition"` (Novel/Familiar) and `"Type"` (IP/Input) to corresponding columns in gene count data  

2. **Extract gene sets**  
   - Based on `"Condition"` and `"Type"`, retrieves the corresponding column from the gene counts file  

3. **Perform GO Enrichment Analysis**  
   - Uses `clusterProfiler::compareCluster()` to compare gene sets  
   - GO terms analyzed across **Biological Process (BP), Molecular Function (MF), and Cellular Component (CC)**  
   - Adjusts p-values using Benjamini-Hochberg (BH) correction  

4. **Filter top GO terms**  
   - Extracts the top 10 significant GO terms per category (BP, MF, CC)  

5. **Generate visualizations**  
   - Creates dot plots for enriched GO terms  
   - Adjusts aesthetics for better readability  

6. **Iterate over all condition/type combinations**  
   - Runs pairwise GO analysis for all condition/type combinations  
   - Saves results and generates plots
     
   --- Lines 128-130:
```
   condition_code <- ifelse(condition == "Novel", "N", "F")  # NOVEL → N, FAMILIAR → F
   type_code <- ifelse(type == "input", "INPT", "IP")  # INPUT → INPT, IP → IP
   column_name <- paste(condition_code, type_code, sep = "_")
```
   
