# ccount

## First, download the ccount.sif by clicking the following link:
https://www.dropbox.com/scl/fi/k6b28kk7d66jt77ahobz6/ccount.sif?rlkey=cz7s0ar87nqpc87t8gbz88ix8&dl=0

## Then activate an interactive session:
```
singularity shell ccount.sif
```

## Dev notes:
1. When building using yml without dependency versions (not exported by `conda env export`), conda may take a lot of memory solving the env dependency tree, and Sylabs may fail due to exceeding memeory limit.
2. Even building with yml with versions, this recipe take more than 1 hour to build using Sylabs, also exceeding the 60min time limit per job.
3. When building with AWS EC2, request at least 8G mem and 20G storage. And better use RedHat since it can use rpm to install singularity easily.
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
sudo singularity build ccount.sig ccount.def
```
