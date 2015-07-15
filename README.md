Early draft paper describing [bcbio-nextgen](https://bcbio-nextgen.readthedocs.org/en/latest/index.html), a python toolkit providing best-practice pipelines for fully automated high throughput sequencing analysis.


## ToDo

1. Go over blog posts (below) and identify relevant figures to include
1. Organize all test data into a single spot (along with documentation, version information, etc.)
1. Re-run benchmarks. This is basically unifying the various blog posts from Brad's site with the same bcbio version, input data and validation data as part of the paper.
1.  Ideally provide IPython/RMarkdown scripts for the downstream comparison. I don't think we need timing information for the validation steps, just for the IT benchmark. 

## Benchmarks to Run

1. Run IT benchmarks locally and AWS for 10 WGS (NA12878 copies), use collectl information for graphs and co (see http://bcb.io/2014/12/19/awsbench/)
2. Run validation for GiaB with 2 different aligners, 3 different variant callers (exome), or with different callers and with/without realignment/recalibration (see http://bcb.io/2013/10/21/updated-comparison-of-variant-detection-methods-ensemble-freebayes-and-minimal-bam-preparation-pipelines/)
3. Run validation for cancer data vs ICGC/TCGA challenge with ~3 different callers (or the current SNP/InDel comparison) (see http://bcb.io/2015/03/05/cancerval/)
4. Run validation for 2-3 SV callers (see http://bcb.io/2014/08/12/validated-whole-genome-structural-variation-detection-using-multiple-callers/)

## Data Sets

TBA

## Figures / Tables

TBA
