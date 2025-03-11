# bulk-RNA-seq-analysis

## 📂 Project Directory  
├── **Pre-processing**  
│   ├── FastQC: Quality Control  
│   └── Pre-processing: Data Adjustment & 2nd FastQC (if needed)  
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
1. Create folder: fastq 🗂️
2. `.fastq.gz` files in /path/to/your/fastq/files

| **Category**       | **Details**                        |
|--------------------|------------------------------------|
| **Input Folder**     | `/path/to/your/fastq/files` (Change this to your actual FASTQ directory) |
| **Input Files**      | `.fastq.gz` files |
| **Output Folder**    | `/path/to/output/directory` (Change this to your desired output location) |
| **Output Files**     | FastQC reports (`.html`, `.zip`) for each `.fastq.gz` file |
| **Requirements**     | - `fastqc` (automatically installed if missing)  <br> - Sufficient disk space for output files <br> - Appropriate permissions to read/write in specified directories |

<summary> 1.1 Pre-processing (if needed)</summary>

---

*Requirements*
+ **System Packages**: gzip, cutadapt, fastp, fastqc, fastuniq, STAR
+ **Input Files**: Paired-end .fastq.gz files with _R1_001.fastq.gz and _R2_001.fastq.gz format.
    
### **Pre-processing Input and Output**

| **Step**               | **Input Folder/Files**                        | **Output Folder/Files**                     | **Requirements**                     |
|-----------------------|------------------------------------------------|------------------------------------------------|---------------------------------------------|
| **Installation**        | N/A                                             | Installed programs: gzip, cutadapt, fastp, fastqc, fastuniq, STAR | sudo apt install, pip install             |
| **Step 0: Decompression**| `/path/to/your/Folder_data/*.fastq.gz`     | Decompressed `.fastq` files in the same folder | `gzip` tool                               |
| **Step 1: Adapter Trimming** | `/path/to/your/Folder_data/*_R1_001.fastq`, `_R2_001.fastq` | Trimmed FASTQ files: `*_trimmed_R1.fastq`, `*_trimmed_R2.fastq` | `cutadapt` tool                            |
| **Step 2: Quality Filtering** | Trimmed FASTQ files from Step 1             | Filtered FASTQ files: `*_filtered_R1.fastq`, `*_filtered_R2.fastq` | `fastp` tool                               |
| **Step 3: Deduplication**    | Filtered FASTQ files from Step 2            | Deduplicated FASTQ files in `/deduplicated` folder | `fastuniq` tool                           |
| **Step 4: FastQC Analysis**  | Deduplicated FASTQ files in `/deduplicated` | FASTQC reports in `/FastQC_results` folder    | `fastqc` tool                             |
| **Step 5: STAR Genome Indexing**| Genome FASTA file, GTF file               | Indexed genome data in `/GENOME_DIR` folder    | `STAR` tool                                |

</details>

<details>
  <summary> 2. Processing</summary>

  ### Key Notes
- **Input Folders:** Primary input paths are `/gene_count_file/path*` and `/path/to/your/Folder_data`.
- **Output Folders:** Results are stored under `/path/to/your/Folder_data/STAR_results/`.
- **Dependencies:** The code ensures all required tools are installed and verified before execution.


| **Step** | **Input Folder(s)** | **Input Files** | **Output Folder(s)** | **Output Files** | **Requirements** |
|------------|------------------------|--------------------|----------------------------|-----------------------|--------------------|
| **1. Installation** | N/A | N/A | N/A | Installed tools | `wget`, `curl`, `unzip`, `gzip`, `jq`, `samtools`, `subread`, `fastqc`, `cutadapt`, `fastp`, `fastuniq`, `STAR` |
| **2. Genome Indexing** | `/Reference/STAR_Index/path` | `Mus_musculus.GRCm39.dna.primary_assembly.fa`, `Mus_musculus.GRCm39.109.gtf` | `/Reference/STAR_Index/path` | Genome index files (e.g., `SA`, `.txt`, `.out`) | `STAR` |
| **3. STAR Alignment** | `/gene_count_file/path/deduplicated/pathFolder` | `*_unique_R1.fastq.gz`, `*_unique_R2.fastq.gz` | `/gene_count_file/path/STAR_results/path` | `.bam` files (e.g., `*_Aligned.sortedByCoord.out.bam`) | `STAR` |
| **4. BAM QC with Samtools** | `/gene_count_file/path/STAR_results/path` | `.bam` files from STAR alignment | Same as input folder | `.txt` QC files (e.g., `*_alignment_stats.txt`) | `samtools` |
| **5. Alignment Summary CSV** | `/gene_count_file/path/STAR_results/path` | `.txt` QC files from Samtools | Same as input folder | `alignment_summary.csv` | `samtools`, `awk` |
| **6. FeatureCounts - Gene Quantification** | `/gene_count_file/path/STAR_results/path` | `.bam` files from STAR alignment, `Mus_musculus.GRCm39.109.gtf` | Same as input folder | `gene_counts.txt`, `gene_counts.csv` | `featureCounts` (from `subread`) |
| **7. Gene Symbol Mapping** | `/gene_count_file/path/STAR_results/` | `gene_counts.csv` | Same as input folder | `gene_counts_with_symbols.csv` | `curl`, `jq` |


