## Session 1 exercises

#### Exe1) How many sequences have been formatted and how does this affect the E-value of BLAST searches?

I ran the following:

    makeblastdb -dbtype prot -in uniprot_Atha.fasta

and got:

    Adding sequences from FASTA; added 15719 sequences in 0.373941 seconds.

According to [1], E-value "describes the number of hits one can expect to see by chance when searching a database of a particular size", therefore ...

#### References

1. https://blast.ncbi.nlm.nih.gov/doc/blast-help/FAQ.html, visited 15/12/2025


#### Exe2) Can you redirect the output to separate files called test.faa.blast and test.fna.blast?

Yes. The BLAST output can be redirected to files using shell redirection (`>`).
 
I ran the following commands:

    blastp -db uniprot_Atha.fasta -query test.faa -outfmt 6 > test.faa.blast

    blastx -db uniprot_Atha.fasta -query test.fna -outfmt 6 > test.fna.blast

This produces two separate files containing the tabular BLAST results for the protein and nucleotide queries, respectively.


#### Exe3) What is the default alignment format, can you show an example?

When no output format is specified, BLAST uses the default pairwise, human-readable format (outfmt 0).

To obtain this output, I ran BLASTP without specifying the `-outfmt` option:

    blastp -db uniprot_Atha.fasta -query test.faa

Running this command produces alignments such as the following:

    >sp|Q5PNU3|AMSH3_ARATH AMSH-like ubiquitin thioesterase 3
    Length=507

    Score = 30.0 bits (66), Expect = 4.2
    Identities = 39/136 (29%), Positives = 58/136 (43%), Gaps = 13/136 (10%)

    Query  613  NGGNNPISPLHTLLSNFSQDESSQLLHLTRTNSAMTSSGWPSKRPAVDSSFQHSGAGNNN  672
                NG +  +S L+++LS    D+     H    NS   S        A +  FQ  G    +
    Sbjct  240  NGDSQEVSTLNSVLS---LDDGRWQRHSEAVNSQFISD-------ATEDPFQFVGMKQPS  289

This output provides detailed information about each alignment, including the identity of the matched database sequence and its total length. 
It reports a bit score that reflects the quality of the alignment and an E-value, which represents the expected number of matches with similar quality that could occur by chance when searching a database of this size.

In addition, the output includes the number and percentage of identical residues, the proportion of positive (conservative) substitutions, and the number of gaps introduced in the alignment. The aligned regions of the query and subject sequences are shown in a readable format, allowing visual inspection of conserved regions, mismatches, and insertions or deletions.


#### Exe4) Are there differences in the results retrieved in both searches?

Yes, there are clear differences between the results obtained with BLASTP and BLASTX, and these differences are expected due to the nature of each search.

BLASTP compares a protein query against a protein database, resulting in direct protein–protein alignments. 
This approach typically retrieves highly specific hits corresponding to closely related proteins, with long alignments and very low E-values.

In contrast, BLASTX translates the nucleotide query sequence into the six possible reading frames before comparing it to the protein database. 
As a result, BLASTX may retrieve a larger number of hits, including partial or redundant alignments, and the alignment statistics (such as alignment length and E-values) may differ from those obtained with BLASTP.

Although both searches identify the same gene or closely related proteins, the number of hits, alignment lengths, and E-values are not identical. 
BLASTP provides more precise results when the correct protein sequence is known, while BLASTX is useful when the correct coding frame of a nucleotide sequence is unknown.    



#### Exe5) Can you explain the contents of the output file profile.out?

The PSI-BLAST output contains iterative alignment results and the construction of a Position-Specific Scoring Matrix (PSSM). The file reports pairwise alignments between the query and detected homologous sequences, including identity, positives, gaps, alignment blocks, and E-values.

For example, matches such as TOM1-like protein 9 (TOL9_ARATH) were detected with low sequence identity (18%) but higher positives (29%), indicating weak but potentially meaningful homology.

After each iteration, PSI-BLAST updates the PSSM using conserved residues from aligned sequences. This matrix reflects amino acid preferences at each query position and allows detection of more distant homologs compared to standard BLASTP.

The output also includes statistical parameters (Lambda, K, H), effective search space, substitution matrix (BLOSUM62), gap penalties, and database size, providing full context for alignment significance.

