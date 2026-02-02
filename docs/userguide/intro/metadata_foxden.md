FOXDEN is a collection of services intended to facilitate the capture, curation, and dissemination of sample and dataset metadata and provenance. The FOXDEN homepage can be found at [https://foxden.classe.cornell.edu:8344](https://foxden.classe.cornell.edu:8344).

Sample-level metadata records can be constructed and, if desired, injected directly into FOXDEN directly from SPEC during data collection. To set up FOXDEN record automation, in SPEC run ```foxden_setup```. In the menu that comes up, configure options as desired:

- Metadata service records ```on/off```: if on, will construct a sample-level metadata record when ```newsample``` is run from SPEC, and inject to FOXDEN or just save the record .json according to the setting chosen under "record submission location" below.
- SpecScans service records ```on/off```: if on, will construct a SPEC Scan Service record .json after each SPEC scan and submit to the FOXDEN SPEC Scan Service [https://foxden.classe.cornell.edu:8344/info/specscans](https://foxden.classe.cornell.edu:8344/info/specscans)
- Manual metadata input ```enable/disable```:
    - if enabled, SPEC will construct a sample-level metadata record according to how ```newsample``` is run:
      - if no arguments are used with ```newsample```, SPEC will prompt for metadata values interactively, and save the resulting composite .json in /nfs/chess/aux/cycles/[cycle ID]/id3a/[btr ID]/[sample ID]/[sample ID.json] 
      - if you wish to upload a .json file with pre-populated sample-level metadata, run ```newsample [sample name] [sample .json with full path]```, for example:
        ```newsample lshr-1 /nfs/chess/aux/cycles/2026-1/shanks-4838-a/example_sample_json.json```
        SPEC will combine the information in the specified .json file with BTR-level metadata specified during ```newuserid```, and save the resulting composite .json in /nfs/chess/aux/cycles/[cycle ID]/id3a/[btr ID]/[sample ID]/[sample ID.json] 
    - If disabled, an incomplete record .json will be created in /nfs/chess/aux/[cycle ID]/ID3A/[btr ID]/[sample ID]/metadata/[sample ID.json] containing only the raw data location.
- Record submission location ```prod services/file only/dev services```:
    - if ```prod services```: records will be submitted to the production server [https://foxden.classe.cornell.edu:8344](https://foxden.classe.cornell.edu:8344)
    - if ```file only```: metadata .json will be created, but will not be injected into FOXDEN
    - if ```dev services```: records will be submitted to the development server [https://foxden-dev.classe.cornell.edu:8344](https://foxden-dev.classe.cornell.edu:8344)
 
FOXDEN records can also be created manually via webform at [https://foxden.classe.cornell.edu:8344/meta](https://foxden.classe.cornell.edu:8344/meta). In the dropdown menu labeled "Beamlines" on the left side of that page, choose a beamline schema (probably 3A if you're reading this guide!) to use the ID3A schema for a record describing sample-level data collected at the beamline, or choose "user" to define your own schema.

