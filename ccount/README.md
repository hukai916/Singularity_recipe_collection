# ccount

**Note 1**: Other than the dependencies installed in the container, the `ccount` also relies on `ccount_utils/` and `pyimagesearch/`, which are included in the `workflow/` folder.

**Note 2**: When running the training module using HPC, `ccount` would execute LSF bsub commands to submit parallel jobs via Snakemake. Activating this container may overwrite the LSF commands, including `bsub`, which could cause Snakemake failures. For UMASS SCI users, the following command has been added into the `environment` variable in the image recipe, so it should just work. However, this solution is not guaranteed to work for all HPC systems as it depends on your HPC system setup, and you may need to consult with your HPC administrator for further assistance.
```
source /lsf/conf/profile.lsf
```

## First, download the ccount.sif by clicking the following link:
With tensorflow:
https://www.dropbox.com/scl/fi/1s8vmqvn1dcou1sf3xu47/ccount_cpu.sif?rlkey=kotv3keketvhjexd65o10dee9&dl=0

With tensorflow-gpu:
https://www.dropbox.com/s/sc5hh0lzpu3au04/ccount_gpu.sif?dl=0

## Then activate an interactive session:
```
singularity shell ccount_cpu.sif

# Or
singularity shell ccount_gpu.sif

# (May be required depending on your LSF setup, inside the container)
source /lsf/conf/profile.lsf
```

## Dev notes:
1. `environment_cpu.yml`: yaml used to build conda env on SCI with tensorflow
2. `environment_gpu.yml`: yaml used to build conda env on SCI with tensorflow-gpu
3. `environment_cpu_sci_work.yml`: yaml exported from the SCI env built with 1)
4. `environment_gpu_sci_work.yml`: yaml exported from the SCI env built with 2)
5. When building using yml without dependency versions (not exported by `conda env export`), conda may take a lot of memory solving the env dependency tree, and Sylabs may fail due to exceeding memeory limit.
6. Even building with yml with versions, this recipe take more than 1 hour to build using Sylabs, also exceeding the 60min time limit per job.
7. When building with AWS EC2, request at least 8G mem and 20G storage. And better use RedHat since it can use rpm to install singularity easily:
```
# AWS EC2 commands (RedHat)
sudo dnf update -y
sudo dnf install rpm-build cryptsetup gcc golang libseccomp-devel git make nano wget -y

export VERSION=3.8.7 && # adjust this as necessary 
wget https://github.com/hpcng/singularity/releases/download/v${VERSION}/singularity-${VERSION}.tar.gz && \
rpmbuild -tb singularity-${VERSION}.tar.gz && \
sudo rpm -ivh ~/rpmbuild/RPMS/x86_64/singularity-$VERSION-1.el9.x86_64.rpm && \
sudo rm -rf ~/rpmbuild singularity-$VERSION*.tar.gz

# build
sudo singularity build ccount.sif ccount.def
```