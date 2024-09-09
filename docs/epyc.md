# Epyc

## Accessing

JupyterHub: [https://epyc.astro.washington.edu/beta-jupyter/](https://epyc.astro.washington.edu/beta-jupyter/)

SSH: `ssh <netid>@epyc.astro.washington.edu`

## Set Up

Link to Epyc directories:

```
$ ln -s /epyc/users/$USER $HOME/epyc_home
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
