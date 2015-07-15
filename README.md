Early draft paper describing [bcbio-nextgen](https://bcbio-nextgen.readthedocs.org/en/latest/index.html), a python toolkit providing best-practice pipelines for fully automated high throughput sequencing analysis.

## ToDo

1. Go over blog posts (below) and identify relevant figures to include [DOING]
1. Organize all test data into a single spot (along with documentation, version information, etc.)
2. Clarify differences between LCR (used in some of the blog posts) and GiaB callable regions filter
1. Re-run benchmarks. This is basically unifying the various blog posts from Brad's site with the same bcbio version, input data and validation data as part of the paper. Make sure we agree on the default metrics to set as we make our way through the benchmarks -- e.g., to be able to show the impact of the LCR and high depth regions those filters need to be disabled by default (or we can show best practices throughout and show their impact by disabling them). 
1.  Visualize differences sticking to concordant / missing / extra / shared|mixed variant description. Ideally provide IPython/RMarkdown scripts for the downstream comparison. I don't think we need timing information for the validation steps, just for the IT benchmark. Code to get started at <https://github.com/chapmanb/bcbb/tree/master/validation>.
2. Move original text from blog posts to main document
2. Pull architecture information out of http://bcb.io/2014/03/06/improving-reproducibility-and-installation-of-genomic-analysis-pipelines-with-docker/ and http://bcb.io/2013/05/22/scaling-variant-detection-pipelines-for-whole-genome-sequencing-analysis/

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


## Still to organize

1. Run IT benchmarks locally and AWS for 10 WGS (NA12878 copies), use collectl information for graphs and co (see http://bcb.io/2014/12/19/awsbench/)

## Data Sets and Metrics to distribute

* [Coriell NA12878 sample information](https://catalog.coriell.org/0/Sections/Search/Sample_Detail.aspx?Ref=GM12878)
* [Platinum Genomes](http://www.illumina.com/platinumgenomes/) (Exome or WGS -- I’d prefer to run everything as WGS data set)
* Callable regions filter as defined by GiaB, to be used for all comparisons
* All config files
* All BED files (LCRs) for re-use by community
* All VCFs
* Raw data for collectl?

## Figures / Tables

* High-level bcbio workflow outline
* Germline FB/HaplotypeCaller/Samtools concordant/discordant SNPs/InDels to introduce concept. 
* Germline calls with FB/HaplotypeCaller with and without recalibration/re-alignment
* Germline calls with different filters applied (re ‘best practices’), FB/HaplotypeCaller with/without LCR, high depth filter (with the depth/ratio plots as supplemental figure)
* Cancer variant caller comparison (SNPs and Indels)
* FreeBayes optimization figure
* SV call comparison in germline or cancer
* Performance metrics local vs AWS as graph and summary table

## Open questions

* Do we want to include the FASTQ binning analysis, <http://bcb.io/2013/02/13/the-influence-of-reduced-resolution-quality-scores-on-alignment-and-variant-calling/>?
* Do we use the VQSR (and VQSR-equivalent for FreeBayes) in the general WGS workflow analysis? If so this needs to go into the methods section with the comparison figure in the supplements

