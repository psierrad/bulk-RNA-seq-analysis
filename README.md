# bulk-RNA-seq-analysis

## 📂 Project Directory  
│  
├── **Pre-processing**  
│   ├── Quality Control: FastQC  
│   └── Data Adjustment & 2nd FastQC (if needed)  
├── **Processing**  
│   ├── Alignment: Hisat2 (M. musculus grcm38_snp_tran)  
│   ├── Sorting Aligned Sequences: Samtools  
│   └── Read Counting: FeatureCounts  
├── **Differential Expression**  
│   ├── GO Analysis  
│   ├── DESeq2 (Normalization & Analysis)  
│   └── Visualization in R  


<details>
  <summary> 1. FastQC</summary>
  
### **FastQC input-output**


| **Category**       | **Details**                        |
|--------------------|------------------------------------|
| **Input Folder**     | `/path/to/your/fastq/files` (Change this to your actual FASTQ directory) |
| **Input Files**      | `.fastq.gz` files |
| **Output Folder**    | `/path/to/output/directory` (Change this to your desired output location) |
| **Output Files**     | FastQC reports (`.html`, `.zip`) for each `.fastq.gz` file |
| **Requirements**     | - `fastqc` (automatically installed if missing)  <br> - Sufficient disk space for output files <br> - Appropriate permissions to read/write in specified directories |

<summary>##1.1 Pre-processing (if needed)</summary>

---

*Requirements*
+ **System Packages**: gzip, cutadapt, fastp, fastqc, fastuniq, STAR
+ **Input Files**: Paired-end .fastq.gz files with _R1_001.fastq.gz and _R2_001.fastq.gz format.
    
### **Pre-processing Input and Output**

| **Step**               | **Input Folder/Files**                        | **Output Folder/Files**                     | **Requirements**                     |
|-----------------------|------------------------------------------------|------------------------------------------------|---------------------------------------------|
| **Installation**        | N/A                                             | Installed programs: gzip, cutadapt, fastp, fastqc, fastuniq, STAR | sudo apt install, pip install             |
| **Step 0: Decompression**| `/data/paula/Paula/Folder_data/*.fastq.gz`     | Decompressed `.fastq` files in the same folder | `gzip` tool                               |
| **Step 1: Adapter Trimming** | `/data/paula/Paula/Folder_data/*_R1_001.fastq`, `_R2_001.fastq` | Trimmed FASTQ files: `*_trimmed_R1.fastq`, `*_trimmed_R2.fastq` | `cutadapt` tool                            |
| **Step 2: Quality Filtering** | Trimmed FASTQ files from Step 1             | Filtered FASTQ files: `*_filtered_R1.fastq`, `*_filtered_R2.fastq` | `fastp` tool                               |
| **Step 3: Deduplication**    | Filtered FASTQ files from Step 2            | Deduplicated FASTQ files in `/deduplicated` folder | `fastuniq` tool                           |
| **Step 4: FastQC Analysis**  | Deduplicated FASTQ files in `/deduplicated` | FASTQC reports in `/FastQC_results` folder    | `fastqc` tool                             |
| **Step 5: STAR Genome Indexing**| Genome FASTA file, GTF file               | Indexed genome data in `/GENOME_DIR` folder    | `STAR` tool                                |

</details>

<details>
  <summary> 2. Processing</summary>
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
</details>

<details>
  <summary>3. GO Analysis (R)</summary>
To adapt this script to different experiments, modify the lines:
1. **Metadata file:** --- Line 68:
file_path <- "/data/paula/Paula/R_studio/go_analysis/gene_counts.csv"

   - Contains columns: "Sample", "Condition", and "Type"  
   - Used to map experimental conditions and types  
   
2. **Gene counts file:** --- Line 68:
 ```  
file_path <- "/data/paula/Paula/R_studio/go_analysis/gene_counts.csv"
 ``` 
   - Contains expression data for different conditions and types  
   - Used to extract gene sets for GO analysis  

   output file---Line 98:
```
output_file <- paste0("/data/paula/Paula/R_studio/go_analysis/", condition1, "_", type1, "_vs_", condition2, "_", type2, "_go_enrichment_results.csv")
```
#### **Processes:**
1. **Read and process metadata**
   - Maps "Condition" (Novel/Familiar) and "Type" (IP/Input) to corresponding columns in gene count data  

2. **Extract gene sets**  
   - Based on "Condition" and "Type", retrieves the corresponding column from the gene counts file  

3. **Perform GO Enrichment Analysis**  
   - Uses clusterProfiler::compareCluster() to compare gene sets  
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
     
  column name mapping --- Lines 128-130
```
condition_code <- ifelse(condition == "Novel", "N", "F")  # NOVEL → N, FAMILIAR → F
type_code <- ifelse(type == "input", "INPT", "IP")  # INPUT → INPT, IP → IP
column_name <- paste(condition_code, type_code, sep = "_")
```
</details>

<details>
  <summary>4. DESeq2 analysis</summary>
To adapt this script to different experiments, modify:

1. Metadata file: Ensure it has "Sample", "Condition", and "Type" columns. --- Lines 11-12
```
raw_counts <- read.csv("counts_file.csv", row.names = 1)  # Ensure first column contains gene names
metadata <- read.csv("metadata_file.csv")
```
3. Conditions: Update "Familiar" and "Novel" if using new conditions.
4. Types: Ensure "Input" and "IP" match dataset terminology.
```
  input_metadata <- metadata[metadata$Type == "Input", ]  # Ensure 'Type' values match exactly
input_counts <- raw_counts[, input_metadata$Sample]
```

6. Thresholds: Adjust p-value cutoff (0.05) and log2 fold change (>1) as needed.
7. Normalization method: If needed, change from DESeq2-based normalization to another approach.

<summary>4.1 Visualization </summary>

Generic Variables for Future Experiments
+ between_condition_results → Results containing enrichment ratios and significance values
+ enrichment_ratios → Table with calculated enrichment ratios
+ norm_input_counts → Normalized counts for Input samples
+ ip_counts → Normalized counts for IP samples
+ Sig_bc_results → List of genes with significant differential enrichment

  ### Visualization Summary: Inputs and Outputs

| **Plot Type**        | **Input Data**                  | **Output Description**                                   |
|----------------------|---------------------------------|-----------------------------------------------------------|
| **Volcano Plot**       | `between_condition_results`     | Highlights significantly enriched genes using Log2(dER) vs -Log10(p-value) with a red threshold line at p = 0.05. |
| **Heatmap**            | `enrichment_ratios` + `Sig_bc_results$Gene` | Displays significant genes' enrichment ratios in a clustered heatmap format. |
| **Scatter Plot**       | `norm_input_counts` + `ip_counts` + `Sig_bc_results$Gene` | Visualizes normalized Input vs IP counts, with significant genes highlighted in red. |

### Key Notes for Future Modifications
- **Volcano Plot**: Adjust the p-value threshold (e.g., `-log10(0.01)` for stricter filtering).
- **Heatmap**: Change clustering options or color schemes to improve visibility for large gene sets.
- **Scatter Plot**: Modify color scales, axis limits, or density settings for clearer visualization.

