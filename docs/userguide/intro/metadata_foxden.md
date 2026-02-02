FOXDEN is a collection of services intended to facilitate the capture, curation, and dissemination of sample and dataset metadata and provenance. The FOXDEN homepage can be found at [https://foxden.classe.cornell.edu:8344](https://foxden.classe.cornell.edu:8344).

Sample-level metadata records can be constructed and, if desired, injected directly into FOXDEN directly from SPEC during data collection. To set up FOXDEN record automation, in SPEC run ```foxden_setup```. In the menu that comes up, configure options as desired:

- Metadata service records ```on/off```: if on, will construct a sample-level metadata record when ```newsample``` is run from SPEC, and inject to FOXDEN or just save the record .json according to the setting chosen under "record submission location" below.
- SpecScans service records ```on/off```: if on, will construct a SPEC Scan Service record .json after each SPEC scan and submit to the FOXDEN SPEC Scan Service [https://foxden.classe.cornell.edu:8344/info/specscans](https://foxden.classe.cornell.edu:8344/info/specscans)
- Manual metadata input ```enable/disable```: if enabled, SPEC will prompt for metadata values during ```newuserid``` and ```newsample```. If disabled, ...
- Record submission location ```prod services/file only/dev services```:
    - if ```prod services```: records will be submitted to the production server [https://foxden.classe.cornell.edu:8344](https://foxden.classe.cornell.edu:8344)
    - if ```file only```: metadata .json will be created, but will not be injected into FOXDEN
    - if ```dev services```: records will be submitted to the development server [https://foxden-dev.classe.cornell.edu:8344](https://foxden-dev.classe.cornell.edu:8344)
 
FOXDEN records can also be created manually via webform at [https://foxden.classe.cornell.edu:8344/meta](https://foxden.classe.cornell.edu:8344/meta). In the dropdown menu labeled "Beamlines" on the left side of that page, choose a beamline schema (probably 3A if you're reading this guide!) to use the ID3A schema for a record describing sample-level data collected at the beamline, or choose "user" to define your own schema.

