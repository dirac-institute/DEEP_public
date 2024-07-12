
# DEEP

DEEP data/code/etc are stored in `/gscratch/dirac/DEEP`

To access the DEEP repository on Klone, do:
```
$ cd /gscratch/dirac/DEEP
$ source bin/setup.sh
loading config /mmfs1/home/stevengs/dirac/DEEP/etc/hyak.local/setup.sh
[opt_lsst] loading version w_2024_09 of the LSST Pipelines
loading config /gscratch/dirac/shared/opt/proc_lsst/etc/hyak.local/setup.sh
setting up klone compute node
```

Loads cluster-specific configurations set in `etc/`

Loads two other modules with a similar setup structure:
- `opt_lsst`: Located at `/gscratch/dirac/shared/opt/opt_lsst`; loads an installation of the LSST science pipelines
- `proc_lsst`: Located at `/gscratch/dirac/shared/opt/proc_lsst`; loads configurations for accessing butler repositorys/registries/pipeline processing code on difference clusters

Importantly, this sets up:
```
$ echo $REPO
$ echo $AWS_ACCESS_KEY_ID
$ echo $AWS_SECRET_ACCESS_KEY
$ echo $PGUSER
$ echo $PGPASSWORD
$ echo $PGHOST
$ echo $PGPORT
```

Install a jupyter kernel to run this setup when a jupyter kernel starts:
```
$ ./bin/install_kernel.sh
```

Run a jupyter server on your compute node
```
$ jupyter lab 1> $HOME/jupyter.log 2>&1 & disown
$ grep "token" $HOME/jupyter.log # wait for url to appear
```

and select "DEEP" for your kernel.

# DEEP Collection Structure

## Observing strategy

Figure 3/4 and Table 3 of https://arxiv.org/pdf/2310.19864

- Nights: e.g. 2019/04/02 == 20190402
- Target: e.g. A0c

## Exposure table

Queried from NOIRLab: `./data/deep_exposures_VR.csv`

## Accessing data via Butler

LSST Science Pipelines Docs: https://pipelines.lsst.io/

Collections are named `DEEP/<night>/<target>` e.g. `DEEP/20190402/A0a`

```python
>>> import lsst.daf.butler as dafButler
>>> import os
>>> import re
>>> import astropy.table

>>> butler = dafButler.Butler(os.environ['REPO'])

>>> print("\n".join(butler.registry.queryCollections(re.compile("DEEP/\d{8}/[AB][01][a-z]"))))
DEEP/20220821/B1a
DEEP/20220821/B1d
DEEP/20220821/B1h
DEEP/20220821/B1m
DEEP/20220822/B1a
DEEP/20220822/B1d
...

>>> len(list(butler.registry.queryDatasets(
    "differenceExp", 
    collections=butler.registry.queryCollections("DEEP/20190402/A0c")
)))
360

>>> len(list(butler.registry.queryDatasets(
    "differenceExp", 
    collections=butler.registry.queryCollections("DEEP/*/*")
)))
123467

>>> len(list(butler.registry.queryDatasets(
    "fakes_calexp", 
    collections=butler.registry.queryCollections("DEEP/*/*")
)))
480769

>>> len(list(butler.registry.queryDatasets(
    "raw", 
    collections=butler.registry.queryCollections(re.compile("DEEP/\d{8}/[AB][01][a-z]/raw"))
)))
519994
```

Relevant datasets are:
- raw: raw images
- calexp: calibrated images
- fakes_calexp: calibrated image with fakes
- differenceExp: difference image (with fakes)

