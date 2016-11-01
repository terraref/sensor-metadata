# Device and Sensor Fixed Metadata

This repository contains device and sensor fixed metadata for the TERRA-REF project used to populate the [Device and Sensor Information](https://terraref.ncsa.illinois.edu/clowder/collection/58035fa34f0c4a438cbb53dc) collection.

The initial revision is the raw sensor_fixed_metadata object provided by LemnaTec for each sensor formatted for use with the Clowder metadata.jsonld endpoint. Sensor datasets include: 

Sensors:
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

Calibration targets:
* [LabSphere](https://terraref.ncsa.illinois.edu/clowder/api/datasets/5817c7c84f0c63f2a8ca5e6c)
* [SphereOptics](https://terraref.ncsa.illinois.edu/clowder/api/datasets/5818bb544f0c63f2a8d20963)

## Adding Metadata
```
cat extractor_template.json | jq --argfile md $sensor/sensor_fixed_metadata.json '.content |= [$md]' > tmp.json

curl -H "Content-Type: application/json" --user $CLOWDER_USER:$CLOWDER_PASSWORD -X POST --data @tmp.json $(cat $sensor/dataset.id)/metadata.jsonld

rm tmp.json
``

