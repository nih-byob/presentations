# Bioinformatics advice I wish I learned 10 years ago

V. Keith Hughitt (May 23, 2019)

## Overview

Below is a list of some of things I've learned over the past ten or so years working
in bioinformatics, and, more generally, scientific data analysis.

Over the years my overall approach, preferred tools, and software environment has
changed many times based on what works and what doesn't work for me.

This is not to say that my approach the only way to do things, or even the best way
to do things, but hopefully some of the ideas or tools mentioned below will be useful to
others in their own path towards bioinformatics bliss.

## TL;DR

1. Don't rely on intuition
2. Subsample your data during development to allow for quick iteration
3. Use pipeline tools
4. Be careful of over-engineering your data
5. Use simple models whenever possible; regularization is essential
6. Devise a way to evaluate predictions early on
7. When using supervised feature selection, apply it _inside_ of the same outer
   cross-validation fold used for model training.
8. Metadata is great
9. GSEA > Hypergeometric test / FET 
10. In general, prefer continuous methods > those that discretize your data...
11. MSigDB and Enrichr are great sources for gene sets.
12. BiomaRt is down a lot
13. Dimension reduction is your friend

## Outline

- [Background](#background)
- [Workflow](#workflow)
- [Organization](#organization)
- [Annotations](#annotations)
- [Visualization](#visualization)
- [Modeling](#modeling)
- [Python & R](#python--r)
- [Bioinformatics Tools](#bioinformatics-tools)
- [Reproducibility](#reproducibility)
- [Command-line Fun](#command-line-fun)
- [Advice for Students](#advice-for-students)

## Background

Before I discuss some of things that I think are useful to know, there are a few topics
that are absolutely _essential_, but that I do not plan to discuss here. Each of these
are important enough to warrant their own tutorials (or series of tutorials), but for
the purposes of this tutorial, I'm only going to mention them and point to some
resources that can be used to learn them if you aren't already familiar.

1. Use [version control](https://github.com/NLM-Reproducibility-Project/Repro_git_vcs_tutorial).
2. Learn at least a little bit of [R](https://www.coursera.org/courses?query=r%20programming) and [Python](https://www.coursera.org/courses?query=r%20programming).
3. Learn about software best practices (e.g.
   [Python](https://gist.github.com/sloria/7001839) / [R](https://swcarpentry.github.io/r-novice-inflammation/06-best-practices-R/))
4. Adopt a style guide for your code ([Python](https://github.com/python/black) / [R](http://adv-r.had.co.nz/Style.html))
5. Use an operating system that the majority of scientific tools and libraries are written for (hint:
   not [this one](https://www.microsoft.com/en-us/windows)..)
6. [StackOverflow](https://stackoverflow.com/), [Biostars](https://www.biostars.org/), and [Bioconductor Support](https://support.bioconductor.org/) are your friends.
7. (So is [Sean Davis](https://twitter.com/seandavis12?lang=en)...)

## Workflow

My general workflow/strategy for approaching new bioinformatics projects has evolved a
lot over the years and will almost certainly continue to do so in the years to come.

Some things that I found to be useful in this respect are:

1. Background research
    - Spend a bit of time doing background research to understand:
        - The underlying biological problem of interest
        - How the data was collected and what's been done to the data thus far
        - Approaches used by others in the field working on similar problems / datasets
          (comparison review papers and bioconductor views and workflows are useful
          here)
2. Take notes
    - Take lots of notes and store them along-side of your code/data. This is mostly for
      _future_-you, but it will come in handy when you have to set a project aside and
      come back to it 6 months later.
3. Ask for metadata
    - Get as much information as you can about the data you plan to analyze; this can be
      useful for things like batch adjustment and detecting unexpected relationships
      between genomic and phenotypic features.
4. Don't assume that your metadata is correct
    - Sometimes samples get mislabeled, or metadata is record incorrectly.
    - PCA / t-SNE plots can help to spot possible errors in such labels.
5. Adopt a consistent project organization
    - Adopt a project organization (more on this below..) and use it to ensure
      consistency across projects.
6. Perform EDA
    - Start with some exploratory data analysis (EDA) to familiarize yourself with the
      data and to check for unexpected patterns, outliers, etc.
7. Use Pipeline tools
    - After performing initial EDA using something like RMarkdown / Jupyter notebooks,
      **strongly consider** using a [pipeline
      framework](https://snakemake.readthedocs.io/en/stable/) for the final analyses. 
    - Pipeline tools also generally make it easier to deploy your code on a cluster such
      as Biowulf.
8. Write resuable code
    - If you are not using a pipeline tool, at the very least, considering writing code
      in a generic/reusable way, and extract out any project- or data-specific
      parameters into a separate [YAML config
      file](https://learn.getgrav.org/16/advanced/yaml).
   - Different config files (e.g. `config-v1.0` and `config-v1.1`) can then be used to
     iterate and test different ideas.
9. Create a data flow diagram
    - Sketch out your data flow early on, and think about what types of outputs you
      ultimately wish to create, and whether the tool or approach you plan to use is
      suitable for that.
10. Do most of your development on a small subset of the data!
    - Bioinformatics analyses frequently take a long time to run.
    - Chances are, there are some bugs in your code (for R, this likelihood increases
      quite a bit..)
    - Instead of attempting to perform your analysis on the full set of data, waiting
      two hours (or two days) and then realizing it's `write_csv(..., delim=XX)` and
      not `write_csv(..., sep=XX)`', instead do most of your development small subset of
      the data, make sure things are working, and then scale it back up to the full
      dataset to get a sense for how the analyses are going.
    - You can include something like a `dev = TRUE` switch at the top of your file (or
      in your config.yml) to allow you to quick toggle data sub-sampling on/off.
    - The end result of this will be that you will be able to iterate, and ultimately
      finish writing your analyses, but faster..
11. Avoid unnecessary data transformations
    - Many data transformations make assumptions about the nature of the underlying
      data or the relationship between element in a dataset.
    - It's easy to blindly apply such a transformation that _in theory_ is going to help
      bring out a signal in the data.
    - I find, however, that unless you are really careful to make sure those assumptions
      are met (and the transformation itself actually does what it purports to do), it's
      you may end up actually removing important signal from the data.
    - Examples:
        - Over-zealous batch adjustment (with ComBat, consider setting `mean.only =
          TRUE`)
        - Toplogical Matrix transformation from WGCNA (may be better off just using the
          correlation-based distance matrix)
        - Quantile normalization (useful if you can be sure that samples have similar
          underlying distributions, but can hide real differences if you aren't
          careful..)
    - Instead, opt for lighter transformations (log-scale, size factor adjustment,
      etc.), unless you are sure about the assumptions and even then, be sure to always
      check the resulting data to see if it matches expectations.
12. Be careful relying too much on your intuition..
    - It's important to build intution about the underlying biology and the nature of
      the data you are working on.
    - If you aren't careful, however, it's easy to follow what _intuitively_ seems like
      a really good far down the road, only to discover later that the idea isn't
      working out as expected..
    - This is especially true in fields like bioinformatics and data science where there
      is something of an art to anlyzing data and about a thousand different ways to
      approach a problem.
    - Instead, spend some time early on trying to think of ways you can systematically
      assess the quality of your predictions.
    - This can be very challenging to do, especially in cases where you are working on
      something that hasn't been done before, but even a heuristic method of evaluating
      predictions is better than none.. (ex. measuring the "shared information" between
      feature and multivariate response datasets when deciding how to perform feature
      engineering prior to machine learning..)

In general, avoid writing code that requires many manual steps to run. This will slow
you down during development and also limit the likelihood that others will use / be able
to use was  you've created.

## Organization

### Project Organization

Project organization is another aspect of my work that is constantly evolving. There are
many different approaches to organizing research software, code, annotations, etc., each
with their own benefits and drawbacks.

My current approach looks something like this:

```
src
├── nih
│   ├── mm-kms28bm
│   ├── mm-mmrf
│   ├── other-gene-comparison
│   ├── pan-pdx-models
│   ├── sclc-george2015
│   └── sclc-jiang2016
└── umd
    └── ...
```

- Top-level directory corresponds to where I did the work
- The next level down includes the individual project folders, each of which are of the 
  form:

```
<biotype>-<name>
```

- For example, at NCI, most of the projects I'm involved with relate to cancer, so the 
prefixes tend to refer to different cancer types (e.g. mm = "multiple myeloma" and sclc = "small
cell lung cancer")
- The "name" part is just a short descriptive name relating to the specific project, or, 
in cases where I'm using data from other experiments, I may include the first author + 
year.

For convenience, I use all lowercase and try and keep the names short.

The structure within each of these project-specific folders varies, depending on the 
type of analyses, but often it looks something like this:

```
proj
├── dev
│   ├── clustering.Rmd
│   └── dim_reduce.Rmd
├── doc
├── output
│   ├── 2019-05-10
│   ├── 2019-05-19
│   └── 2019-05-21
├── README.Rmd
├── renv
├── rmd
└── scripts
```

- `dev` - EDA and other short 1-off analyses to test out various ideas.
- `doc` - Markdown notes to myself, metadata, etc. not under version control.
- `output` - Results generated by the main analysis, as well as the generated HTML
  outputs themselves. Date- or version-specific sub-directories are used to separate
  of multiple versions of the analysis.
- `renv` - [renv](https://github.com/rstudio/renv)-generated files and folders used to
  keep track of package versions for R/Rmd-based projects.
- `rmd` - For RMarkdown projects, analyses are split up into modular RMarkdown files,
  each of which is included in the main `README.Rmd` analysis script.
- `scripts` - Helper scripts used to clean up external data or perform other tasks that
  aren't directly related to the research question, but may be useful to track.

### Data Organization

I generally like to keep data and code separate from one another, for at leats a few
reasons:

1. Large datasets can be stored on a larger RAID storage partition
2. Code can be stored on a faster partition which is backed up on Dropbox, etc.
3. Related datasets can be stored together, making it easier to do multi-experiment
   analyses.

My current setup looks like:

```
data
├── annotations
│   ├── clue_drug_repurposing_hub
│   │   ├── repurposing_drugs_20180907.txt
│   │   └── repurposing_samples_20180907.txt
│   ├── cpdb
│   │   ├── human
│   │   └── mouse
│   ├── dgidb
│   │   └── 3.0.2
│   ├── gene-drug-knowledge-database
│   │   └── Knowledge_database_v20.0.xlsx
│   └── ...
├── containers
│   └── singularity
│       └── sclc-george2015
├── projects
│   ├── nih
│   │   ├── mm-feature-selection
│   │   ├── mm-p3-variants
│   │   └── sclc-doe
│   └── umd
│       ├── coex_networks
│       ├── param_opt
│       └── tcruzi_hsapiens
├── public
│   └── human
│       ├── array_express
│       ├── geo
│       │   └── GSE6477
│       │       ├── processed
│       │       │   ├── GSE6477_expr.csv
│       │       │   └── sample_metadata.csv
│       │       └── raw
│       │           ├── GPL96.soft
│       │           └── GSE6477_series_matrix.txt.gz
│       ├── hmcl
│       ├── mao2019
│       ├── mmrf_commpass
│       └── smith2019
└── ref
    ├── adapters
    │   ├── lcdb
    │   └── trimmomatic
    └── human
        ├── agilent
        ├── gatk
        ├── gencode-v30
        └── rRNA
```

This structure certainly isn't perfect (for example, the `public/` datasets folder
includes a mix of datatypes, so finding all transcriptomic data is would not be so
straight-forward), but I like it more than any of the previous directory structures I've
used in the past.

### Cookiecutter

![cookiecutter logo](https://camo.githubusercontent.com/c2095c350e36abaafd738dcdc6cdc9e7d585d69e/68747470733a2f2f7261772e6769746875622e636f6d2f617564726579722f636f6f6b69656375747465722f336163303738333536616466356131613732303432646665373265626661346139636435656633382f6c6f676f2f636f6f6b69656375747465725f6d656469756d2e706e67)

[Cookiecutter](https://cookiecutter.readthedocs.io/en/latest/) is a useful tool to help
you create consistent project structures.

You can either point cookiecutter to a [pre-existing
template](https://cookiecutter.readthedocs.io/en/latest/readme.html#available-cookiecutters), for example:

- [cookie-cutter-data-science](https://github.com/drivendata/cookiecutter-data-science)
- [cookiecutter-r-data-analysis](https://github.com/bdcaf/cookiecutter-r-data-analysis)
- [cookiecutter-rmd-data-science](https://github.com/khughitt/cookiecutter-rmd-data-science)
- [cookiecutter-docker-science](https://github.com/docker-science/cookiecutter-docker-science)
- [cookiecutter-reproducible-science](https://github.com/mkrapp/cookiecutter-reproducible-science)

..Or, you can create your own project template.

Cookiecutter uses the template to generate a file and directory structure defined by the
template, with useful boiler-plate code, etc. created for you.

The templates format is quite easy to work with, so as long as you can decide on some
structure or set of structures that work for you, it is not difficult to translate them
into a cookiecutter template.

## Annotations

### Gene Sets

1. Not all annotations are created equal.
2. Some of the annotation sources that I've found to be particularly informative are:
    - [MSigDB](http://software.broadinstitute.org/gsea/msigdb/collections.jsp) (includes
      GO, KEGG, Reactome, etc.)
    - [DrugBank](http://amp.pharm.mssm.edu/Harmonizome/dataset/DrugBank+Drug+Targets)
    - [DSigDB](http://tanlab.ucdenver.edu/DSigDB/DSigDBv1.0/download.html)
3. KEGG is quite popular, but I haven't found it to be too useful for gene set
   enrichment analyses.
4. ConsensusPathDB (CPDB) is another meta-database that aggregates information from a
   large number of other sources including KEGG, but again, it hasn't been as useful in
   my own work.

### Mapping between gene identifiers

To map between gene identifiers, try:

- [annotables](https://github.com/stephenturner/annotables)
- [MyGene.py](http://docs.mygene.info/projects/mygene-py/en/latest/) (Python)
- [genemunge](https://github.com/unlearnai/genemunge) (Python)

In R, you can also use the Org.xx packages (e.g. ), however the syntax to map
identifiers is a bit more verbose.

There are also inferences to [BioMart](http://www.biomart.org/) in both R 
([biomaRt](https://bioconductor.org/packages/release/bioc/html/biomaRt.html)) and Python 
([pybiomart](https://github.com/jrderuiter/pybiomart)), however, the BioMart servers are
not always up and so these methods will fail sometimes / require switching mirrors.

## Visualization

**R**

- [ggplot](https://ggplot2.tidyverse.org/)
- [heatmaply](https://cran.r-project.org/web/packages/heatmaply/vignettes/heatmaply.html) / [aheatmap](http://nmf.r-forge.r-project.org/aheatmap.html) (static)
- [Rtsne](https://cran.r-project.org/web/packages/Rtsne/Rtsne.pdf)
- [UWOT](https://github.com/jlmelville/uwot)
- [dimred](https://github.com/gdkrmr/dimRed)
- [shiny](https://shiny.rstudio.com/)
- [plotly](https://plot.ly/)

Use the [viridis](https://cran.r-project.org/web/packages/viridis/vignettes/intro-to-viridis.html) 
package for high-contrast accessible plots in R (in Python, viridis is the default
colormap for Matplotlib.)

**Python**

- [plotly](https://plot.ly/python/)
- [seaborn](https://github.com/mwaskom/seaborn)
- [UMAP](https://umap-learn.readthedocs.io/en/latest/)

## Modeling

- Feature selection / CV strategy
- [broom](https://cran.r-project.org/web/packages/broom/vignettes/broom.html)
- [tidymodels](https://www.tidyverse.org/articles/2018/08/tidymodels-0-0-1/)

## Python & R

### Python

- [ipython](https://ipython.org/)
- [jupyter notebook](https://jupyter.org/)
- [black](https://github.com/python/black)

### R

![radian console
screenshot](https://user-images.githubusercontent.com/1690993/30728530-b5e9eb5c-9f26-11e7-8453-73a2e880c9de.png)
(radian console for R)

- [RStudio](https://www.rstudio.com/) / [Nvim-R](https://github.com/jalvesaq/Nvim-R)
- [radian](https://github.com/randy3k/radian) Enhanced console for R
- [pak](https://github.com/r-lib/pak) Intelligent & parallelized package installer for R
- [colorout](https://github.com/jalvesaq/colorout) colorized R console
- [txtplot](https://cran.r-project.org/web/packages/txtplot/index.html) ascii plots
- [janitor](https://cran.r-project.org/web/packages/janitor/) data tidying (the
  `tabyl()` function is really useful..)

`tabyl()` example:

```r
# table()
> table(iris$Species)

    setosa versicolor  virginica 
        50         50         50 

# tabyl()
> janitor::tabyl(iris$Species)
 iris$Species  n   percent
       setosa 50 0.3333333
   versicolor 50 0.3333333
    virginica 50 0.3333333
```


## Reproducibility

- Use [renv](https://github.com/rstudio/renv) to control r dependencies.
- Use [conda](https://docs.conda.io/en/latest/) to control Python ond other
  dependencies.
- Use [snakemake](https://snakemake.readthedocs.io/en/stable/) or another pipeline
  tool to make it easy to re-run your entire analyses.
- Use [Docker](https://docs.docker.com/) or [Singularity](https://www.sylabs.io/docs/) containers along-side of
  these if you want to maximize the chances that someone else can reproduce your
  results.
- Remember to set a random seed before running any non-deterministic code:
    - `set.seed(1)` (R)
    - `import random; random.seed(1)` (Python)
    - `import numpy; numpy.random.seed(1)` (Python / NumPy)
- Include system / version information in analysis log / reports:
    - `sessionInfo()`
    - `system('git rev-parse --short HEAD', intern = TRUE)`

## Command-line Fun

While not strictly bioinformatics-related, since you are likely to spend a lot of time
on the common-line, tools that make you more effective on the command-line are likely to
have widespread benefits to your producitity (and sanity).

Here are some of the ones I use the most:

- [Zsh](https://www.howtogeek.com/362409/what-is-zsh-and-why-should-you-use-it-instead-of-bash/)
    - [oh-my-zsh](https://ohmyz.sh/)
    - [biozsh](https://github.com/kloetzl/biozsh.git)
- [Tmux](https://hackernoon.com/a-gentle-introduction-to-tmux-8d784c404340)
    - [powerline](https://github.com/powerline/powerline)
- [Neovim](https://neovim.io/)
- [The Silver Searcher](https://github.com/ggreer/the_silver_searcher)
- [fasd](https://github.com/clvv/fasd) (or [z.lua](https://github.com/skywind3000/z.lua))
- [lsd](https://github.com/Peltoche/lsd)
- [fd](https://github.com/sharkdp/fd)
- [fzf](https://github.com/junegunn/fzf)
- [visidata](https://visidata.org/)
- [csvkit](https://csvkit.readthedocs.io/en/latest/)
- [xclip](https://github.com/astrand/xclip)

## Advice for Students

If you are in the early stages of working towards a career in bioinformatics, or if you
are just interested in developing your bioinformatics skillset in a rigorous way, these
are a few suggestions that I think will benefit you a lot in the long run:

1. Use Linux
2. Learn Python (and then R..)
3. Take a course on [software engineering](https://www.datacamp.com/courses/software-engineering-for-data-scientists-in-python)
4. If you have the chance to take extra math courses, take a course on linear algebra,
   and one or two courses on probability & statistics / modeling.
5. Learn about approaches to reproducible research and apply them early on.
6. Play around with many different datasets to build your intution;
   [kaggle](https://www.kaggle.com/) is a great resource for this.

