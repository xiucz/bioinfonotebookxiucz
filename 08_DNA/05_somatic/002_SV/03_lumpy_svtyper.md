# 
`GT!=0/0` will get rid of low frequency variants.

### IMPRECISE tags
Lumpy gives two probabilistic intervals for every call. The point given in
2nd column of the VCF and the "END" value in the INFO field correspond to
the points in the intervals with the highest probability. The CIPOS and
CIEND give the down/up stream positions of the intervals and CIPOS95 and
CIEND95 give the 95% confidence interval.

IMPRECISE tags SVs with breakpoints that are not resolved to a single base pair.  Check out the lumpy paper for a description of how these breakpoints are defined.