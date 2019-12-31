```
STAR --runThreadN 4 \
    --outSAMtype BAM SortedByCoordinate \
    --outSAMstrandField intronMotif \
    --outFilterIntronMotifs RemoveNoncanonicalUnannotated \
    --outReadsUnmapped None \
    --chimSegmentMin 15 \
    --chimJunctionOverhangMin 15 \
    --chimOutType WithinBAM \
    --alignMatesGapMax 200000 \
    --alignIntronMax 200000 \
    --genomeDir $STAR_GENOME_INDEX \
    --readFilesIn $SAMPLE_FASTQ_1 $SAMPLE_FASTQ_2 \
    --outFileNamePrefix $TUTORIAL_HOME/analysis/star/$SAMPLE_ID/
    --outSAMtype BAM SortedByCoordinate \

    --outFileNamePrefix $TUTORIAL_HOME/analysis/star/$SAMPLE_ID/
```
--chimSegmentMin 20 --chimOutTypeWithinBAM

In order to obtain alignments of chimeric reads potentially supporting fusions, we have added the --chimSegmentMin 20 option to obtain chimerica reads anchored by at least 20nt on either side of the fusion boundary, and --chimOutTypeWithinBAM to report such alignments in the sam/bam output.