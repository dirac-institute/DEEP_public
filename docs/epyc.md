# Epyc

## Accessing

JupyterHub: [https://epyc.astro.washington.edu/beta-jupyter/](https://epyc.astro.washington.edu/beta-jupyter/)

SSH: `ssh <netid>@epyc.astro.washington.edu`

## Set Up

Link to Epyc directories:

```
$ mkdir -p /epyc/users/$USER
$ ln -s /epyc/users/$USER $HOME/epyc_home
$ mkdir -p /epyc/ssd/users/$USER
$ ln -s /epyc/ssd/users/$USER $HOME/epyc_ssd
```

Download the DEEP_public tutorials:
```
$ cd $HOME/epyc_home
$ git clone https://github.com/dirac-institute/DEEP_public.git
```

Install the DEEP Jupyter kernel

```
$ bash /epyc/projects/DEEP/bin/install_kernel.sh
```

## Project Directory

The project directory is `/epyc/projects/DEEP`.

## Setting up your own space and repository

### On Epyc

**Note**: This method should only be used for accessing subsets of data around ~10-100GB. For larger data subsets, we should coordinate to make copies into the shared repository on Epyc.

You can create your own space and repository on Epyc by following these steps:
```
$ mkdir -p /epyc/users/$USER/my_repo # create a directory for your repository
$ cp /epyc/projects/DEEP/klone_repo/butler.yaml /epyc/users/$USER/my_repo/. # initialize the repository for access
```

Then, source the setup script in the project directory:
```
$ source /epyc/projects/DEEP/bin/setup.sh
```
and overwrite the `REPO` environment variable:
```
$ export REPO=/epyc/users/$USER/my_repo
```
Or if you are working from Python and using the DEEP Jupyter kernel, use this when instantiating your Butler:
```
>>> butler = dafButler.Butler(f"/epyc/users/{os.environ['USER']}/my_repo")
```

If there are data that you want to copy over from Klone and you know thier dataset refs use
```
butler.getURI(ref).path
```
to identify the fileystem path on Klone for your datasets of interest. And then copy these files to `/epyc/users/$USER/my_repo` on Epyc to make them available in your personal repository. You can use `scp` or `rsync` to do this.

If you want to copy an entire night of data out, you can copy the relevant directories from `/gscratch/dirac/DEEP/repo`. Note that all data products from a single night amount to more than 1TB, so care should be taken to select out only the datasets that are needed.


