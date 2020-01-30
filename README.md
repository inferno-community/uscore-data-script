# uscore-data-script

Creates a minimal set of complete, realistic synthetic patients for FHIR US Core v3.1.0
by generating a population of patients in Synthea and finding a small representive set that
contain all required data elements.  This small set of patients is ideal for testing
because it is suitable for demonstrating support for all MUST SUPPORT data elements without requiring a large set of patients or sacrificing realism.

## Install

This uscore-data-script requires working installations of Ruby and Java. After cloning this repository, run:

```
cd <path>/uscore-data-script
bundle install
```

## Creating the Data Set

1. Run uscore-data-script

The uscore-data-script will execute Synthea to generate synthetic patients, load the resulting
FHIR Bundles, and select certain patients based on criteria described below.

```
bundle exec ruby uscore-data-script.rb [mrburns]
```

There is an optional `mrburns` parameter, which if included, will generate a single longitudinal
patient who possesses at least one resource conforming to every US Core profile. In other words,
one patient with everything. If the `mrburns` parameter is not included, the script will generate
a small collection of testing patients.

2. Use Data

Use the data files located in `/output/data`.  It includes 

## What uscore-data-script does

This data script searches through patient data generated by [Synthea](https://github.com/synthetichealth/synthea)
and attempts to pick the minimum number of patients satisfying these criteria:

- At least one male, at least one female
- One white, one black, one Hispanic
- One child, one adult, one elderly
- One must be a smoker
- One must have an allergy
- The child must have immunizations
- The child must not be a smoker
- The elderly patient must have an implantable device
- All patients together must satisfy all [US Core Implementation Guide](http://hl7.org/fhir/us/core/STU3.1/) profiles

After selecting patients, the data script then makes the following modifications:

- Create [vitalspanel](http://hl7.org/fhir/R4/vitalspanel.html) observations where appropriate.
- If no smoker was found, make one of the patients a smoker.
- Pick one patient, select a condition and replace the category with [data absent reason unknown](http://hl7.org/fhir/us/core/STU3.1/general-guidance.html#missing-data).
- Pick one patient, select the patient name, and replace name data with [data absent reason unknown](http://hl7.org/fhir/us/core/STU3.1/general-guidance.html#missing-data).
- If no stand-alone Medication was found (i.e. all the medications were RxNorm codes) then pick a patient, and modify a MedicationRequest to have a reference to a stand-alone Medication.
- Remove all Claims and Explanation of Benefits.
- Update each Patient Provenance references.

Finally, the data script runs the FHIR Validator on each Bundle and writes the results to `./output/validation`


# License

Copyright 2020 The MITRE Corporation

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
