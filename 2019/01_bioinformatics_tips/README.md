# Bioinformatics advice I wish I learned 10 years ago

- V. Keith Hughitt (May 23, 2019)

## Overview

Below is a list of some of things I've learned over the past ten or so years working
in bioinformatics, and, more generally, in scientific data analysis.

Over the years, my overall approach, preferred tools, and software environment has
changed many times, based on what works and what doesn't work for me.

This is not to say that my approach is the only way to do things, or even the best way
to do things, but hopefully some of the ideas or tools mentioned below will be useful to
others in their own path towards bioinformatics bliss.

## Outline

- Background
- Strategy
- Organization
- Annotations
- Visualization
- Modeling
- Python & R
- Tools
- Command-line Fun

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