#### Exe6) Building a Hidden Markov Model from aligned sequences and scanning the database

To search for additional homologs using an HMM-based approach, I followed the standard HMMER workflow: multiple sequence alignment → HMM construction → indexing → database scan.

First, after obtaining a multiple sequence alignment (MSA) of the selected protein sequences (saved as `aligned.fasta`), I built a Hidden Markov Model profile with:

    hmmbuild model.hmm aligned.fasta

When I attempted to scan the UniProt Arabidopsis protein database with the model, I initially ran:

    hmmscan model.hmm uniprot_Atha.fasta > hmmer.out

This failed with:

    Error: Failed to open binary auxfiles for model.hmm: use hmmpress first

Therefore, I indexed the HMM profile as required by HMMER:

    hmmpress model.hmm

After indexing, the scan ran successfully:

    hmmscan model.hmm uniprot_Atha.fasta > hmmer.out

The resulting output file started with the expected HMMER header (HMMER 3.4) and reported, for the first queries in the database (e.g., `sp|Q9C5W6|14312_ARATH`, a 14-3-3-like protein), that:

    [No hits detected that satisfy reporting thresholds]

The internal pipeline summary also showed that no sequences passed any of the successive filters:

    Passed MSV filter: 0
    Passed bias filter: 0
    Passed Vit filter: 0
    Passed Fwd filter: 0

Overall, the HMM scan did not return significant matches under the default reporting thresholds, suggesting that the HMM profile built from the selected alignment was highly specific (and/or based on limited training diversity). Nonetheless, the exercise demonstrates the full HMMER procedure to build, prepare, and apply an HMM profile for sensitive protein family searches.

#### Exe7) Structural similarity-based functional annotation of AT1G30330.2 (ARF6)

**Steps**

1. I took the full-length protein sequence of AT1G30330.2 (ARF6) from `test.faa`.
2. I submitted it to **HHPred** and searched against the **PDB** database.
3. I checked the hitlist and the coverage plot to see which parts of ARF6 were supported by structural matches.
4. I used the boundaries of the PDB matches to define the main domains, and I checked AlphaFoldDB for the full-length model.

HHPred returned **55 hits**. The top ones had **100% probability** and very low E-values (e.g. **5.7e-74** for **4LDU_A**). The best hits were Auxin Response Factors from *Arabidopsis thaliana*:

- **4LDU_A**: Auxin response factor 5 (DNA-binding transcription factor)
- **4LDV_A**: Auxin response factor 1
- **8OJ2_A**: Auxin response factor
- **4CHK_H**: PB1 domain of Auxin response factor 5

From the coverage plot, the strongest structural support was for residues **~16–363**, which matches the conserved DNA-binding region. There were also hits for the C-terminal **PB1** domain (**~500–650**), which is involved in protein–protein interactions.

**Domains (PDB / Pfam)**

| Domain | Approx. residues | Evidence | Function |
|-------|------------------|----------|----------|
| DNA-binding region (includes B3 DBD) | ~16–363 | PDB (4LDU, 4LDV, 8OJ2) | DNA binding to auxin-responsive elements |
| Middle region (Q-rich / low complexity) | ~350–500 | Sequence composition | Transcriptional regulation |
| PB1 domain | ~500–650 | PDB (4CHK) / Pfam | Protein–protein interactions, dimerization |

**Similar sequence in AlphaFoldDB**

| AlphaFold ID | UniProt | Gene | Organism | Length | Avg pLDDT | Notes |
|--------------|---------|------|----------|--------|-----------|------|
| AF-Q9ZTX8-F1-v6 | Q9ZTX8 | ARF6 | Arabidopsis thaliana | 935 aa | 61.62 (low) | Full-length AlphaFold model; low average confidence, likely due to long low-complexity regions |

Overall, the PDB matches and the AlphaFold entry support the annotation of AT1G30330.2 as an Auxin Response Factor (ARF) transcription factor.

#### Exe8) GO terms and eggNOG orthology groups of AT1G30330.2 (ARF6)

AT1G30330.2 (ARF6) was queried in eggNOG using Viridiplantae as taxonomic scope and restricting functional interpretation to plant orthologs.

Three orthologous groups were identified, corresponding to different taxonomic levels:

