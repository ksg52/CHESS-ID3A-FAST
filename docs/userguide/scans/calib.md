# Guide to performing calibration scans
ID3A / FAST

## Ceria calibration
-	In tomo mode, find the ceria sample and position in the centre of the beam
-	Switch to ff mode
-	A good beam size is 0.25 mm x 0.25 mm
-	Umv ome 0
-	```sync_ct [number of frames] [exposure time in s]```  - check rings 
-	Typically do 2 frames, start with 3 s. If not enough signal at 0 attenuation, try longer. 
-	Typically get max ~10000 ADU/pixel in central rings. There should be no saturation.
-	```umv ome 180```
-	```sync_ct [same # of frames] [same exposure time]```
-	Take darks: ```sync_ct [same # of frames] [same exposure time] darkfield```

## Multi-ruby calibration
-	In tomo mode, get all 3 rubies into the beam on nf camera: find top of pedestal (embedded in the resin) and then move ramsz 0.525 mm down  
locating off the platen (better at higher energies): rubies are 1.035 mm above the platen
-	A good beam size is 0.6 mm tall, 3 mm wide
-	```Slew_ome 0 10 40 0.25``` or ```slew_ome 0 40 160 0.25``` to check whether attenuation is good – NB will not see rings on ff detector! Just isolated spots along the ring arcs. Bright spots in central rings will have some saturation. A bit of saturation per spot is okay.
-	```atten [attenuation length]``` - check [here](/userguide/pre_scan/atten_check/) for guide values for attenuation length
-	Then ```slew_ome 0 360 1440 0.25``` for the real deal

