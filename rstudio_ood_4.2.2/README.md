# An enhanced Singularity image for OOD RStudio

## The issue:
- UMass OOD (https://ood.umassmed.edu/) supports RStudio, and you are encouraged to build your own customized singularity image to run OOD RStudio with.
- The sample image `/share/pkg/containers/rstudio_example/r_4.1.2.sif` built with https://ood.umassmed.edu/public/rstudio_4.1.2.recipe.txt by the OOD team seems to work, however, when it comes to installing R packages, many system libraries are missing. Thus, it is barely usable.

## The solution:
- There is a well-maintained RStudio Docker image: https://hub.docker.com/r/rocker/rstudio/tags.
- It seems to have many of the commonly required system libraries, and can be used as a base OS for building a custom Singularity image.
- The corresponding Singularity recipe can be as simple as: `rstudio_ood_4.2.2.def`
```
  Bootstrap: docker
  From: rocker/rstudio:4.2.2

  %labels
        Author Kai.Hu@umassmed.edu

  %environment
        export PATH=/usr/lib/rstudio-server/bin:$PATH

  %runscript
        mkdir /tmp/run; rserver "${@}"

  %test
        rserver --help
```
- Frustratingly, even with the above image, some system libraries are still missing when trying to install certain R packages, here is a list of them:
  - left is the R package in trouble, middle is the missing system file, right is the system library to be installed in order to fix the issue
```
XVector       : zlib.h          : libz-dev
enrichplot    : libglpk.so.40   : libglpk-dev
Rhtslib       : lzma.h          : liblzma-dev
Rhtslib       : bzlib.h         : libbz2-dev
Cairo         : cairo.h         : libcairo2-dev
Cairo         : X11/Intrinsic.h : libxt-dev
```
- After adding the above system libraries, at least the following R packages can be installed with no problem:
```
  # install.packages("BiocManager")
  # BiocManager::install("apeglm")
  # BiocManager::install("XVector")
  # BiocManager::install("clusterProfiler")
  # BiocManager::install("AnnotationDbi")
  # BiocManager::install("dplyr")
  # BiocManager::install("Seurat")
  # BiocManager::install("ggpubr")
  # BiocManager::install("DropletUtils")
  # BiocManager::install("scRNAseq")
  # install.packages("remotes")
  # remotes::install_github('satijalab/seurat-wrappers')
  # BiocManager::install("DESeq2")
  # BiocManager::install("TMExplorer")
  # BiocManager::install("pheatmap")
  # BiocManager::install("scater")
  # install.packages("devtools")
  # devtools::install_github("navinlabcode/copykat")
  # BiocManager::install("SingleR")
  # BiocManager::install("paletteer")
  # BiocManager::install("org.Hs.eg.db")
```
- The updated Singularity file is (`rstudio_ood_4.2.2_0.6.def`):
```
  Bootstrap: docker
  From: rocker/rstudio:4.2.2

  %labels
  	Author Kai.Hu@umassmed.edu

  %environment
  	export PATH=/usr/lib/rstudio-server/bin:$PATH

  %runscript
  	mkdir /tmp/run; rserver "${@}"

  %post
  	apt-get update -q && apt-get install -q -y libz-dev
  	apt-get update -q && apt-get install -q -y libglpk-dev
  	apt-get update -q && apt-get install -q -y liblzma-dev
  	apt-get update -q && apt-get install -q -y libbz2-dev
          apt-get update -q && apt-get install -q -y libcairo2-dev
          apt-get update -q && apt-get install -q -y libxt-dev

  %test
  	rserver --help
```
- To obtain the updated image:
  - option1: build it from recipe file
  ```
    singularity build rstudio_ood_4.2.2_0.6.sif rstudio_ood_4.2.2_0.6.def
  ```
  - option2: pull a pre-built one from the Library Registry
  ```
    singularity pull --arch amd64 library://hukai916_singularity/r/rstudio_ood_4.2.2:0.6
  ```
  - option3 (UMMS users): there is copy in a shared place on SCI:
  ```
    /pi/mccb-umw/shared/singularity/rstudio_ood_4.2.2_0.6.sif
  ```
- Of note, no R package comes with the above image, you must install them afterwards.  
- Of note, for other R packages, some other system libraries are likely needed to be added.
- **UMMS users**, for the above list of R packages to be immediately available, add the following line to the top of your R script in your OOD RStudio:
```
  .libPaths(c("/pi/mccb-umw/shared/R_libs/R_4.2.2_ood", .libPaths()))
```

## Regarding the docker_sister folder
If you do not have access to a Linux machine with root access, but still would like to interactively test your image build, you can use the `docker_sister/Dockerfile` as a starting point. More info regarding this strategy, check out the PPT here: https://github.com/hukai916/UMMS/tree/main/Notes/singularity_demo

Hope you can save some debugging time by reading all above.
