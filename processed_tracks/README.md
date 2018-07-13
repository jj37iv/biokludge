# Processed tracks of ATAC-seq, DNase/MNase, short cap, long cap RNA-seq

Brackets refer to corresponding processing rules in [workflows/](/workflows/).

## ATAC-seq (`atac_ce10_spmr_se`)

1. Reads were trimmed using trim_galore (`tg_se`), and aligned using bwa in single-end mode (`bwa_se`).
2. Low-quality (q<10 -- `rm_unmapped`, `rm_q10`), mitochondrial (`rm_chrM`), and [modENCODE blacklisted](https://www.encodeproject.org/comparative/regulation/#Wormset5) (`rm_blacklist`) reads were discarded.
3. Normalised coverage was calculated using MACS2: --format BAM --bdg --SPMR --gsize ce --nolambda --nomodel --extsize 150 --shift -75 --keep-dup all (`macs2_se_extsize150_shiftm75_keepdup_all`).
    - This leads to high signal [where the cut sites are](https://github.com/taoliu/MACS/issues/145) by extending and shifting the tags, thereby [effectively smoothing the cut site coverage signal](https://groups.google.com/forum/#!topic/macs-announcement/4OCE59gkpKYs)
4. Files were renamed by library series ID (where available), and signal was smoothed by binning at 10bp resolution (`atac_ce10_spmr_se`).

Paired-end ATAC-seq data was also processed as follows (`atac_ce10_spmr_pe_lt200`).

1. Reads were trimmed using trim_galore (`tg_pe`), and aligned using bwa in single-end mode (`bwa_pe`).
2. Low-quality (q<10 -- `rm_unmapped_pe`, `rm_q10`), mitochondrial (`rm_chrM`), and [modENCODE blacklisted](https://www.encodeproject.org/comparative/regulation/#Wormset5) (`rm_blacklist`) reads were discarded.
3. Normalised coverage was calculated using MACS2 in paired-end mode, size-selecting for <200bp fragments (`macs2_pe_lt200`).
4. Files were renamed by library series ID (where available), and signal was smoothed by binning at 10bp resolution (`atac_ce10_spmr_pe_lt200`).

Rule `atac_processed` builds both single- and paired-end coverage tracks for all samples.

DNase-seq, and MNase-seq was processed as paired-end ATAC-seq data.

## Long cap RNA-seq (`lcap_ce10_linear_fwd`, `_rev`)

1. Reads were trimmed to 20bp (`trim20`), and aligned using bwa in paired-end mode (`bwa_pe`).
2. Low-quality (q<10), mitochondrial, rRNA, and modENCODE-blacklisted reads were discarded.
3. Strand-specific paired-end coverage with regions between read pairs "filled in" were generated.
4. Read coverage was normalised by DESeq2 sizeFactors of gene-level read counts.
5. Stage-specific normalised coverage was calculated by averaging across two biological replicates, and smoothed by binning at 10bp resolution.
6. Normalised coverage was log2-scaled using the formula log2(n + 1).

Rule `lcap_processed` builds coverage tracks for both samples.