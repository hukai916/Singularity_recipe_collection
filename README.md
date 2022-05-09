# A collection of Singularity recipe files

## How to build Singularity containers
### With MacBook:
Currently only support ```singularity build --remote```, therefore, need to follow the [Sylabs guides](https://sylabs.io/guides/3.3/user-guide/cloud_library.html), first [create an account](https://sylabs.io/guides/3.3/user-guide/cloud_library.html#make-an-account), then, [generate a token](https://sylabs.io/guides/3.3/user-guide/cloud_library.html#creating-a-access-token).

After that, verify token with ```singularity remote login```, then, ```singularity build --remote your_image_name.sif your_recipe_name.recipe```.

### With HPCC:
Will need ```root``` privilege, therefore, need to use ```singularity build --remote``` as well. Follow the same procedure as above. (```--fakeroot``` not seem to work)

### Collected recipes
#### rstudio_ood_4.2.0
A custom Singularity image containing R 4.2.0 to be used with UMass OOD: https://www.umassrc.org:444/
