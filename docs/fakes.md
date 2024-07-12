All paths are relative to the project directory `/gscratch/dirac/DEEP` and it is assumed that `bin/setup.sh` was sourced.

# Fakes

Catalogs of fakes for the survey are available in:
- `data/fakes/DEEP/fakes_orbits.fits`: properties of the orbits of the fakes TNOs
- `data/fakes/DEEP/fakes_detections.fits`: realizations of detections of fake TNOs
- `data/fakes/DEEP/asteroidorbits.fts`: properties of the orbits of the fake MBAs
- `data/fakes/DEEP/fakeasteroids.fits`: realization of detections of fake MBAs
- `data/fakes/DEEP/fakes_detections_joined.fits`: `fakeasteroids.fits` and `fakes_detections.fits` joined into one catalog. A value of 10000000 is added to the ORBITID for the asteroids to diffentiate them from the TNOs in the joined catalog, in addition to adding a type column to the table schema.
- `data/fakes/DEEP/fakes_binaryproperties.fits`: properties of the binary TNOs injected

## Loading in Python

```python
>>> import astropy.table
>>> orbits = astropy.table.Table.read("data/fakes/DEEP/fakes_orbits.fits")
>>> print(len(orbits))
23849
>>> print(orbits.columns)
<TableColumns names=('ORBITID','a','e','i','Omega','omega','T_p','xv','N_IMAGES','m_VR','d','r','H_VR','AMP','PHASE','PERIOD')>

>>> detections = astropy.table.Table.read("data/fakes/DEEP/fakes_detections.fits")
>>> print(len(detections))
4726326
>>> print(detections.columns)
<TableColumns names=('RA','DEC','EXPNUM','CCDNUM','ORBITID','aei','mjd_mid','TDB','xv','H_VR','AMP','PERIOD','PHASE','d','observatory','r','MAG')>

>>> asteroids = astropy.table.Table.read("data/fakes/DEEP/fakeasteroids.fits")
>>> print(len(asteroids))
2521874
>>> print(asteroids.columns)
<TableColumns names=('RA','DEC','EXPNUM','CCDNUM','ORBITID','aei','mjd_mid','TDB','xv','H_VR','AMP','PERIOD','PHASE','observatory','d','r','MAG')>

>>> joined = astropy.table.Table.read("data/fakes/DEEP/fakes_detections_joined.fits")
>>> print(len(joined))
7248200
>>> print(joined.columns)
<TableColumns names=('RA','DEC','EXPNUM','CCDNUM','ORBITID','aei','mjd_mid','TDB','xv','H_VR','AMP','PERIOD','PHASE','d','observatory','r','MAG','type')>
>>> print(len(joined[joined['type'] == 'asteroid']))
2521874
>>> print(len(joined[joined['type'] == 'tno']))
4726326

>>> binaries = astropy.table.Table.read("data/fakes/DEEP/fakes_binaryproperties.fits")
>>> print(binaries.columns)
<TableColumns names=('ORBITID','DELTA_H','SEPARATION','ANGLE')>
>>> print(len(binaries))
1170
```

## Availability in the Butler

The joined fakes detection catalog is ingested in the Butler repository under the collection `DEEP/fakes` with the dataset type name `DEEP_fakes`.

```
$ butler query-datasets $REPO "DEEP_fakes" --collections DEEP/fakes

   type           run                         id                 
---------- ----------------- ------------------------------------
DEEP_fakes DEEP/fakes/ingest a3b39e72-d5d5-41ac-bd40-acd97b494694
```

When accessing the fakes in Python with the Butler, it is advised to convert the `lsst.afw.table.Catalog` object returned to an astropy table with `asAstropy()`:
```python
>>> import lsst.daf.butler as dafButler
>>> import os
>>> butler = dafButler.Butler(os.environ['REPO'])
>>> fakes = butler.get("DEEP_fakes", collections="DEEP/fakes").asAstropy()
```

