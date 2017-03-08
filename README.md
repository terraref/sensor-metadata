# Device and Sensor Fixed Metadata

This repository contains device and sensor fixed metadata for the TERRA-REF project used to populate the [Device and Sensor Information](https://terraref.ncsa.illinois.edu/clowder/collection/5873a6234f0cad7d81318e36) collection. Each sensor and calibration target is represented by a Clowder dataset containing documentation artifacts (e.g., datasheets, calibration certificates, specifications).  Metadata is attached to each dataset for programmatic access using the Clowder [metadata.jsonld](https://terraref.ncsa.illinois.edu/clowder/assets/docs/api/index.html#!/datasets/addMetadataJsonLD) endpoint.

The initial release [v0.1](https://github.com/terraref/sensor-metadata/releases/tag/v0.1) is simply the raw ```sensor_fixed_metadata``` object provided by LemnaTec for each sensor with corrections to minor mispellings. Subsequent revisions add the RSR data, improve field usage consistency across sensors, and possibly refactor to align with standards such as the OGC [SWE](http://www.opengeospatial.org/ogc/markets-technologies/swe) and [SensorML](http://www.sensorml.com/)

The following url provides a list of all datasets in this collection:
https://terraref.ncsa.illinois.edu/clowder/api/collections/5873a6234f0cad7d81318e36/getDatasets

This is in the "Maricopa Agricultural Center Field Scanner" space.

Below are links to Clowder datasets for each sensor and calibration target:

## Sensors

* [co2sensor](https://terraref.ncsa.illinois.edu/clowder/api/datasets/5873a9924f0cad7d8131b648/metadata.jsonld)
* [cropCircle](https://terraref.ncsa.illinois.edu/clowder/api/datasets/5873a7ed4f0cad7d8131a2e7/metadata.jsonld)
* [flirlrCamera](https://terraref.ncsa.illinois.edu/clowder/api/datasets/5873a7184f0cad7d8131994a/metadata.jsonld)
* [ndviSensor](https://terraref.ncsa.illinois.edu/clowder/api/datasets/5873a8f64f0cad7d8131af54/metadata.jsonld)
* [parSensor](https://terraref.ncsa.illinois.edu/clowder/api/datasets/5873a8ce4f0cad7d8131ad86/metadata.jsonld)
* [priSensor](https://terraref.ncsa.illinois.edu/clowder/api/datasets/5873a9174f0cad7d8131b09a/metadata.jsonld)
* [ps2](https://terraref.ncsa.illinois.edu/clowder/api/datasets/5873a84b4f0cad7d8131a73d/metadata.jsonld)
* [scanner3D](https://terraref.ncsa.illinois.edu/clowder/api/datasets/5873a7444f0cad7d81319b2b/metadata.jsonld)
* [spectrometer](https://terraref.ncsa.illinois.edu/clowder/api/datasets/5873a87a4f0cad7d8131a96a/metadata.jsonld)
* [stereo](https://terraref.ncsa.illinois.edu/clowder/api/datasets/5873a8ae4f0cad7d8131ac0e/metadata.jsonld)
* [SWIR](https://terraref.ncsa.illinois.edu/clowder/api/datasets/5873a79e4f0cad7d81319f5f/metadata.jsonld)
* [VNIR](https://terraref.ncsa.illinois.edu/clowder/api/datasets/5873a7bb4f0cad7d8131a0b7/metadata.jsonld)
* [weatherStation](https://terraref.ncsa.illinois.edu/clowder/api/datasets/5873a9724f0cad7d8131b4d3/metadata.jsonld)

## Calibration targets:

* [LabSphere](https://terraref.ncsa.illinois.edu/clowder/api/datasets/5873a8184f0cad7d8131a4dd)
* [SphereOptics](https://terraref.ncsa.illinois.edu/clowder/api/datasets/5873a9434f0cad7d8131b29a)

## Adding Metadata
After updating metadata in this repository, it can be POSTed to the associated dataset using the following steps:

```sh
cat extractor_template.json | jq --argfile md $sensor/sensor_fixed_metadata.json '.content |= $md' > tmp.json

curl -H "Content-Type: application/json" --user $CLOWDER_USER:$CLOWDER_PASSWORD -X POST --data @tmp.json $(cat $sensor/dataset.id)/metadata.jsonld

rm tmp.json
```

## Accessing metadata in R

Sensor metadata can easily be used in R. See the [sensor metadata tutorial for more examples](https://github.com/terraref/tutorials/blob/master/sensors/02-sensor-metadata.Rmd).

For example, plotting the RSR curve for the NDVI sensor:

```r
# install.packages('RJSONIO')
library(RJSONIO)
# d <- fromJSON("https://terraref.ncsa.illinois.edu/clowder/api/datasets/{id}/metadata.jsonld")
ndvi_metadata <- fromJSON("https://terraref.ncsa.illinois.edu/clowder/api/datasets/5873a8f64f0cad7d8131af54/metadata.jsonld")
ndvi_rsr<- ndvi_metadata[[1]]$content$rsr 
plot(ndvi_rsr$up$ch1 ~ ndvi_rsr$up$wavelength, type="l", col= "blue", xlab="Wavelength [nm]", ylab="Response")
lines(ndvi_rsr$up$ch4 ~ ndvi_rsr$up$wavelength, type="l", col= "red")
```

# Documenting Sensor Platforms

Currently, each sensor platform is represented as a space in Clowder (e.g., Maricopa Agricultural Center Field Scanner). To document a new sensor/platform:

* Login to Clowder (https://terraref.ncsa.illinois.edu/clowder/)
* Under the **Space** for your platform, create a collection calls "Device and Sensor Information", if it doesn't already exist
* Create a **Dataset** to represent the sensor. Name the dataset based on the short manufacturer name and sensor type (e.g., Headwall VNIR) and add a brief description of the dataset. For examples, see https://terraref.ncsa.illinois.edu/clowder/collection/5873a6234f0cad7d81318e36.
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

**Note**: The JSON-LD (sensor context)[contexts/sensor.jsonld) is a work in progress. This is an attempt to model the LemnaTec sensor fixed metadata (see below) using OCG standard vocabularies from SWE and SensorML. Please feel free to provide feedback/input via Github issues.

| Field name              | Definition |  Type | 
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
| band_number| http://sensorml.com/ont/swe/property/BandNumber | xsd:string|
| banwidth | http://sensorml.com/ont/swe/property/Bandwidth | xsd:string|
| wavelength| http://sensorml.com/ont/swe/property/Wavelength | xsd:string | 
| gantry_location| (LemnaTec) location in gantry (e.g., camerabox) |xsd:string|
| gantry_orientation| (LemnaTec) orientation in gantry (e.g., facing down) | xsd:string|
| camerabox_location| (LemnaTec) location in camerabox - object | Object | 
| x |  X position| xsd:float| 
| y |  Y position| xsd:float| 
| z |  Z position| xsd:float|
| components | List of sensor components | Array | 
| component_type | Component type | xsd:string |
| properties | Property/value pairs | Object |
| aperture | Camera aperture | xsd:float | 
| focal_length | Camera focal length| xsd:float |
| max_scan_resolution_width| Scanner maximum scan resolution width | xsd:int| 
| output_format | Output data format| xsd:string |
| calibration_information | Sensor calibration information | Object |
| date | Date | xsd:date |
| calibrated | Whether the sensor has been calibrated| xsd:boolean |
| certificate | URL to certificate file | xsd:string |
| note | Note field | xsd:string |


## LemnaTec fixed sensor metadata fields:
The following fields are used in the LemnaTec fixed sensor metadata and will eventually be migrated to OGC SWE and TERRA fields outlined above:

| Field name              | Definition | 
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

### JSON-LD Example
Below is 
```json
{
    "@context": {
      "terra" : "http://purl.org/terra/sensor#",
      "dc": "http://purl.org/dc/terms/",
      "swe" : "http://sensorml.com/ont/swe/",
      "xsd": "http://www.w3.org/2001/XMLSchema#",
          "id" : {
            "@id": "dc:identifier",
            "@type": "xsd:string"
          },
          "description" : {
            "@id": "dc:description",
            "@type": "xsd:string"
          },
          "platform_name" : {
              "@id": "swe:PlatformName",
              "@type": "xsd:string"
          },
          "manufacturer" : {
              "@id": "swe:Manufacturer",
              "@type": "xsd:string"
          },
          "model_number" : {
              "@id": "swe:ModelNumber",
              "@type": "xsd:string"
          },
          "name" : {
              "@id": "swe:ShortName",
              "@type": "xsd:string"
          },
          "serial_number" : {
              "@id": "swe:SerialNumber",
              "@type": "xsd:string"
          },
          "sensor_type" : {
              "@id": "swe:SensorType",
              "@type": "xsd:string"
          },
          "intended_application" : {
              "@id": "swe:IntendedApplication",
              "@type": "xsd:string"
          },
          "band_number" : {
              "@id": "swe:BandNumber",
              "@type": "xsd:string"
          },
          "bandwidth" : {
              "@id": "swe:Bandwidth",
              "@type": "xsd:string"
          },
          "wavelength" : {
              "@id": "swe:Wavelength",
              "@type": "xsd:string"
          },
          "gantry_location" : {
            "@id": "terra:GantryLocation",
            "@type": "xsd:string"
          },
          "gantry_orientation" : {
            "@id": "terra:GantryOrientation",
            "@type": "xsd:string"
          },
          "camerabox_location" : {
            "@id": "terra:CameraboxLocation",
            "@type": "xsd:string"
          },
          "x" : {
              "@id": "terra:x",
              "@type": "xsd:float"
          },
          "y" : {
              "@id": "terra:y",
              "@type": "xsd:float"
          },
          "z" : {
              "@id": "terra:z",
              "@type": "xsd:float"
          },
          "components" : {
             "@id": "terra:Components",
             "@container": "@set"
          },
          "component"  : {
             "@id" : "terra:Component",
             "@type" : "xsd:string"
          },
          "component_type" : {
             "@id" : "terra:ComponentType",
             "@type" : "xsd:string"
          },
          "properties" : {
             "@id": "terra:Properties",
             "@container": "@set"
{
    "@context": {
      "terra" : "http://purl.org/terra/sensor#",
      "dc": "http://purl.org/dc/terms/",
      "swe" : "http://sensorml.com/ont/swe/",
      "xsd": "http://www.w3.org/2001/XMLSchema#",
          "id" : {
            "@id": "dc:identifier",
            "@type": "xsd:string"
          },
          "description" : {
            "@id": "dc:description",
            "@type": "xsd:string"
          },
          "platform_name" : {
              "@id": "swe:PlatformName",
              "@type": "xsd:string"
          },
          "manufacturer" : {
              "@id": "swe:Manufacturer",
              "@type": "xsd:string"
          },
          "model_number" : {
              "@id": "swe:ModelNumber",
              "@type": "xsd:string"
          },
          "name" : {
              "@id": "swe:ShortName",
              "@type": "xsd:string"
          },
          "serial_number" : {
              "@id": "swe:SerialNumber",
              "@type": "xsd:string"
          },
          "sensor_type" : {
              "@id": "swe:SensorType",
              "@type": "xsd:string"
          },
          "intended_application" : {
              "@id": "swe:IntendedApplication",
              "@type": "xsd:string"
          },
          "band_number" : {
              "@id": "swe:BandNumber",
              "@type": "xsd:string"
          },
          "bandwidth" : {
              "@id": "swe:Bandwidth",
              "@type": "xsd:string"
          },
          "wavelength" : {
              "@id": "swe:Wavelength",
              "@type": "xsd:string"
          },
          "gantry_location" : {
            "@id": "terra:GantryLocation",
            "@type": "xsd:string"
          },
          "gantry_orientation" : {
            "@id": "terra:GantryOrientation",
            "@type": "xsd:string"
          },
          "camerabox_location" : {
            "@id": "terra:CameraboxLocation",
            "@type": "xsd:string"
          },
          "x" : {
              "@id": "terra:x",
              "@type": "xsd:float"
          },
          "y" : {
              "@id": "terra:y",
              "@type": "xsd:float"
          },
          "z" : {
              "@id": "terra:z",
              "@type": "xsd:float"
          },
          "components" : {
             "@id": "terra:Components",
             "@container": "@set"
          },
          "component"  : {
             "@id" : "terra:Component",
             "@type" : "xsd:string"
          },
          "component_type" : {
             "@id" : "terra:ComponentType",
             "@type" : "xsd:string"
          },
          "properties" : {
             "@id": "terra:Properties",
             "@container": "@set"
          },
          "aperture" : {
             "@id" : "terra:Aperture",
             "@type" : "xsd:float"
          },
          "focal_length" : {
             "@id" : "terra:FocalLength",
             "@type" : "xsd:float"
          },
          "max_scan_resolution_width" : {
             "@id" : "terra:MaxScanResolutionWidth",
             "@type" : "xsd:int"
          },
          "output_format" : {
             "@id" : "terra:OutputFormat",
             "@type" : "xsd:string"
          },
          "calibration_info" : {
             "@id": "terra:CalibrationInformation",
             "@container": "@set"
          },
          "calibrated" : {
            "@id": "terra:Calibrated",
            "@type" : "xsd:boolean"
          },
          "certificate" : {
            "@id": "terra:CalibrationCertificate",
            "@type" : "xsd:string"
          },      
          "note" : {
            "@id": "terra:Note",
            "@type" : "xsd:string"
          }
     },
    "id" : "swir camera box",
    "name" : "SWIR",
    "manufacturer": "Headwall Scientific",
    "model_number": "SWIR",
    "serial_number": "G4-383",
    "specification" : "https://terraref.ncsa.illinois.edu/clowder/files/58178df74f0ce77b665606e3",
    "description": "hyperspectral camera to measure shortwave infrared (SWIR) radiation",
    "intended_application": "measures spectral reflectance from 950nm to 2500nm",
    "gantry_location" : "camerabox",
    "gantry_orientation" : "facing down",
    "camerabox_location" : {
      "x" : "0.877",
      "y" : "2.325",
      "z" : "0.635"
    },
    "components" : [{
      "component_type" : "optics",
      "manufacturer" : "Stingray optics",
      "name" : "SR-1200-030 F/1.3 0.48-2.5um",
      "properties" : {
        "aperture" : 2.0,
        "focal_length" : 25
      }
    }],
    "calibration_info" : {
      "calibrated" : true,
      "date" : "2015-09-21",
      "certificate" : "https://terraref.ncsa.illinois.edu/clowder/files/58178df74f0ce77b665606ed",
      "note" : "todo, calibration white/dark reference need to be done constantly during measurements"
    },
    "output_format" : "hypercube with preview image and additional information provided by sensor",
    "properties" : {
      "max_scan_resolution_width" : 384,
      "field_of_view": {
        "y" : 0.75
      }
    }  
}
```
