C. Allele Fraction Clustering Model
Several of FilterMutectCalls’s probabilistic filters rely on the following graphical model for the tumor’s spectrum of allele fractions of real variants. At the top of the model is a binary indicator for whether a variant is neither a technical artifact or a non-somatic mutation.
P (consider) = (1 − max artifact error prob)(1 − max non-somatic prob). (13)
Note that possible sequencing errors, with error probability captured by the independent reads assumption of the Mutect2 log odds, are considered. Next, a candidate SNV has prior probability πreal = πSNV/3 to be real and prior probability 1 − πSNV/3 to be a sequencing error, while a candidate indel of length L (negative for deletions, positive for insertions) has prior probability πreal = πL to be real. Real variants are partitioned according to a mixture model with weights πH for a high-allele fraction cluster, πB for a “background cluster”, and πD = 1 − πH − πB to be further clustered into according to a Dirichlet process with concentration α. Each non-error cluster has a characteristic distribution of allele fractions: Beta(αH,βH) and Beta(αB,βB) for the high-allele fraction and background clusters, and Dirac δ functions for the Dirichlet clusters. The idea of this partitioning is that there will usually be sharply- defined allele fractions corresponding to the hets of different subclones, which are captured by the Dirichlet clusters, but that homozygosity and copy-number variation produce deviations from this clustering.
Provided that we stochastically assign the ith variant as non-artifactual, the prior probabilities for each possibility are described by a modified Chinese Restaurant Process scheme:
P (Sequencing error) =1 − πreal P(High AF) =πrealπH P(Background) =πrealπB
P(Dirichlet )=π j
cluster, N−i is the number of variants besides the ith assigned to cluster j, and N−i is the total number of variants jD
besides the ith assigned to Dirichlet clusters.
Mutect2 emits the likelihood ratio between sequencing error and a real variant assuming a flat prior. However,
we can approximately compute the likelihood ratio that a non-flat Beta prior would have produced. We can do this because in Equation 9 a change in the prior allele fraction is unlikely to affect z ̄ very much - usually the allele that a read supports is clear regardless of prior. Therefore, only the term g(α) − g(β) changes. Since β = α + n, where n is the vector of observed read counts per allele we can convert the log-likelihood obtained with flat prior α0 to one reflecting an allele fraction prior α by adding
∆log odds(α)=∆lnP(R|A)=[g(α)−g(α+n)]−[g(α0)−g(α0 +n)]. (19)
Since only ratios of likelihoods are meaningful, we may WLOG set the likelihood of reads given sequencing error to be 1 and then set the likelihood fo each allele fraction cluster to be exp [Mutect2 log odds + ∆log odds(α)]. The likelihood of a new cluster is the average over all possible new allele fractions of the likelihood, which is, by definition, simply the original Mutect2 likelihood that integrated over a flat prior. Thus we have likelihoods for every cluster. Combined with the priors, this gives us the conditional posterior for Gibbs sampling the cluster assignment of a candidate variant.
We can estimate the parameters of the model by maximum likelihood. For example, we estimate πL as the total number of length-L indels in the non-error clusters divided by the total number of callable sites3, πB as the total number of variants assigned to the background cluster divided by the total number of non-error assignments. The allele fraction of a Dirichlet cluster is estimated as its total alt read count divided by its total read count. Finally, the beta distributions of the high-allele fraction and background clusters are fit by brute force likelihood optimization.
Once the model converges, the combination of cluster weights and allele-fraction-adjusted likelihoods lets us compute the posterior probability that a candidate variant is a sequencing error, which determines the weak evidence filter. Since the clusters also define distributions on alt allele counts – the high-AF and background clusters define beta-binomials and the Dirichlet clusters define binomial – we also have the mixture model distribution P (alt count|total count, somatic), which the contamination and germline filters use.

---

## III. GERMLINE FILTER

