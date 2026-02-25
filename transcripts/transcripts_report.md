## Session 3 – Transcriptome analysis (WGCNA)

### Technical issues and troubleshooting

#### Package loading issue
When loading the **WGCNA** package, an error occurred due to a dependency conflict. Specifically, the installed version of the `rlang` package was older than the version required by WGCNA.

To resolve this issue, the R session was restarted and the `rlang` package was reinstalled to update it to a compatible version:


remove.packages("rlang")
install.packages("rlang")

### Working directory issue
During the analysis, the input files did not work correctly when executed from other locations. Finally, the working directory was set to the Desktop, after which the scripts ran correctly and the analysis proceeded without further problems.

#### WGCNA results

#### 3.1) How many samples & isoforms are included in TPM_counts_Drought_W_dataset.csv?
The TPM expression dataset from watered (W) conditions contains **9,940 isoforms measured across 39 samples**. Isoform identifiers are provided in a separate annotation column (`target_id`), while the remaining columns correspond to individual RNA-seq samples.

#### 3.2) How many samples are discarded after outlier analysis?
No samples were discarded after outlier analysis. Both the missing-value quality control (`goodSamplesGenes`) and the hierarchical clustering of samples showed that all **39 watered samples** clustered together below the selected cut height, indicating the absence of obvious outliers.

#### 3.3) What power value have you set as appropriate for calculating adjacency?
A soft-thresholding power of **β = 6** was selected, as it is the lowest value at which the scale-free topology fit index exceeds the chosen threshold (R² = 0.85), while preserving reasonable mean connectivity for network construction.

#### 3.4) How many co-expression modules are established before and how many after the module merging process?

Before module merging, 40 co-expression modules were identified. Modules with highly similar eigengenes were then merged using ‘mergeCloseModule’ with a cut height of **0.25** (approximately equivalent to an eigengene correlation of 0.75), resulting in **36 final modules**.

#### 3.5) What is the hub isoform (or hub gene) of the cyan module?
The hub isoform of the cyan module is Bradi1g00700.3, identified as the most highly connected gene within the module using an unsigned network with soft-thresholding power β = 6.

#### 3.6) According to the module-trait association heat map, which module has the highest positive correlation with the "blwgrd (below ground biomass)" trait?
According to the module–trait relationship heat map, the **violet module (MEviolet)** shows the highest positive correlation with **blwgrd**, with a correlation of **0.66** (p-value = **3e-04**).
