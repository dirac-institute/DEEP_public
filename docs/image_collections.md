# Image Collections

Image collections are tables of metadata about difference images that exist in a Butler repository. The image collection contains information such as: exposure time, zero point, and seeing.

Image collections are stored in `/gscratch/dirac/DEEP/collab/image_collections`. An image collection exists for each night/target pair observed in the survey:
```
$ ls /gscratch/dirac/DEEP/collab/image_collections | grep "collection" | head
20190402_A0a.collection
20190402_A0b.collection
20190402_A0c.collection
20190403_A0a.collection
20190403_A0b.collection
20190403_A0c.collection
20190504_A0a.collection
20190504_A0b.collection
20190504_A0c.collection
20190504_A1a.collection
```

Each image collection can be read as an Astropy table:
```
>>> import astropy.table
>>> collection = astropy.table.Table.read("/gscratch/dirac/DEEP/collab/image_collections/20190402_A0a.collection", format="ascii.ecsv")
>>> collection
<Table length=360>
               dataId                std_idx ext_idx ...  night   fieldname
               str36                  int64   int64  ...   str8      str3  
------------------------------------ ------- ------- ... -------- ---------
e885caff-5870-4520-9032-bd1334f282cd     148       0 ... 20190402       A0a
ec50a903-bc22-40a7-8680-1c938fbcf5a7     304       0 ... 20190402       A0a
                                 ...     ...     ... ...      ...       ...
a88f94c7-719e-4a59-ad3d-b80adaa21718     120       0 ... 20190402       A0a
16fa831f-cad1-4ba1-99e6-6ad503f00319       0       0 ... 20190402       A0a
>>> print(collection.columns)
<TableColumns names=('dataId','std_idx','ext_idx','std_name','collection','datasetType','visit','detector','band','filter','exposureTime','mjd_start','mjd','object','pointing_ra','pointing_dec','airmass','ra','dec','ra_tl','dec_tl','ra_tr','dec_tr','ra_br','dec_br','ra_bl','dec_bl','OBSID','DTNSANAM','AIRMASS','DIMM2SEE','GAINA','GAINB','psfSigma','psfArea','nPsfStar','zeroPoint','skyBg','skyNoise','meanVar','astromOffsetMean','astromOffsetStd','config','wcs','night','fieldname')>
>>> print(collection['psfSigma'])
     psfSigma     
------------------
 2.275264312619666
2.2812283648167875
               ...
2.5977503077525252
2.6718396363490275
2.5950174167570634
Length = 360 rows
>>> print(collection['zeroPoint'])
    zeroPoint     
------------------
31.215071546061957
31.221874587010277
               ...
31.171059103522836
31.193345782110736
31.166799151390254
Length = 360 rows
```

## Columns

Potentially useful columns are:
- `visit`: the exposure number.
- `detector`: the CCD number.
- `zeroPoint`: zero points fit during photometric calibration with respect to the Pan-STARRS catalog.
- `psfSigma`: The standard deviation of a 2D Gaussian PSF model fit to bright stars on the detector in pixels. The FWHM in arcseconds is obtained via `2.355 * 0.2637 * psfSigma` where `0.2637` is a fiducial DECam pixel scale and `2.355` converts a standard deviation into FWHM for a Guassian.

## Joined collection

All collections are joined into one in the file `/gscratch/dirac/DEEP/collab/image_collections/joined.collection`

## Sharded collections

Image collections are sharded on a per-target/night/detector basis in the directory `/gscratch/dirac/DEEP/collab/image_collections/sharded` with the format: `<target>/<night>/<night>_<target>_<detector>.collection`. For example, `/gscratch/dirac/DEEP/collab/image_collections/sharded/A0a/20190402/20190402_A0a_001.collection` contains metadata for detector `1` from all exposures on night `20190402` and target `A0a`.
