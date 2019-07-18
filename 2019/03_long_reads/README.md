# Long Reads: Mapping to Genome Assembly
### Arang Rhei - July 18, 2019

## Overview
- Read length and accuracy of PacBio and Nanopore technologies
- Comparison of methods for mapping and identification of structural variants
- Challenges in assembling entire human genome
  - Human reference genome is currently incomplete
  - Complete assembly of X chromosome using unique markers
- Phasing chromosomes of diploid organisms
- Ongoing genome projects:
  - [1000 Genomes Project](http://www.internationalgenome.org/)
  - [The Vertebrate Genome Project]([https://vertebrategenomesproject.org/])

## References
- [NHGRI Genome Informatics Section](https://genomeinformatics.github.io/)
- [Sedlazeck et al., Accurate detection of complex structural variations using single-molecule sequencing. Nat Methods (2018)](https://doi.org/10.1038/s41592-018-0001-7)

## Q&A
**Q:** In the plot of Read Length x QV, what does QV represent?
**A:** In the plot, QV represents raw read accuracy (not % identity w.r.t. the a priori assembly)

**Q:** Can you go over the cost considerations of PacBio and Nanopore sequencing?
**A:** Depends on the relationship of the lab/institute with the company, as well as relative coverage needs.  NISC currently has a good pricing deal with Pac Bio.  Nanopore (GridION) is cheaper, can use PacBio for polishing.

**Q:** Why were Europeans excluded from the latest round of the Pan-genome project?
**A:** It is focused on characterizing variants/haplotypes of lesser represented ethnic groups around the world, relative to the current reference (which is largely/entirely)  derived from Europeans.  The focus is to diversify the reference genome.

**Q:** Use/efficacy of long read sequencing of RNA for improved transcriptome assembly?
**A:** Certainly an area for improvement. May need to deal with issues with recovering enough material for sufficient coverage and quantitation.

**Q:** In a project to characterize/quantify changes in TE insertions in a haploid cell line (comparing a gene KO vs WT), what is a reasonable estimate for minimum coverage, and is it feasible for a single smaller lab? Ie. with a MinIon
**A:** Yes, it should be possible. Rough estimate would be 50X coverage for a solid de novo genome assembly.  Suggest using a starter kit from Nanopore.

**Q:** Has long-read sequencing improved in its efficacy/application for large-scale pharmogenomics?
**A:** Yes! There is a large scale pharmacological study going on in the Netherlands utilizing PacBio HiFi sequencing to characterize genotypic associations with drug efficacy.  For a ~20kb region of interest, HiFi should be a good fit and more effective than regular length sequencing.  Cost of HiFi is approximately (1X : 3X) compared to regular length.
