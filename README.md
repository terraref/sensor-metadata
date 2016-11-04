# Device and Sensor Fixed Metadata

This repository contains device and sensor fixed metadata for the TERRA-REF project used to populate the [Device and Sensor Information](https://terraref.ncsa.illinois.edu/clowder/collection/58035fa34f0c4a438cbb53dc) collection. Each sensor and calibration target is represented by a Clowder dataset containing documentation artifacts (e.g., datasheets, calibration certificates, specifications).  Metadata is attached to each dataset for programmatic access using the Clowder [metadata.jsonld](https://terraref.ncsa.illinois.edu/clowder/assets/docs/api/index.html#!/datasets/addMetadataJsonLD) endpoint.

The initial release [v0.1](https://github.com/terraref/sensor-metadata/releases/tag/v0.1) is simply the raw ```sensor_fixed_metadata``` object provided by LemnaTec for each sensor with corrections to minor mispellings. Subsequent revisions add the RSR data, improve field usage consistency across sensors, and possibly refactor to align with standards such as the OGC [SWE](http://www.opengeospatial.org/ogc/markets-technologies/swe) and [SensorML](http://www.sensorml.com/)

The following url provides a list of all datasets in this collection:
https://terraref.ncsa.illinois.edu/clowder/api/collections/58035fa34f0c4a438cbb53dc/getDatasets

This is in the "Maricopa Agricultural Center Field Scanner" space.

Below are links to Clowder datasets for each sensor and calibration target:

## Sensors

* [co2sensor](https://terraref.ncsa.illinois.edu/clowder/datasets/581787d94f0ce77b6655b819)
* [cropCircle](https://terraref.ncsa.illinois.edu/clowder/datasets/580361164f0c4a438cbb546c)
* [flirlrCamera](https://terraref.ncsa.illinois.edu/clowder/datasets/5817877a4f0ce77b6655b320)
* [ndviSensor](https://terraref.ncsa.illinois.edu/clowder/datasets/581787524f0ce77b6655b2c7)
* [parSensor](https://terraref.ncsa.illinois.edu/clowder/datasets/581788344f0ce77b6655bf23)
* [priSensor](https://terraref.ncsa.illinois.edu/clowder/datasets/581789524f0ce77b6655ccf9)
* [ps2](https://terraref.ncsa.illinois.edu/clowder/datasets/581789834f0ce77b6655cee4)
* [scanner3D](https://terraref.ncsa.illinois.edu/clowder/datasets/581789af4f0ce77b6655d094)
* [spectrometer](https://terraref.ncsa.illinois.edu/clowder/datasets/581789ca4f0ce77b6655d0f5)
* [stereo](https://terraref.ncsa.illinois.edu/clowder/datasets/5817873d4f0ce77b6655b269)
* [SWIR](https://terraref.ncsa.illinois.edu/clowder/datasets/5817870c4f0ce77b6655aecd)
* [VNIR](https://terraref.ncsa.illinois.edu/clowder/datasets/581787264f0ce77b6655b125)
* [weatherStation](https://terraref.ncsa.illinois.edu/clowder/datasets/58178a744f0ce77b6655d38a)

## Calibration targets:

* [LabSphere](https://terraref.ncsa.illinois.edu/clowder/api/datasets/5817c7c84f0c63f2a8ca5e6c)
* [SphereOptics](https://terraref.ncsa.illinois.edu/clowder/api/datasets/5818bb544f0c63f2a8d20963)

## Adding Metadata
After updating metadata in this repository, it can be POSTed to the associated dataset using the following steps:

```sh
cat extractor_template.json | jq --argfile md $sensor/sensor_fixed_metadata.json '.content |= $md' > tmp.json

curl -H "Content-Type: application/json" --user $CLOWDER_USER:$CLOWDER_PASSWORD -X POST --data @tmp.json $(cat $sensor/dataset.id)/metadata.jsonld

rm tmp.json
```

## Accessing metadata in R

Sensor metadata can easily be used in R. For example, plotting the RSR curve for the NDVI sensor:

```r
# install.packages('RJSONIO')
library(RJSONIO)
# d <- fromJSON("https://terraref.ncsa.illinois.edu/clowder/api/datasets/{id}/metadata.jsonld")
ndvi_metadata <- fromJSON("https://terraref.ncsa.illinois.edu/clowder/api/datasets/581787524f0ce77b6655b2c7/metadata.jsonld")
ndvi_rsr<- ndvi_metadata[[1]]$content$rsr 
plot(rsr$up$ch1 ~ rsr$up$wavelength, type="l", col= "blue", xlab="Wavelength [nm]", ylab="Response")
lines(rsr$up$ch4 ~ rsr$up$wavelength, type="l", col= "red")
```
