---
title: 3D printing timelapse
date: 2025-2-13 19:00:00
categories: [Projects]
tags: [3d printing, photography, timelapse] #use lowercase, comma separated
#file has to be named yyyy-mm-dd-<post name>.md in order to show up
---
# How to take a timelapse of your 3D print

## Always try to combine different hobbies
3D prints (whether FDM filament squeezed out, or SLA where resin is cured by light) are constructed vertically, layer by layer. Naturally this suggests the idea of a timelapse movie to see the printing process sped up and the object magically grow before our eyes. For resin prints that's pretty easy because the print is only moving up in the Z direction as new layers are added. But if you stick a camera in front of your "bed-slinger" FDM printer (like the Prusa MK3S+ I have) you will observe a chaotic mess, because the printed object and print head mechanism will most likely be in a different position when each frame is taken. To solve this problem, we need to control the camera, synchronize the shutter with each layer, and get the bed and growing print in the same location for each frame.

## Getting in the same position at each layer
As usual, I looked around the internet and copied from various places, but implemented the ideas myself. G-code is the language that's used to tell the printer head where to move and what to do. That's what the slicer outputs to the printer. In PrusaSlicer, you can easily insert G-code to be run after every Z layer (go to Printers, Custom G-code, After layer change G-code). We can use this to move the bed and printer head to the same spot after every layer is completed. Here's the code I used, modified from [this video](https://youtu.be/NgZZzi6sRDU?si=FEdzyVIKoghU__l3):

	;AFTER_LAYER_CHANGE
	;[layer_z]
	G1 E-7 F2000 ;retract filament (retract -7mm at 2000mm/min)
	G1 X251 Y200 F10000 ;Move bed forward and press the switches

	G4 SO ;Wait for move to finish 

	G4 P200 ;wait .2 seconds

	G1 E6.9 F1600 ;extrude filament (extrude 6.9mm at 1600mm/min)

This additional move adds very little time to the total print but gives a good result. It also doesn't cause any issues in the print quality that I can discern.


## Controlling the shutter mechanically - make the printer do it for you
I believe this could be done via software if you're using Octoprint or some other printer controller, but I haven't delved into that at all. This will be very much a physical solution based on what I already know how to do. I had already used various remote control methods to focus and trigger the shutter on my Sony a6000. It's been a while since I wired the connector but the pinout for the proprietary Sony multiport connector is out there, and it's just a matter of connecting one or the other pin to ground. For other brands, shutter triggering is done by sensing resistance, so that's slightly more involved, but still doable. Anyway, Sony is easy to control by simply closing a switch, or closing a relay via an Arduino (which I have done extensively for synchronizing timelapse shooting with lights and motor movements). We can do either one here, but first we'll make the printer push a switch. I found that there's a rather unimportant and easy to remove piece on top of the printer head that could be modified to hold a switch ([here](https://www.printables.com/model/57217-i3-mk3s-printable-parts/files#preview.file.p5s37)). Luckily there are already .stl files of all these parts shared by Prusa, so modifying it to hold a switch or two was easy. If the switches are put in the back they don't interfere with normal movement, and can be pushed by moving the print head all the way to the right. I tweaked the modified piece until it was perfect and then hot glued two switches in place. The wires can be routed along the normal cable from the printer head as well, so they don't cause any trouble.
![Switch holder part](/myfiles/switch_holder.png)
![Switch holder in place](/myfiles/switch_holder_in_place.png)
![Switch holder in place](/myfiles/switch_holder_in_place2.png)

## Performing the timelapse
As I said, the switch could directly control the shutter and that would be fine, but we could also detect the switch activation with an Arduino and then do whatever we want with that signal. I'm using an Arduino Uno with a 4 relay shield on it. I decided to throw some programmable RGB LED lights on there and have them change color at every layer for a little extra *something*. For these shots I also used flash for additional fill lighting -- that was just controlled by the camera directly. More extensive lighting could be set up and turned on and off for each layer as well, via the relays/Arduino. The very simple Arduino sketch is found [here](https://github.com/jpickard2/random_arduino_sketches/blob/05148de64ba69efdf2d728f33689e0e82a9241cc/3d_printing_timelapse.ino).


## Assembling the timelapse movie
Finally, the single shots need to be assembled into a movie. This is easily done in Premiere Pro or other programs that are specific for that purpose. I could perhaps have chosen a more interesting print to film (or turned the cool part towards the camera!) but whatever. It's just a proof of concept.
![Finished timelapse](/myfiles/3dprint_timelapse.gif)


