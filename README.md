Workflow to convert Markdown documents into HTML, PDF and DOCX with support for references. Based on Kieran Haley's [workflow](http://kieranhealy.org//blog/archives/2014/01/23/plain-text/):

## LaTex 

* Install [modern TeX distro](https://www.tug.org/mactex/)
* Add style files for [Memoir](https://github.com/kjhealy/latex-custom-kjh/tree/master/needs-memoir) and a [preamble](https://github.com/kjhealy/latex-custom-kjh/tree/master/needs-org-mode) to `~/Library/texmf`
* Install vc.sty (Version control for LaTeX)
* Grab [test file](https://github.com/kjhealy/latex-custom-kjh/blob/master/templates/basic/article.tex) and try to compile
* Do a test run, modify customized files if needed (e.g., remove fonts not available)

## Pandoc

* brew install pandoc
* brew install pandoc-citeproc
* Clone the [pandoc scripts](https://github.com/kjhealy/pandoc-templates) and move to ~/.pandoc

## Combine

* Tweak the included Makefile with the correct paths
* Added a section to also generate Word documents (without a dedicated template)
 
