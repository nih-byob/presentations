# Bioinformatics advice I wish I learned 10 years ago

V. Keith Hughitt (May 23, 2019)

## Overview

Below is a list of some of things I've learned over the past ten or so years working
in bioinformatics, and, more generally, in scientific data analysis.

Over the years, my overall approach, preferred tools, and software environment has
changed many times, based on what works and what doesn't work for me.

This is not to say that my approach is the only way to do things, or even the best way
to do things, but hopefully some of the ideas or tools mentioned below will be useful to
others in their own path towards bioinformatics bliss.

## TL;DR

...

## Outline

- [Background](#background)
- [Strategy](#strategy)
- [Organization](#organization)
- [Annotations](#annotations)
- [Visualization](#visualization)
- [Modeling](#modeling)
- [Python & R](#python-and-r)
- [Reproducibility](#reproducibility)
- [Tools](#tools)
- [Command-line Fun](#command-line-fun)

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

## Strategy

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
4. Adopt a consistent project organization
    - Adopt a project organization (more on this below..) and use it to ensure
      consistency across projects.
5. Perform EDA
    - Start with some exploratory data analysis (EDA) to familiarize yourself with the
      data and to check for unexpected patterns, outliers, etc.
6. Use Pipeline tools
    - After performing initial EDA using something like RMarkdown / Jupyter notebooks,
      **strongly consider** using a [pipeline
      framework](https://snakemake.readthedocs.io/en/stable/) for the final analyses. 
7. Write resuable code
    - If you are not using a pipeline tool, at the very least, considering writing code
      in a generic/reusable way, and extract out any project- or data-specific
      parameters into a separate [YAML config
      file](https://learn.getgrav.org/16/advanced/yaml).
   - Different config files (e.g. `config-v1.0` and `config-v1.1`) can then be used to
     iterate and test different ideas.
8. Create a data flow diagram
    - Sketch out your data flow early on, and think about what types of outputs you
      ultimately wish to create, and whether the tool or approach you plan to use is
      suitable for that.
9. **Do most of your development on a small subset of the data!**
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

Finally, [Cookiecutter](https://cookiecutter.readthedocs.io/en/latest/) is a useful tool
to help you create consistent project structures.

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
## Visualization
## Modeling
## Python & R

## Reproducibility

- Use [renv](https://github.com/rstudio/renv) to control r dependencies.
- Use [conda](https://docs.conda.io/en/latest/) to control Python ond other
  dependencies.
- Use [snakemake](https://snakemake.readthedocs.io/en/stable/) or another pipeline
  tool to make it easy to re-run your entire analyses.
- Use [Docker](https://docs.docker.com/) or [Singularity](https://www.sylabs.io/docs/) containers along-side of
  these if you want to maximize the chances that someone else can reproduce your
  results.

