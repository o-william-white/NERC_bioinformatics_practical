# Processing Individual BUSCO Genes for Phylogenetics

In this practical you will take the individual BUSCO gene sequences extracted in the previous step and prepare them for phylogenetic analysis. Each gene must be aligned across all species, inspected for quality, cleaned to remove poorly represented taxa and unreliable alignment regions, before being used in downstream analyses such as tree building.

We will work through all of these steps using a single gene (`93270.fasta`) as an example, then apply the same workflow to all genes in the dataset.

## Table of Contents

1. [Before you start](#before-you-start)
2. [Align sequences with MAFFT](#align-sequences-with-mafft)
3. [Inspect alignment with ALAN](#inspect-alignment-with-alan)
4. [Remove samples with >50% missing data](#remove-samples-with-50-missing-data)
5. [Trim poorly aligned regions with Gblocks](#trim-poorly-aligned-regions-with-gblocks)
6. [Run the full pipeline for all genes](#run-the-full-pipeline-for-all-genes)
7. [Summary](#summary)

---

## Before you start

To complete this practical, you will need to have:
- Access to the Franklin HPC
- Completed the previous practical (genome assembly and BUSCO assessment)
- A basic understanding of Linux commands

## Set up directory

```bash
# change to home directory
cd ~

# make new directory
mkdir nerc_practical_alignments

# change directory 
cd nerc_practical_alignments
```

---

## Set up conda environments

In this practical we will use a single environment:
- `env_phylo` - tools for processing alignments

```bash
# copy the environments folder from the shared training directory to your current directory
cp -r /hpc/groups/nerc-training/envs .
```

Now we can install all the environments we need for the practical. This can take a few minutes to install. 

```bash
# create the phylo environment 
conda env create -y -f envs/env_phylo.yml

# activate this environment
conda activate env_phylo
```

## Copy the example input data

```
cp -r /hpc/groups/nerc-training/busco_genes_cds .
```

It should be a directory containing 10 fasta files, each containing unaligned sequences for a BUSCo gene. 

The files are names after BUSCO gene IDs and sequences are named with the species name and genome accession. Each sequence header should be the species or assembly name. 

We will work with the first file `busco_genes_cds/93270.fasta`.

```
# see the head of the file
head busco_genes_cds/93270.fasta

# what sequences are in the file 
grep -e "^>" busco_genes_cds/93270.fasta

# how many sequences are in the file
grep -e "^>" busco_genes_cds/93270.fasta | wc -l 
```
---

## Align sequences with MAFFT

Before comparing sequences across species, they need to be **aligned** — that is, gaps are inserted so that equivalent positions in different sequences are in the same column. This is an essential step before any phylogenetic analysis.

**MAFFT** is one of the most widely used multiple sequence alignment programs. It is fast, accurate, and handles both short and long sequences well. We use the `--auto` flag, which instructs MAFFT to automatically choose the most appropriate alignment strategy based on the size and complexity of the input.

Run in terminal:
```bash

# make output directory
mkdir -p mafft_output

# align the sequences in gene 93270 using MAFFT
mafft \
    --maxiterate 1000 \
    --globalpair \
    --adjustdirectionaccurately \
    busco_genes_cds/93270.fasta > mafft_output/93270.fasta
```

Check output:
```bash
# view the first few lines of the alignment - you should now see gap characters (-)
head mafft_output/93270.fasta
```

---

## Inspect alignment with ALEN

Before proceeding, it is important to visually inspect the alignment to check its overall quality. A good alignment should show a clear block structure, with most sequences sharing similar patterns across columns. Problems to look out for include:

- Sequences that are almost entirely gaps (a taxon with very little data)
- Large regions of sequence that do not align well with the rest (misassembled or miss-annotated regions)
- One or two sequences that appear shifted or frame-shifted relative to all others

**ALAN** is a lightweight command-line alignment viewer that works directly in the terminal, making it convenient for quickly inspecting alignments on the HPC without downloading files.

Run in terminal:
```bash
# view the alignment in the terminal
alen mafft_output/93270.fasta
```

ALAN keybindings:
| Key | Action |
|-----|--------|
| `←` `→` | Scroll left / right along the alignment |
| `↑` `↓` | Scroll up / down through sequences |
| `g` | Jump to a specific alignment column |
| `q` | Quit |

> **Discussion:** Look at the alignment for gene `93270`.
> - Are there any sequences that appear mostly as gaps?
> - Are there any regions where most sequences share conserved columns but one or two do not?
> - Does the alignment look broadly consistent, or are there large misaligned blocks?

---

## Remove samples with >50% missing data

Some sequences may be incomplete — for example if BUSCO only recovered a partial gene, or if the region was poorly assembled. Including a sequence that is mostly gaps can distort downstream analyses, in particular it can produce spurious long branches in phylogenetic trees.

As a standard quality filter, we remove any sequence where more than **50%** of alignment positions are gaps (`-`) or unknown bases (`N`). 

The Python script below reads a MAFFT-aligned FASTA, calculates the proportion of missing characters for each sequence, and writes only the sequences that pass the threshold to a new file.

First, cp the script `alignments_filter.py`:

```bash
cp /hpc/groups/nerc-training/alignments_filter.py .

# see help info
python /hpc/groups/nerc-training/alignments_filter.py --help
```

Run in terminal:
```bash
mkdir mafft_filtered_output

# remove sequences with more than 50% missing data
python alignments_filter.py \
   --input mafft_output/93270.fasta \
   --output mafft_filtered_output/93270.fasta \
   --threshold 0.5
```

The script will print:
- sequence names
- sequence lengths
- number of missing site 
- percentage of missing sites

Check output:
```bash
# count the number of sequences remaining in the filtered file
grep -c "^>" mafft_filtered_output/93270.fasta
```

> **Discussion:**
> - Were any sequences removed from `93270.fasta`?
> - Do the sequences removed match what you observed when you inspected the alignment with ALAN?
> - Is 50% an appropriate threshold? What might be the consequence of setting a stricter or more lenient cutoff?

---

## Trim poorly aligned regions with Gblocks

Even after removing poorly-represented taxa, many columns in an alignment may be unreliable. Regions that are only present in a small number of taxa, or that are surrounded by many gaps, are more likely to reflect alignment errors than true homology. Including such regions can mislead phylogenetic inference.

**Gblocks** selects conserved, well-aligned blocks from an alignment and discards everything else. It applies a set of criteria based on:

- The minimum number of sequences that must have a non-gap character in a column to keep it
- The maximum number of contiguous non-conserved positions allowed
- The minimum length of a retained block

We use relaxed settings (`-b4=5 -b5=h`) to retain a reasonable amount of informative data while still removing the worst regions. These flags mean:

| Flag | Meaning |
|------|---------|
| `-t=d` | Treat sequences as DNA (use `-t=p` for amino acid sequences) |
| `-b4=5` | Minimum length of a kept block is 5 positions |
| `-b5=h` | Allow gaps in half of all sequences in a position |

Run in terminal:
```bash
# trim poorly-aligned regions with Gblocks
Gblocks mafft_filtered_output/93270.fasta \
        -t=d \
        -b4=5 \
        -b5=h
```

Gblocks writes its output to the same directory as the input, appending `-gb` to the filename:

```bash
# check the trimmed output file
ls -lh mafft_filtered_output/
```

You should see a new file `93270.fasta-gb`. Move it to the gblocks output directory:

```bash
mkdir -p gblocks_output
cp mafft_filtered_output/93270.fasta-gb gblocks_output/93270.fasta

# see the head of the file, spaces have be added!
head gblocks_output/93270.fasta

# remove spaces
sed -i -e 's/ //g' gblocks_output/93270.fasta

# check alignment
alen gblocks_output/93270.fasta
```

> **Discussion:**
> - How much of the alignment was retained after Gblocks trimming?
> - Open both the pre- and post-Gblocks alignments in ALAN. Can you see which regions were removed?
> - What is the risk of being too aggressive with trimming? What about too lenient?

---

## Run the full pipeline for all genes

Once you are happy with the settings from testing on `93270.fasta`, you can run the full pipeline across all genes using a loop.

Open a text file and call it `process_alignments.bash`, and paste in the script below:
```bash
#!/bin/bash

# create output directories
mkdir -p mafft_output
mkdir -p mafft_filtered_output
mkdir -p gblocks_output
mkdir -p logs

# loop over all genes and run MAFFT, filter missing data, then Gblocks
for fasta in busco_genes_cds/*.fasta; do

    gene=$(basename "$fasta" .fasta)

    echo "Processing gene: $gene"

    # step 1: align with MAFFT
    mafft \
        --maxiterate 1000 \
        --globalpair \
        --adjustdirectionaccurately \
        $fasta > mafft_output/${gene}.fasta \
        2> logs/${gene}_mafft.log

    # step 2: remove sequences with >50% missing data
    python alignments_filter.py \
       --input mafft_output/${gene}.fasta \
       --output mafft_filtered_output/${gene}.fasta \
       --threshold 0.5 \
       > logs/${gene}_filter.log 2>&1

    # step 3: trim with Gblocks
    Gblocks "mafft_filtered_output/${gene}.fasta" \
            -t=d \
            -b4=5 \
            -b5=h \
            > logs/${gene}_gblocks.log 2>&1

    # cp Gblocks output to the final directory
    cp mafft_filtered_output/${gene}.fasta-gb gblocks_output/${gene}.fasta

    # remove spaces
    sed -e 's/ //g' -i gblocks_output/${gene}.fasta

done
```

Run the script:
```bash
bash process_alignments.bash
```

Check output:
```bash
# count how many genes were successfully processed
ls gblocks_output/*.fasta | wc -l
```

> **Note:** If a gene has too few taxa remaining after the missing data filter, Gblocks may fail or produce an empty alignment. These genes should be excluded from downstream analyses.

---

## Summary

You have now processed each BUSCO gene through a standard alignment quality-control pipeline. Here is a recap of what was produced at each stage:

| Step | Tool | Key output |
|------|------|------------|
| Align sequences | MAFFT | `mafft_output/<gene>.fasta` |
| Inspect alignment | ALAN | (visual inspection only) |
| Remove missing data | `alignments_filter.py` | `mafft_filtered_output/<gene>.fasta` |
| Trim alignment | Gblocks | `gblocks_output/<gene>.fasta` |

The files in `gblocks_output/` are now ready for concatenation or individual gene tree inference in the next step.
