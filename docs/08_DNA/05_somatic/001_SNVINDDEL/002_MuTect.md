### COSMIC in MuTect influence

Mutect2 
https://gatkforums.broadinstitute.org/gatk/discussion/2593/how-does-use-of-cosmic-in-mutect-influence-of-mutations-called-in-known-cancer-genes-vs-others


## tag
+ t_lod_fstar: suggesting insufficient evidence of its presence in the tumor sample
+ homologous_mapping_event: detects homologous sequences and filters out variants falling into sequences that have three or more events observed in the tumor. 
+ clustered_events: a filter for clustered artifacts. 
+ str_contraction: filters out variants from short tandem repeat regions.
+ alt_allele_in_normal: filters out variants if enough evidence is shown of its presence in the normal sample (NLOD threshold > 2.0).
+ multi_event_alt_allele_in_normal: filters out a variant when multiple events are detected at the same position in the matched-normal sample. 
+ germline_risk: filters out variants that show sufficient evidence of being germline based on dbSNP, COSMIC and the matched-normal sample (NLOD value)（If	a	variant	is	in	dbSNP	but	not	in	COSMIC,	reject		）. 
+ panel_of_normals: filters out variants present in at least two samples of the panel of normals.

