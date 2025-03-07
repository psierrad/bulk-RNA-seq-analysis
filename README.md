# bulk-RNA-seq-analysis
### 1. FastQC Installation & Execution Script
Generic script for installing FastQC, setting up input and output paths, and running quality control on all **.fastq.gz** files in a specified directory.
    # 1. Install FastQC (if not installed)
    # 2. Set input and output paths
    # Create output directory if it doesn't exist
    # 3. Verify FASTQ files exist
    # 4. Run FastQC on all .fastq.gz files
#### 1.1 Pre-processing
### 2. Procesing
### 3. GO ANALYSIS-R

#### GO ANALYSIS-R

##### Metadata File:
The metadata file (`metadata.csv`) should be structured like this:

```csv
"Sample","Condition","Type"
"F_INPT","F","INPUT"
"F_IP","F","IP"
"N_INPT","N","INPUT"
"N_IP","N","IP"
```

Ensure that the column names in the metadata match the expected "Sample", "Condition", and "Type".
Metadata File Path:
--- Line 45: 
```
    metadata_file_path <- "/data/paula/Paula/R_studio/go_analysis/metadata.csv"
```
##### Main Data File Path:
--- Line 68:
```
    file_path <- "/data/paula/Paula/R_studio/go_analysis/gene_counts.csv"
```
##### Output directory** (line 98) should be set to your desired location.
---Line 98:
```
   output_file <- paste0("/data/paula/Paula/R_studio/go_analysis/", condition1, "_", type1, "_vs_", condition2, "_", type2, "_go_enrichment_results.csv")
```

##### Condition and Type mappings** (lines 128-130) are set dynamically but may need adjustments based on how you label the conditions and types in your metadata.
--- Lines 128-130:
```
   condition_code <- ifelse(condition == "Novel", "N", "F")  # NOVEL → N, FAMILIAR → F
   type_code <- ifelse(type == "input", "INPT", "IP")  # INPUT → INPT, IP → IP
   column_name <- paste(condition_code, type_code, sep = "_")
```

#### Analysis and visualization-R
#### Genes to LS clusters -PYTHON