Suppose we have detected an allele such that its (somatic) likelihood in the tumor is $l_{t}$ and its(diploid) likelihood in the normal is ${l_{n}}^{2}$. By convention, both of these are relative to a likelihood of 1 for the allele not to be found. If we have no matched normal, ln = 1. Suppose we also have the population allele frequency f of this allele. Then the prior probabilities for the normal to be heterozygous and homozygous alt for the allele are 2f(1−f) and f2 and the prior probability for the normal genotype not to contain the allele is (1−f)2. Finally, suppose that the prior for this allele to arise as a somatic variant is π.
假设我们已经检测到某个等位基因在肿瘤样本中的突变似然值是$l_{t}$，它在正常样本中的似然值是${l_{n}}^{2}$。按照惯例，如果该等位基因没有被发现，则它们都有1的似然值。如果我们没有配对的正常样本，$l_{t} = 1$。假设我们也有了该等位基因的人群突变频率，那么正常样本的杂合和纯合等位基因的先验概率分别为$2f(1−f)$ 和$f^{2}$， 正常样本基因型不包含该等位基因先验概率是$(1−f)^{2}$。最后假设该等位基因是肿瘤变异的先验概率是$π$。
We can determine the posterior probability that the variant exists in the normal genotype by calculating the unnormalized probabilities of four possibilities:
1. The variant exists in the tumor and the normal as a germline het. This has unnormalized probability 2f (1 − f)lnlt(1 − π).
2. The variant exists in the tumor and the normal as a germline hom alt. This has unnormalized probability f2lnlt(1 − π).
3. The variant exists in the tumor but not the normal. This has unnormalized probability (1 − f)2ltπ.

We exclude possibilities in which the variant does not exist in the tumor sample because we really want the conditional probability that the variant is germline given that it would otherwise be called.
Normalizing, we obtain the following posterior probability that an allele is a germline variant: (1)+(2)  2f(1−f)+f2 lnlt(1−π)
P(germline)= (1)+(2)+(3) = (2f(1−f)+f2)lnlt(1−π)+lt(1−f)2π. (26)
The above equation, in which the factors of lt could cancel if we wished, is not quite right. The tumor likelihood
lt is the probability of the tumor data given that the allele exists in the tumor as a somatic variant. If the allele is
in the tumor as a germline het we must modify lt to account for the fact that the allele fraction is determined by the
ploidy – it must be either fg or 1 − fgwith equal probability, where fg is the minor allele fraction of germline hets.
It would be awkward to recalculate the tumor likelihood with the allele frequency constrained to these two values3,
but we can estimate a correction factor as follows: assuming that the posterior on the allele fraction in the somatic
likelihoods model is fairly tight, the likelihood of a alt reads out of n total reads is  n (1 − ft)n−afa, where ft is the a
tumor alt allele fraction. That is, our sophisticated model that marginalizes over ft reduces to something more naive. If the variant is a germline event, the likelihood becomes 1 n  (1−f )n−afa +fn−a(1−f )a . Thus, in case (1) we
1 (1 − fg)n−afa + fn−a(1 − fg)a
χ=gg. (27)
5
  2agggg
 have lt → χlt, where
For germline hom alts, both the tumor and normal allele fractions will be similarly large, so to decent approximation we don’t have to modify lt. Of course, this only applies if the allele fraction is large. Rather than try to model the count of ref reads within a germline hom alt site, we simply set a threshold of allele fraction 0.9, so that in case (2) lt → I[ft > 0.9]lt. and the corrected germline probability is
(1)+(2)  2f(1−f)χ+I[ft >0.9]f2 ln(1−π)
P(germline)= (1)+(2)+(3) = (2f(1−f)χ+I[ft >0.9]f2)ln(1−π)+(1−f)2π. (28)
To filter, we set a threshold on this posterior probability.
So far we have assumed that the population allele frequency f is known, which is the case if it is found in our germline
resource, such as gnomAD. If f is not known we must make a reasonable guess as follows. Suppose the prior distribution on f is Beta(α, β). The mean α/(α+β) of this prior is the average human heterozygosity θ ≈ 10−3, so we have β ≈ α/θ. We need one more constraint to determine α and β, and since we are concerned with imputing f when f is small we use a condition based on rare variants. Specifically, the number of variant alleles n at some site in a germline resource with N/2 samples, hence N chromosomes, is given by f ∼ Beta(α,β),n ∼ Binom(N,f). That is, n ∼ BetaBinom(α,β,N). The probability of a site being non-variant in every sample is then P(n = 0) = BetaBinom(0|α,β,N), which we equate to the empirical proportion of non-variant sites in our resource, about 7/8 for exonic sites in gnomAD. Solving, we obtain approximately α = 0.01, β = 10 for gnomAD. Now, given that some allele found by Mutect is not in the resource, the posterior on f is Beta(α, β + N ), the mean of which is, since β << N , about α/N . By default, Mutect uses this value.