</details>

<details>
  <summary>3. GO Analysis (R)</summary>
  
1. Create folder: go_analysis 🗂️
2. gene_count.csv file in your/path/go_analysis

### 📋 GO Analysis Input/Output

| **Category**      | **Details** |
|-------------------|--------------|
| **Input Folder**    | `/your/folder/R_studio/go_analysis/` |
| **Input Files**     | `metadata.csv`<br>`gene_counts.csv` |
| **Output Folder**   | `/your/folder/R_studio/go_analysis/` |
| **Output Files**    | `<Condition1>_<Type1>_vs_<Condition2>_<Type2>_go_enrichment_results.csv` (GO enrichment result CSV for each condition comparison) |
| **Requirements**    | R libraries: `clusterProfiler`, `org.Mm.eg.db`, `readr`, `ggplot2`, `dplyr` |
| **Input Data Description** | **`metadata.csv`** - Contains `Sample`, `Condition`, and `Type` labels<br>**`gene_counts.csv`** - Contains gene expression data with sample-specific columns (`F_INPT`, `F_IP`, `N_INPT`, `N_IP`) |
| **Process**         | 1. Load metadata to map sample conditions and types<br>2. Read gene count data<br>3. Perform GO enrichment analysis for each condition & type combination<br>4. Visualize the top enriched GO terms with dot plots |
| **Output Data Description** | GO enrichment CSV files with detailed ontology information (BP, MF, CC), adjusted p-values, and gene counts |
| **Visualization**   | Dot plots for top GO terms in each category (BP, MF, CC) |

</details>

<details>
  <summary>4. DESeq2 analysis</summary>


## 📋 DESeq2 analysis & visualization

| **Step**                     | **Input Files**                  | **Requirements**                              | **Output Files**                                  |
|------------------------------|----------------------------------|------------------------------------------------|-----------------------------------------------------|
| **Step 1: Load Data**         | `counts_file.csv`<br>`metadata_file.csv` | Libraries: `tidyverse`, `DESeq2`, `pheatmap`  | N/A                                                 |
| **Step 2: Normalize Input**   | `counts_file.csv`<br>`metadata_file.csv` | DESeq2 (Normalization)                         | Normalized Input Counts (In-memory object)          |
| **Step 3: Enrichment Ratios** | Normalized Input Counts<br>IP Counts   | Metadata with `Sample`, `Condition`, `Type`     | `enrichment_ratios.csv`                             |
| **Step 4: Within-condition Analysis** | `enrichment_ratios.csv`        | Correct Sample Order in Metadata                | `within_condition_results_familiar.csv`<br>`within_condition_results_novel.csv` |
| **Step 5: Between-condition Analysis** | `enrichment_ratios.csv`        | Metadata conditions labeled as 'Familiar' and 'Novel' | `between_condition_results.csv`<br>`significant_between_condition_results.csv` |
| **Step 6: Visualizations**    | `between_condition_results.csv`<br>`enrichment_ratios.csv` | Libraries: `ggplot2`, `pheatmap`                | Volcano Plot<br>Heatmap<br>Scatter Plot             |
``

**Input Files**:
- `counts_file.csv`: Raw gene counts with genes as rows and samples as columns.  
- `metadata_file.csv`: Metadata containing `Sample`, `Condition`, and `Type`.  --- Lines 11-12

- Thresholds: Adjust p-value cutoff (0.05) and log2 fold change (>1) as needed.
- Normalization method: If needed, change from DESeq2-based normalization to another approach.

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

