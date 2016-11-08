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

# Documenting Sensor Platforms

Currently, each sensor platform is represented as a space in Clowder (e.g., Maricopa Agricultural Center Field Scanner). To document a new sensor/platform:

* Login to Clowder (https://terraref.ncsa.illinois.edu/clowder/)
* Under the **Space** for your platform, create a collection calls "Device and Sensor Information", if it doesn't already exist
* Create a **Dataset** to represent the sensor. Name the dataset based on the short manufacturer name and sensor type (e.g., Headwall VNIR) and add a brief description of the dataset. For examples, see https://terraref.ncsa.illinois.edu/clowder/collection/58035fa34f0c4a438cbb53dc.
* Upload any associated datasheets, specifications, calibration certificates, or other related documentation to the dataset. 

## Adding sensor metadata
Clowder supports JSON-LD metadata for any Dataset. We've started using this feature to provide programmatic access to sensor metadata via the Clowder API. Examples are in the ```sensors``` directory in this repository.

Create a directory and ```fixed_sensor_metadata.json``` file and issue a pull-request to this repository.

The data in ```fixed_sensor_metadata.json``` will be inserted into the ```content``` object in the Clowder template below:
```
{
  "@context": "http://purl.org/terra/contexts/sensor.jsonld",
  "content" : {
  },
  "agent": {
      "@type": "cat:extractor",
      "extractor_id":"https://terraref.ncsa.illinois.edu/clowder/api/extractors/terra.sensor_fixed_metadata"
  }
}
```

The defacto standard for fixed sensor metadata is based on data provided by LemnaTec. This format will likely change going forward to leverage the OCG SWE set of vocabularies.

## JSON-LD Context

We are currently working on a JSON-LD context based in part on the OGC SWE and SensorML vocabularies. 

| Field name              | Description |  Type | 
| ----                    | ----         | ---- | 
| id                      | http://purl.org/dc/terms/identifier    | xsd:string |
| description             | http://purl.org/dc/terms/description   | xsd:string |  
| platform_name           | http://sensorml.com/ont/swe/property/PlatformName | xsd:string |
| manufacturer            | http://sensorml.com/ont/swe/property/ManufacturerName | xsd:string | 
| model_number            | http://sensorml.com/ont/swe/property/ModelNumber | xsd:string | 
| name                    | http://sensorml.com/ont/swe/property/ShortName | xsd:string | 
| serial_number           | http://sensorml.com/ont/swe/property/SerialNumber | xsd:string | 
| sensor_type             | http://sensorml.com/ont/swe/property/SensorType | xsd:string | 
| intended_application    | http://sensorml.com/ont/swe/property/IntendedApplication| xsd:string | 


## LemnaTec fixed sensor metadata fields:
The following fields are used in the LemnaTec fixed sensor metadata and will eventually be migrated to OGC SWE and TERRA fields outlined above:

| Field name              | Description | 
| ----                    | ----         |
| sensor manufacturer     | Manufacturer name |
| sensor product name     | Sensor product or model name | 
| sensor serial number    | Sensor serial number | 
| sensor description      | Sensor short description | 
| sensor purpose          | Short description of sensor purpose | 
| is calibrated           | Whether the sensor is calibrated |     
| calibration date        | Date of last calibration | 
| calibration certificate | URL to certificate file in Clowder or description of location |
| rsr                     | Relative spectral response object |   
| output data format      | Sensor output data format | 

Other fields are available, but generally specific to the LemnaTec Scanalyzer platform.  Examples can be found in the ```fixed_sensor_metadata.json``` files in the sensor subdirectories.
