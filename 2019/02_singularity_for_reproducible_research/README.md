# README
Henoke Shiferaw (June 20, 2019)

### Contents:
SingularityTalk.ppt - These are the slides from my talk on June 20, 2019 on Singularity for Reproducible Research
ApplicationOfSingularity - This is the Markdown file I shared in my talk which shows the recipe file that I made to build my container and some more useful commands to know.

## Useful Links:

[Singularity version 3.2 Documentation](https://sylabs.io/guides/3.2/user-guide/index.html)

[Talk by Gregory Kurtzer – Lead Developer of Singularity](https://www.youtube.com/watch?v=z_HWonvcIZo)

[Singularity on Biowulf](https://hpc.nih.gov/apps/singularity.html)

[Basic Tutorial of Singularity from Biowulf](https://github.com/NIH-HPC/Singularity-Tutorial)

[Best Practices for making Recipe files](https://singularity.lbl.gov/docs-recipes#best-practices-for-build-recipes)

[Installing the latest version of Singularity as of June 20, 2019 on Linux](https://sylabs.io/guides/3.2/user-guide/installation.html#install-on-linux)

## Creating Writable Containers
I wasn't able to go into this due to time, but if you want a space to be able test out the commands needed to install software into your container,
you can try creating a sandbox directory.

There is a way to convert your sandbox to the default squashfs image file but this is not considered good practice as you will not have the full documentation of how your container was made.

The recommended workflow would be test out your commands in a writable container. Then copy the commands that work to the necessary sections in the recipe file.
To learn more:
https://sylabs.io/guides/3.2/user-guide/build_a_container.html#creating-writable-sandbox-directories
