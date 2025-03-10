# bulk-RNA-seq-analysis
​
├── Pre-Processing 
│   ├── Quality Control: FastQC 
│   ├── Adjust the data & 2nd FastQC (if needed)
├── Processing 
│   ├── Alignment​: Hisat2 (M. musculus grcm38_snp_tran)​
│   ├── Sort the aligned sequences (Samtools)​
    ├── Read counting​:  FeatureCounts​
├── Differential Expression​
│   ├── GO analysis​
│   ├── DESeq2 (Normalization & Analysis)​
└─── Visualization in R ​
📂 Project Directory  
│  
├── **Pre-processing**  
│   └── Quality Control: FastQC 
│   └── Adjust the data & 2nd FastQC (if needed)
├── **Processing**  
│   └── Tools: Hisat2 (M. musculus grcm38_snp_tran)  
│  
├── **Sequence Processing**  
│   ├── Sorting Aligned Sequences (Samtools)  
│   └── Visualization with IGV  
│  
├── **Read Counting**  
│   └── Tools: FeatureCounts  
│  
├── **Differential Expression Analysis**  
│   ├── GO Analysis  
│   ├── DESeq2 (Normalization & Analysis)  
│  
├── **IP/INPT Ratio Calculation**  
│   └── Visualization in R  
│  
└── **Final Output**  
    └── Visualizations and Functional Analysis  


<details>
  <summary> 1. FastQC</summary>

*Installation & Execution* 
Generic script for installing FastQC, setting up input and output paths, and running quality control on all **.fastq.gz** files in a specified directory.
1. Install FastQC (if not installed)
2. Set input and output paths
   + Create output directory if it doesn't exist
4. Verify FASTQ files exist
5. Run FastQC on all .fastq.gz files

<summary>1.1 Pre-processing (if needed)</summary>

1. Install Required Programs and Libraries  
2. Define Directory Paths **.fastq.gz** files
   + Create Necessary Directories 
3. Decompress FASTQ Files
4. Adapter Trimming and *Quality Filtering* **(Cutadapt + Fastp)**  
5. Deduplication with **FastUniq**
6. Second Quality Check with FastQC  
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

