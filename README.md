# Development AssayClasses RESTful service

## Dev Service
This simple, standalone service is designed for quick changes to the assay class/rule chain information during development to allow for quick testing. The service two endpoints:
  - `/assayclasses?application_context=HUBMAP`
  - `/assayclasses/<assay-code>?application_context=HUBMAP`

## JSON backing file and releasing to UBKG
The [assayclasses.json file](https://github.com/x-atlas-consortia/dev-assay-class-api/blob/development/assayclasses.json) file is read directly by this service to form the responses. This file can be changed directly in GitHub (in the `development` branch only) to test changes to the associated rule chain/assay class information that normally is sourced from UBKG. To develop using this service and eventually release to UBKG follow this procedure:
- For each release cycle create a new `release` branch from `main`.
- On the new `release` branch, update the `assayclasses.json` file.
- From that `release` branch, create a PR to `development` and merge the changes into the `development` branch. Test locally or using the HuBMAP DEV infrastructure where this dev service is available at  `http://gateway.dev.hubmapconsortium.org:8181/assayclasses`.
- If there are new changes necessary, make those changes to `release` and create a new PR into `development`. Repeat testing.
- After a week of accumulating features on the `release` branch, create a PR into `main` from the `release`.
- Add Alan Simmons as a reviewer on the PR.
- Alan will add the changes to UBKG Neo4j. Zhou will release to the UBKG DEV instance and can be accessed at `https://ontology-api.dev.hubmapconsortium.org/`.
- Test the changes on the HuBMAP TEST infrastructure (TEST infrastructure is connected to UBKG DEV instance).
- After successful testing on TEST the changes to UBKG Neo4j will be released to PROD.


### Running the service locally
To run this service, in this directory:
  - copy instance/app.cfg.example to instance/app.cfg
  - create a python virtual environment with the contents of requirements.txt imported into the environment
  - source/activate the virtual environment
  - execute `python app.py`
The service will be available on port 8181.
See below for instructions to run with Docker


Both endpoints require the `application_context=HUBMAP` parameter.  (A future version will allow SENNET context as well, which will read results from a different file).

### Endpoints
The `/assayclasses` endpoint simply returns the contents of the [assayclasses.json file](https://github.com/x-atlas-consortia/dev-assay-class-api/blob/development/assayclasses.json) as a json response.

The `/assayclasses/<assay-code>` endpoint searches the same [assayclasses.json file](https://github.com/x-atlas-consortia/dev-assay-class-api/blob/development/assayclasses.json) for an assayclass item matching `rule_description.code` and returns the full matching assayclass item as a json response.  If the code is not found a 404 is returned. For example if `/assayclasses/C200001?application_context=HUBMAP` is called the return value is:

```
{
  "rule_description": {
    "application_context": "HUBMAP",
    "code": "C200150",
    "name": "non-DCWG primary IMC2D"
  },
  "value": {
    "active_status": "active",
    "assaytype": "IMC2D",
    "dataset_type": {
      "PDR_category": "MxNF",
      "dataset_type": "2D Imaging Mass Cytometry",
      "fig2": {
        "aggregated_assaytype": "LC-MS",
        "category": "bulk",
        "modality": "Proteomics"
      }
    },
    "description": "2D Imaging Mass Cytometry",
    "dir_schema": "imc-v0",
    "is_multiassay": false,
    "measurement_assay": {
      "codes": [
        {
          "code": "SENNET:C006901",
          "term": "Imaging Mass Cytometry Measurement Assay"
        },
        {
          "code": "HUBMAP:C006901",
          "term": "Imaging Mass Cytometry Measurement Assay"
        },
        {
          "code": "OBI:0003096",
          "term": "imaging mass cytometry assay"
        }
      ],
      "contains_full_genetic_sequences": false
    },
    "must_contain": [],
    "pipeline_shorthand": null,
    "process_state": "primary",
    "provider": "IEC",
    "tbl_schema": "imc-v",
    "vitessce_hints": []
  }
}
```

### Docker Deployment on DEV VM

First build a new docker image
```
docker-compose build
```

Then spin up the container
```
docker-compose up -d
```

Once the container is up running correctly, you can access at `http://gateway.dev.hubmapconsortium.org:8181/assayclasses`
