#Useful spec commands:

spec.log for a given sample is saved in /nfs/chess/raw/[run cycle ID]/id3a/[userid-file ID]/[sample ID]

##Basic spec commands:

-	```ascan [motor] [start] [stop] [#steps] [count time]``` is an absolute position scan of ```[motor]```
-	```dscan [motor] [relative start] [relative stop] [#steps] [count time]``` is a relative position scan of ```[motor]```
-	```d2scan``` does a dscan of 2 motors simultaneously
-	```lup [motor]``` … is identical to dscan
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
##Attenuation:
-	```atten [thickness]``` inserts ```[thickness]``` mm of steel; range is 0-20 mm in 0.25 mm steps
-	watt returns the current attenuator thickness
##RAMS2 loading:
-	```mvr_screw [displacement] [block] [velocity] [acceleration]``` moves the screw head by ```[displacement]``` mm, at corresponding ```[velocity]``` and ```[acceleration]```. Almost always set ```[block]``` = 1 to have mvr_screw block the spec terminal until motion is complete. ```[displacement]``` > 0 for tension, < 0 for compression. ```[velocity]``` and ```[acceleration]``` should be given as absolute values.
