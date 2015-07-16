Early draft paper describing [bcbio-nextgen](https://bcbio-nextgen.readthedocs.org/en/latest/index.html), a python toolkit providing best-practice pipelines for fully automated high throughput sequencing analysis.

## ToDo

1. Go over blog posts (below) and identify relevant figures to include [DONE]
1. Pull architecture information out of <http://bcb.io/2014/03/06/improving-reproducibility-and-installation-of-genomic-analysis-pipelines-with-docker/> and <http://bcb.io/2013/05/22/scaling-variant-detection-pipelines-for-whole-genome-sequencing-analysis/>. [DONE]
1. Organize all test data into a single spot (along with documentation, version information, etc.) [DOING]
1. Clarify differences between LCR (used in some of the blog posts) and GiaB callable regions filter
1. Define/Agree on the default metrics to set as we make our way through the benchmarks -- e.g., to be able to show the impact of the LCR and high depth regions those filters need to be disabled by default (or we can show best practices throughout and show their impact by disabling them).
1. Re-run benchmarks. This is basically unifying the various blog posts from Brad's site with the same bcbio version, input data and validation data as part of the paper.  
1.  Visualize differences sticking to concordant / missing / extra / shared|mixed variant description. Ideally provide IPython/RMarkdown scripts for the downstream comparison. I don't think we need timing information for the validation steps, just for the IT benchmark. Code to get started at <https://github.com/chapmanb/bcbb/tree/master/validation>.
1. Move original text from blog posts to main document.


## Benchmarks to Run

### Germline

**Ensemble calling:** Don’t think we want to re-introduce the SVM ensemble calling approach. Would be good to limit to `bwa-mem` with `FreeBayes`, `GATK HaplotypeCaller` and maybe `samtools` as a third option, show benefits of Ensemble call as currently implemented (found in 2 out of 3 callers that are independent). Just as a proof of principle. See <http://bcb.io/2013/02/06/an-automated-ensemble-method-for-combining-and-evaluating-genomic-variants-from-multiple-callers/>. Worth keeping an eye on the time spent calling variants with each tool so we can discuss time vs result differences.

**On ‘best practices’:** Lots of talking about what best practice means, but it’s actually not really defined (other than ‘what everyone else does’). We could add some contentious topics such as the need for re-alignment/re-calibration (see <http://bcb.io/2013/10/21/updated-comparison-of-variant-detection-methods-ensemble-freebayes-and-minimal-bam-preparation-pipelines/>), better filtering (see <http://bcb.io/2014/05/12/wgs-trio-variant-evaluation/>) and the impact of joint calling (see <http://bcb.io/2014/10/07/joint-calling/>), e.g., on a single NA12878 run with and without a reference 1000G panel / subset. Highlight differences (license free pipeline). 

The joint calling story is optional and I’d limit it to the “NA12878 with/without a subset of 1000G with the same ethnicity” example; the blog post is tricky to wrap your head around unless we dedicate a significant portion of the text to it. 

### Somatic

This one’s relatively straightforward as there’s only one comparison so far (see <http://bcb.io/2015/03/05/cancerval/>). Introduce the [ICGC-TCGA DREAM data](http://bcb.io/2015/03/05/cancerval/) and show two things:

**Cancer variant caller comparison:** Just as important to compare differences in callers (if not more so, far from a set of ‘standard’ callers in this domain). Repeat Brad’s analysis just to make sure we have the config files and data all in one place for distribution. 

**Tweaking tools:** Highlight how the benchmark sets are not only important to test new tools but to optimize existing ones. FreeBayes with default and Speedseq-optimized parameters. 

Don’t think I’d include the cancer-only story as it would need more work (comparison of filters, manual curation) though I really like the detection of a false variant call. Up for discussion. 

### SV

Benchmark sets not limited to SNPs/InDels, also need to include larger events. See <http://bcb.io/2014/08/12/validated-whole-genome-structural-variation-detection-using-multiple-callers/>.

**Ensemble SV approaches:** A repeat of the previous concept. Old run was with Lumpy, Delly, cn.mops. We’d probably switch to Lumpy, cnvkit, Wham for this (or keep Delly though it takes a while to run). Stratifiy by size of event. We could alterntively focus on the [cancer SVs](http://bcb.io/2015/03/05/cancerval/) to tie in with the previous section; I’d decide this based on what reference data set we trust more.

We can then point to other frameworks (Meta-SV and Co) for better SV call integration. Highlight how this can and should feed back into the SNV/InDel call reports, flagging regions overlapping SV events for manual validation. Same for coverage. 

## Scaling

> Moving from exome to whole genome sequencing introduces a myriad of scaling and informatics challenges. In addition to the biological component of correctly identifying biological variation, it’s equally important to be able to handle the informatics complexities that come with scaling up to whole genomes.

That’s the theme for this section. Outline how bcbio handles this (parallelization, minimize I/O, pulling in information from <http://bcb.io/2013/05/22/scaling-variant-detection-pipelines-for-whole-genome-sequencing-analysis/>), including how we can monitor/benchmark different workflows and tools. Re-run resource benchmarks locally and on AWS for 10 WGS (NA12878 copies), use collectl information for graphs and Co (see <http://bcb.io/2014/12/19/awsbench/>). Ideally for whatever workflow we decided to be optimal above (germline, FreeBayes, minimal prep, same filters). 


## Data Sets and Metrics to distribute

* [ ] The [Coriell NA12878 sample information](https://catalog.coriell.org/0/Sections/Search/Sample_Detail.aspx?Ref=GM12878)
* [ ] Reads from the [Platinum Genome](http://www.illumina.com/platinumgenomes/) for [NA12878](http://www.ebi.ac.uk/ena/data/view/SAMEA1573618), 50x WGS
* [ ] Recent [GiaB reference materials](https://sites.stanford.edu/abms/content/giab-reference-materials-and-data), in particular the [integrated calls](ftp://ftp-trace.ncbi.nih.gov/giab/ftp/release/NA12878_HG001/NISTv2.19/) (v2.19) along with the [definition of callable regions](ftp://ftp-trace.ncbi.nih.gov/giab/ftp/release/NA12878_HG001/NISTv2.19/). See the [README](ftp://ftp-trace.ncbi.nih.gov/giab/ftp/release/NA12878_HG001/NISTv2.19/README.NIST.v2.19.txt) for details
* [ ] For SV calling we’ll have to distribute our own and include detailed information [how it was derived](http://bcb.io/2014/08/12/validated-whole-genome-structural-variation-detection-using-multiple-callers/). The SV calling uses information from the [Platinum Genomes Trio](http://www.ebi.ac.uk/ena/data/view/ERP001960) so we’ll need all samples. Brad’s [shell script](https://raw.github.com/chapmanb/bcbio-nextgen/master/config/examples/NA12878-trio-sv-getdata.sh) has the locations, but we’ll need to point to the raw validation data and scripts used to generate those
* [ ] BED file of [low complexity regions](https://github.com/lh3/varcmp/raw/master/scripts/LCR-hs37d5.bed.gz) generated by Heng Li in <http://bioinformatics.oxfordjournals.org/content/early/2014/07/03/bioinformatics.btu356>
* [ ] All config files
* [ ] All VCFs
* [ ] All BAMs?
* [ ] Raw data for collectl?

## Figures / Tables

* High-level bcbio workflow outline (see <http://bcb.io/2013/05/22/scaling-variant-detection-pipelines-for-whole-genome-sequencing-analysis/>)
* Germline FB/HaplotypeCaller/Samtools concordant/discordant SNPs/InDels to introduce concept. 
* Germline calls with FB/HaplotypeCaller with and without recalibration/re-alignment
* Germline calls with different filters applied (re ‘best practices’), FB/HaplotypeCaller with/without LCR, high depth filter (with the depth/ratio plots as supplemental figure)
* Cancer variant caller comparison (SNPs and Indels)
* FreeBayes optimization figure
* SV call comparison in germline or cancer
* bcbio parallelism approach, heterogenous cluster creation
* Performance metrics local vs AWS as graph and summary table

## Methods

* Need to agree on standard workflow and filtering to be kept consistent across comparisons
* Need description of bcbio.variation, in particular how variants/InDels are normalized between VCFs

## Open questions

* Do we want to include the FASTQ binning analysis, <http://bcb.io/2013/02/13/the-influence-of-reduced-resolution-quality-scores-on-alignment-and-variant-calling/>?
* Do we use the VQSR (and VQSR-equivalent for FreeBayes) in the general WGS workflow analysis? If so this needs to go into the methods section with the comparison figure in the supplements
* Do we touch upon the bcbio-vm and Docker part at all? Information can be pulled from <http://bcb.io/2014/03/06/improving-reproducibility-and-installation-of-genomic-analysis-pipelines-with-docker/> and <http://bcb.io/2014/12/19/awsbench/> but it’s tricky to integrate with the rest of the paper (other than the AWS benchmarking). 
* RNA-Seq yay or nay?
* For scaling approach do we show a 1WGS example with 16 vs 160 cores? I.e., what is the improvement going from 1 to 10 nodes? Do we show the NFS/Lustre story at all or keep it simple to raw numbers?


