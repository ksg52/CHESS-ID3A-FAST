#Useful spec commands:

spec.log for a given sample is saved in /nfs/chess/raw/[run cycle ID]/id3a/[userid-file ID]/[sample ID]

##Basic spec commands:

-	```ascan [motor] [start] [stop] [#steps] [count time]``` is an absolute position scan of ```[motor]```
    - if aligning, use ```mbascan [motor] [start] [stop] [#steps] [count time]``` to automatically close garage door to protect far-field detectors
-	```dscan [motor] [relative start] [relative stop] [#steps] [count time]``` is a relative position scan of ```[motor]```
    - if aligning, use ```mbdscan [motor] [start] [stop] [#steps] [count time]``` to automatically close garage door to protect far-field detectors
-	```d2scan``` does a dscan of 2 motors simultaneously
    - if aligning, use ```mbd2scan [motor] [start] [stop] [#steps] [count time]``` to automatically close garage door to protect far-field detectors
-	```umv [motor] [position]``` is an absolute move of ```[motor]``` to ```[position]```
-	```umvr [motor] [relative position]``` is a relative move
-	```tw [motor] [step size]``` gives you a prompt to move ```[motor]``` by relative ```+/- [step size]```. Within the prompt hit enter to do the move, type ```+``` or ```–``` to change directions, type a new step size to change step sizes; type anything else to exit tweak mode
-	```wm [motor]``` reports ```[motor]```’s current position, and also displays [motor]’s limits
-	```we``` prints all motor positions
-	```ct [count time]``` counts for ```[count time]``` and displays the resulting ion chamber counts
-	To change variable plotted on the y-axis in yellow spec graph: ```plotselect [counter]``` , then ```splot```
-	```prdef [macro name]``` displays the definition of the macro, including the filename where it’s located
-	```qdo [macro name]``` recompiles a macro file
-	```newsample``` prompts you to define a new sample name

 
##Checking and setting the beam width and height:
-	```get_beam_width``` returns the width of the beam-defining slits (s0l, s0r) in mm
-	```get_beam_height``` returns the height of the beam-defining slits (s0t, s0b) in mm
-	```set_beam_width``` sets the width of the beam-defining slits
-	```set_beam_height``` sets the height of the beam-defining slits
-	similar macros ```get_guard_width```, etc exist to check or set the guard slits (s1t,b,l,r)

##Absorption foil utilities:
-	```insert_foil [1,2,3,4]``` inserts Sn (29.200 keV), Pr (41.991 keV), Tb (51.996 keV), Yb (61.332 keV)
-	```remove_foils``` removes all foils
-	```where_foils``` tells you what foil is currently in as well as the monochromator energy

##Fast shutter and garage door:
- ```opens``` opens the fast shutter
- ```closes``` closes the fast shutter
- ```open_garage_door``` opens the garage door (lead shield in front of the far-field detector)
- ```close_garage_door``` closes the garage door

##Attenuation:
-	```atten [thickness]``` inserts ```[thickness]``` mm of steel; range is 0-20 mm in 0.25 mm steps. NOTE: the fast shutter automatically closes when the attenuation is changed, so make sure to ```opens``` if you are e.g. watching the live view on the near-field camera or running line-up scans (```dscan```, etc)
-	```watt``` returns the current attenuator thickness

##RAMS2 loading:

**The RAMS2 operates in displacement control by default. Load control is possible, but should be used with care and only if you have consulted with beamline staff first.**
-	```mvr_screw [displacement] [block] [velocity] [acceleration]``` moves the screw head by ```[displacement]``` mm, at corresponding ```[velocity]``` and ```[acceleration]```. Almost always set ```[block]``` = 1 to have mvr_screw block the spec terminal until motion is complete. ```[displacement]``` > 0 for tension, < 0 for compression. ```[velocity]``` and ```[acceleration]``` should be given as absolute values.
-	```wm_screw``` returns the current screw position in mm
-	```wm_force``` returns the current load cell reading in N
-	```mv_force [force in N]``` switches from displacement control to load control and moves to the specified force (units: N). It will hold at this force until another ```mv_force``` command, or until ```rams_force_off```. **Note:** load control only works if the screw position is within a millimeter or so of 0 (the position it was at when the RAMS2 was last power cycled).
-	```rams_force_off``` exits force control and re-enters displacement control

##data acquisition: far-field
- ```sync_ff_on``` preps the far-field detector (Dexelas or GE) for data acquisition, moves the near-field detector out of the way, opens the garage door, and sets the attenuation to 8 mm steel (to prevent accidental damage to the far-field detector). **should be preceded by sync_nf_off** if the near-field detector has been in use.
- ```sync_ct [number of frames] [exposure time] [OPTIONAL: darkfield]``` opens the fast shutter, acquires ```[number of frames]``` frames each with exposure time ```[exposure time]``` (in s) on the far-field detector, then closes the fast shutter. If the optional flag ```darkfield``` is used, the garage door and fast shutter are closed before data acquisition, and the garage door is opened afterwards.
- ```slew_ome [ome start] [ome end] [number of frames] [exposure time] [OPTIONAL: darkfield]``` rotates RAMS2 ome from ```[ome start]``` to ```[ome end]```, acquiring ```[number of frames]``` with exposure time ```[exposure time]``` during rotation. The fast shutter is opened and closed at the beginning and end of data acquisition. If the optional flag ```darkfield``` is used, the garage door and fast shutter are closed before data acquisition, and the garage door is opened afterwards.
- ```sync_ff_off``` removes the far-field detector from data acquisition and closes the garage door.

##data acquisition: near-field
- ```sync_nf_on``` preps the near-field detector (Retiga or Manta) for data acquisition, moves the near-field detector into place, moves the near-field beamtop into place to block the direct beam, and sets the attenuation to 0 mm steel. **should be preceded by sync_ff_off** if the far-field detector has been in use.
- ```sync_tomo_on``` is identical to ```sync_nf_on```, except that it moves the near-field beamtop out of the field of view.
- ```slew_ome [ome start] [ome end] [number of frames] [exposure time] [OPTIONAL: darkfield]``` rotates RAMS2 ome from ```[ome start]``` to ```[ome end]```, acquiring ```[number of frames]``` with exposure time ```[exposure time]``` during rotation. The fast shutter is opened and closed at the beginning and end of data acquisition. If the optional flag ```darkfield``` is used, the garage door and fast shutter are closed before data acquisition, and the garage door is opened afterwards.
- ```nf_stream_on``` starts a live stream of 1 s exposures on the near-field detector. Images are not saved.
- ```nf_stream_off``` stops the live stream. **if nf_stream_on was used, nf_stream_off must be used prior to data acquisition** or images will not be saved.
- ```sync_nf_off``` removes the near-field detector from data acquisition.