- ENOG5028JYJ (root): Transcription – auxin-activated signaling pathway 
- ENOG502QSCZ (Eukaryota): Transcription – auxin-activated signaling pathway 
- ENOG5037P4Z (Viridiplantae): Auxin response factors (ARFs)

The Viridiplantae group (**ENOG5037P4Z**) was used for functional annotation, as it represents plant-specific ARF orthologs.

**eggNOG orthology group (plant-specific):**

- ENOG5037P4Z

Functional description of this group indicates that Auxin Response Factors (ARFs) are transcription factors that bind the DNA motif 5'-TGTCTC-3' present in auxin-responsive promoter elements (AuxREs).

**GO terms associated with ENOG5037P4Z**

**Biological Process (selected):**
- response to auxin stimulus 
- auxin mediated signaling pathway 
- hormone-mediated signaling pathway 
- regulation of transcription, DNA-dependent 
- gene expression 
- signaling / signal transduction 
- cellular response to chemical stimulus 
- regulation of biological process 
- developmental process (including flower and shoot development)

**Cellular Component:**
- nucleus 
- intracellular 
- intracellular organelle 
- membrane-bounded organelle 
- cell / cell part 

**Molecular Function:**
- DNA binding 
- nucleic acid binding 
- binding 
- organic cyclic compound binding 
- heterocyclic compound binding 

Overall, orthology-based annotation confirms ARF6 as a nuclear transcription factor involved in auxin-mediated signaling and regulation of gene expression.

#### Exe9) Summary of results (QuickGO / UniProt)

1) GO IDs → term + category

GO:0009414 – response to water deprivation (Biological Process)

GO:0035618 – root hair (Cellular Component)

GO:0016491 – oxidoreductase activity (Molecular Function)

2) Photosynthesis GO ID + category

GO:0015979 – photosynthesis (Biological Process)

3) Parents and children of photosynthesis (GO:0015979)

Immediate parents (ancestor chart): GO:0008152 (metabolic process) → GO:0009987 (cellular process) → GO:0008150 (biological process)

Immediate children (direct descendants):
GO:0019684 (photosynthesis, light reaction), GO:0019685 (photosynthesis, dark reaction),
GO:0010109 (regulation of photosynthesis), GO:1905156 (negative regulation of photosynthesis), GO:1905157 (positive regulation of photosynthesis),
GO:0009521 (photosystem), GO:0034357 (photosynthetic membrane).

4) GO annotation terms of proteins (UniProt)

A0A068LKP4 (A. thaliana): very short predicted protein (53 aa), unreviewed (TrEMBL), low annotation score → limited functional annotation.

A0A097PR28 (P. persica): general transcription factor IIH subunit 2, evidence at transcript level.

A0A059Q6N8 (Z. mays): Photosystem II reaction center protein M (psbM) → clearly related to photosynthesis.
Observation: annotation depth is very different between entries; the maize protein is directly photosynthesis-related, while the Arabidopsis one is poorly annotated (likely due to short length / predicted status).

5) Leaf development term

GO:0048366 – leaf development.

6) Proteins assigned to leaf development (GO:0048366)

Arabidopsis thaliana: 1,318 proteins

Prunus persica: 78 proteins

Zea mays: 203 proteins (Taxonomy ID: 4577)

7) BP annotations supported by experimental evidence (QuickGO → Statistics)

Arabidopsis thaliana: 28,612 BP annotations, 8,950 distinct gene products (experimental evidence filter applied).

Prunus persica: 6 BP annotations, 5 distinct gene products (experimental evidence filter applied).

#### Exe10) Predicting 3D structure of 14-3-3-like protein GF14 iota (Q9C5W6)

Steps performed

1. The protein sequence of 14-3-3-like protein GF14 iota (UniProt Q9C5W6) from Arabidopsis thaliana was extracted from the FASTA file.

2. The 3D structure was obtained directly from AlphaFoldDB, as a precomputed model is available for this protein.

3. The AlphaFold prediction was visualized and evaluated using the pLDDT confidence scores and the predicted aligned error (PAE) plot.


Structural model quality

Protein: 14-3-3-like protein GF14 iota
UniProt: Q9C5W6 
Organism: Arabidopsis thaliana 
Length: ~266 aa 
Method: AlphaFoldDB 
pTM: 0.89 
Model confidence: Very high (most residues pLDDT > 90)

