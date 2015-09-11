Early draft paper describing [bcbio-nextgen](https://bcbio-nextgen.readthedocs.org/en/latest/index.html), a python toolkit providing best-practice pipelines for fully automated high throughput sequencing analysis.

## ToDo

1. [x] Go over blog posts (below) and identify relevant figures to include 
1. [x] Pull architecture information out of <http://bcb.io/2014/03/06/improving-reproducibility-and-installation-of-genomic-analysis-pipelines-with-docker/> and <http://bcb.io/2013/05/22/scaling-variant-detection-pipelines-for-whole-genome-sequencing-analysis/>.
1. [x] Clarify differences between LCR (used in some of the blog posts) and GiaB callable regions filter
1. [ ] Organize all test data into a single spot (along with documentation, version information, bcbio config files, etc.) [DOING]
1. [ ] Define/Agree on the default metrics to set as we make our way through the benchmarks -- e.g., to be able to show the impact of the LCR and high depth regions those filters need to be disabled by default (or we can show best practices throughout and show their impact by disabling them). Output is a set of bcbio `yaml` config files.
1. [ ] Re-run benchmarks. This is basically unifying the various blog posts from Brad's site with the same bcbio version, input data and validation data as part of the paper. Store resulting csv files along with the primary data and `yaml` config files
1.  [ ] Visualize differences sticking to concordant / missing / extra / shared|mixed variant description. Ideally provide IPython/RMarkdown scripts for the downstream comparison. I don't think we need timing information for the validation steps, just for the IT benchmark. Code to get started at <https://github.com/chapmanb/bcbb/tree/master/validation>.
1. [ ] Move original text from blog posts to main document.


## Benchmarks to Run



### Germline

Use the [NA12877, NA12878, and NA12882 trio at 50x WGS](http://www.ebi.ac.uk/ena/data/view/ERP001960) for all validation steps, with NA12878 SNPs and InDels validated against GiaB. Batch calling is sufficient for this. 

For germline, the high coverage filter is the default for FreeBayes. We should also turn on `remove_lcr: true`. This is not the default since I don't think people would want those regions removed for real calls but it helps a ton for comparisons.

**Ensemble calling:** Would be good to limit to `bwa-mem` with `FreeBayes`, `GATK HaplotypeCaller` and `samtools` as a third option, show benefits of Ensemble call as currently implemented (found in 2 out of 3 callers that are independent) as a proof of principle. See <http://bcb.io/2013/02/06/an-automated-ensemble-method-for-combining-and-evaluating-genomic-variants-from-multiple-callers/>. Worth keeping an eye on the time spent calling variants with each tool so we can discuss time vs benefits story. 

**On ‘best practices’:** Lots of talking about what best practice means, but it’s actually not really defined (other than ‘what everyone else does’). Add a frequently discussed topic such as the need for re-alignment/re-calibration (see <http://bcb.io/2013/10/21/updated-comparison-of-variant-detection-methods-ensemble-freebayes-and-minimal-bam-preparation-pipelines/>) to show value in being able to actually test different ‘best practice’ suggestions.

I would keep better filtering (see <http://bcb.io/2014/05/12/wgs-trio-variant-evaluation/>) and the impact of joint calling (see <http://bcb.io/2014/10/07/joint-calling/>), e.g., on a single NA12878 run with and without a reference 1000G panel / subset out of this paper or limit it to supplements if necessary. 

Check that VQSR is enabled for the GATK-caller validation and that we have similar filters in place for FreeBayes and samtools.


### Somatic

This one’s relatively straightforward as there’s only one comparison so far (see <http://bcb.io/2015/03/05/cancerval/>). Introduce the [ICGC-TCGA DREAM data](http://bcb.io/2015/03/05/cancerval/) and show two things:

**Cancer variant caller comparison:** Just as important to compare differences in callers (if not more so, far from a set of ‘standard’ callers in this domain). Repeat Brad’s analysis just to make sure we have the config files and data all in one place for distribution. Lineup of callers is MuTect/Scalpel, FeeBayes, VarScan and VarDict. We can skip the ensemble call here. 

**Tweaking tools:** Highlight how the benchmark sets are not only important to test new tools but to optimize existing ones. FreeBayes with default and Speedseq-optimized parameters. 

Don’t think I’d include the cancer-only story as it would need more work (comparison of filters, manual curation) though I really like the detection of a false variant call. Up for discussion. 

### SV

Benchmark sets not limited to SNPs/InDels, also need to include larger events. See <http://bcb.io/2014/08/12/validated-whole-genome-structural-variation-detection-using-multiple-callers/>. We can use the recent [GiaB SV test data set](https://sites.stanford.edu/abms/content/giab-reference-materials-and-data) for this; still need to go through the [svclassify paper](http://biorxiv.org/content/early/2015/05/16/019372).

**Ensemble SV approaches:** A repeat of the previous concept. Old run was with Lumpy, Delly, cn.mops; for this we’d switch to Lumpy, Wham and Manta. Stratifiy by size of event. We can then point to other frameworks (Meta-SV and Co) for better SV call integration. Highlight how this can and should feed back into the SNV/InDel call reports, flagging regions overlapping SV events for manual validation. Same for coverage. 

## Scaling

> Moving from exome to whole genome sequencing introduces a myriad of scaling and informatics challenges. In addition to the biological component of correctly identifying biological variation, it’s equally important to be able to handle the informatics complexities that come with scaling up to whole genomes.

That’s the theme for this section. Outline how bcbio handles this (parallelization, minimize I/O, pulling in information from <http://bcb.io/2013/05/22/scaling-variant-detection-pipelines-for-whole-genome-sequencing-analysis/>), including how we can monitor/benchmark different workflows and tools. Re-run resource benchmarks locally and on AWS for 10 WGS (NA12878 copies), use collectl information for graphs and Co (see <http://bcb.io/2014/12/19/awsbench/>). Ideally for whatever workflow we decided to be optimal above (germline, FreeBayes, minimal prep, same filters). Lorena has already run this both locally and on AWS, if possible we should re-use those collectl files. If we need to re-generate them I’d love to re-use her code/scripts at least.


## Data Sets and Metrics to distribute

* [ ] The [Coriell NA12878 sample information](https://catalog.coriell.org/0/Sections/Search/Sample_Detail.aspx?Ref=GM12878)
* [ ] Reads from the [Platinum Genome](http://www.illumina.com/platinumgenomes/) for the 50x WGS trio of [NA12877](http://www.ebi.ac.uk/ena/data/view/SAMEA1573614), [NA12878](http://www.ebi.ac.uk/ena/data/view/SAMEA1573618) and [NA12882](http://www.ebi.ac.uk/ena/data/view/SAMEA1573621)
* [ ] Data from the [ICGC-TCGA DREAM Mutation Challenge](https://www.synapse.org/#!Synapse:syn312572), right now limited to the [5 simulated-sequencing tumors](https://www.synapse.org/#!Synapse:syn312572/wiki/62018). Decision was to go for the `in silico 3` data set (100% cellularity, with clonality of 50%, 33%, 20%). Still need to grab the data, code for this is:

```Shell
wget --no-check-certificate https://cghub.ucsc.edu/software/downloads/GeneTorrent/3.8.5a/GeneTorrent-download-3.8.5a-94-CentOS5.8.x86_64.tar.gz
tar -xzvpf GeneTorrent-download-3.8.5a-94-CentOS5.8.x86_64.tar.gz
cghub/bin/gtdownload -v -c http://dream.annailabs.com/dream_public.pem -d https://dream.annailabs.com/cghub/data/analysis/download/b19d76a0-a487-4c50-8f9c-3b4d5e53239d
ln -s b19d76a0-a487-4c50-8f9c-3b4d5e53239d/*.bam
ln -s b19d76a0-a487-4c50-8f9c-3b4d5e53239d/*.bam.bai
cghub/bin/gtdownload -v -c http://dream.annailabs.com/dream_public.pem -d https://dream.annailabs.com/cghub/data/analysis/download/8fe6fc33-2daf-4393-929f-7c3493d04bef
ln -s 8fe6fc33-2daf-4393-929f-7c3493d04bef/*.bam
ln -s 8fe6fc33-2daf-4393-929f-7c3493d04bef/*.bam.bai
```

I could not find the SV regions that got introduced into the sample and which should be discounted during benchmarking so we might just count all regions (frankly we should -- they’ll be present in actual tumor samples, too).  

* [ ] Recent [GiaB reference materials](https://sites.stanford.edu/abms/content/giab-reference-materials-and-data), in particular the [integrated calls](ftp://ftp-trace.ncbi.nih.gov/giab/ftp/release/NA12878_HG001/NISTv2.19/) (v2.19) along with the [definition of callable regions](ftp://ftp-trace.ncbi.nih.gov/giab/ftp/release/NA12878_HG001/NISTv2.19/) (ftp://ftp-trace.ncbi.nih.gov/giab/ftp/release/NA12878_HG001/NISTv2.19/). See the [README](ftp://ftp-trace.ncbi.nih.gov/giab/ftp/release/NA12878_HG001/NISTv2.19/README.NIST.v2.19.txt) (ftp://ftp-trace.ncbi.nih.gov/giab/ftp/release/NA12878_HG001/NISTv2.19/README.NIST.v2.19.txt) for details
* [ ] For SV calling we can use the most recent GiaB SV data set (limited to insertions and deletions). From Justin Zook:

> We have uploaded a set of high-confidence large deletions and insertions for NIST RM8398/NA12878 to the GIAB FTP site.  This set of 2676 deletions and 68 insertions was classified as high-confidence because the calls had evidence from at least 3 technologies using the svclassify machine learning model, which uses a variety of annotations calculated from reads mapped in and around the putative structural variant.   All high-confidence sites also had at least 3 technologies with at least 3 reads supporting the ALT allele using the [svviz semi-automated visualization method](http://biorxiv.org/content/early/2015/03/07/016063).  Our svclassify manuscript describing our methods used to generate these calls is on [biorxiv](http://biorxiv.org/content/early/2015/05/16/019372)

The data is available as two BED files for insertions (ftp://ftp-trace.ncbi.nih.gov/giab/ftp/technical/svclassify_Manuscript/Supplementary_Information/Personalis_1000_Genomes_deduplicated_deletions.bed) and deletions (ftp://ftp-trace.ncbi.nih.gov/giab/ftp/technical/svclassify_Manuscript/Supplementary_Information/Personalis_1000_Genomes_deduplicated_deletions.bed). 

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

## Compiling this paper:

```
$ brew install pandoc pandoc-citeproc
$ brew cask install mactex
$ git clone https://github.com/kjhealy/pandoc-templates ~/.pandoc
$ sudo cp -a sty/* /usr/local/texlive/texmf-local/tex/latex/local/ && texhash
$ bin/minionpro.sh
$ make
```
