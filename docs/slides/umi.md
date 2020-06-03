---
marp: true
size: 4:3
theme: default
---

https://sfvideo.blob.core.windows.net/sitefinity/docs/default-source/user-guide-manual/analysis-guideline-variant-calling-data-with-umis.pdf?sfvrsn=d0aa3207_26

---

## Construct an unmmaped BAM tagged with UMIs
The optimal way to generate a mapped BAM with UMIs depends on the raw data format (e.g., FASTQ, BCL) and existing processing pipelines. Ultimately, you want a BAM file where the UMI sequence is stored in the RX tag for each read. Read 1 and read 2 of the same pair should have the same value stored in the RX tag, regardless which read contains the UMI sequence.

---

#### Multiple tools exist to help with this process:

1.	Picard’s **IlluminaBasecallsToSam** processes Illumina instrument run folders containing BCL fi	and produces demultiplexed, per-sample, unmapped BAM fi	The tool extracts UMI sequences from anywhere in the template or index reads into the RX tag.
2.	fgbio’s **AnnotateBamWithUmis** adds the UMIs from a FASTQ fi	to the RX tag of reads in a pre-existing BAM fi	by matching read names between the fi
3.	fgbio’s **ExtractUmisFromBam** processes unmapped BAM fi	where the UMIs are contained within read 1 or read 2 sequences, and extracts the UMI sequences into the RX tag.

---

sd
