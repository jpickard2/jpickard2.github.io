---
title: 3D printed protein structures
date: 2025-2-6 19:00:00
categories: [Projects]
tags: [3d printing, proteins, biology] #use lowercase, comma separated
#file has to be named yyyy-mm-dd-<post name>.md in order to show up
---
# How to 3D print protein structures

## What other public data would be fun to 3D print?

For anyone unaware, proteins do pretty much everything that makes life possible. DNA encodes the instructions for proteins, which are essentially linear chains of amino acids. There are about 20 amino acids and each has slightly different properties, so these can be put together in practically infinite combinations, and form proteins with lengths of just a few to thousands of amino acids. These strings then fold up into their final structures based on their chemical properties. The finished proteins can have many different functions. Most famously, they can be enzymes that speed up all kinds of chemical reactions essential to life. But they also do all kinds of other tasks, like moving things around the body, into and out of cells, and even around inside cells. All our senses come down to proteins changing shape in response to a specific signal. The physical integrity of our skin and muscles is thanks to long chains of proteins holding them together and contracting when needed. I could go on... but in short, everything that's keeping you alive right now is being done by proteins. Individual proteins are generally pretty small, too small for a light microscope or even electron microscope to see. And although we may know the primary sequence of the amino acids that make up a given protein (this can be read from the DNA sequence), until recently we couldn't predict the complex final form very well. This is important, because knowing the shape of a protein can tell you a lot about its function. There's just no substitute for looking at something when you want to understand it. So techniques like X-ray crystallography were invented, and people have obtained the structures of many, many proteins over the decades. These are conveniently collected in a public database, the [RSCB Protein Data Bank](https://www.rcsb.org/pages/about-us/index). This seemed like an ideal source of interesting shapes to try printing. 

## Getting the structure file and converting to .stl
First, we need to pick an interesting protein. There are a lot to choose from. I selected a type of sugar-binding protein that plants produce called a lectin, specifically [UEA-I](https://www.rcsb.org/structure/1JXN) from *Ulex europaeus* (European gorse). UEA-I lectin is used as a tool to fluorescently label cells. Here is an example from mouse small intestine that I happen to have.
![UEA-I staining in the mouse small intestine](/myfiles/uea-1_staining.png)

I downloaded the structure in PDBx/mmCIF format. To look at the structure and convert it to .stl format, I opened it in this free viewer program from UCSF called [ChimeraX](https://www.rbvi.ucsf.edu/chimerax/). I "selected all", then on the "Molecule Display" tab I clicked Hide on Atoms, Cartoons, and Surfaces. Then I selected only the main protein (there is also a sugar molecule in the structure which I didn't want) and clicked on "Show" for Surfaces. This gave a nice space-filling type of structure that seemed amenable to printing. Then I went to File->Save and saved it as a .stl.
![UEA-I protein structure in ChimeraX](/myfiles/uea_rotate.gif)

## Modifying the .stl
Now this could be directly put into your slicer and printed as a semi-solid object, but I think it would look cool to try to light it from the inside. That means I'll need to make it hollow and try to get a uniform shell thickness all over. I've had good results with a free program called Meshmixer (although it isn't being updated for MacOS anymore, so use at your own risk). Blender can also do it, but I have found that Meshmixer handles all the lumps and weird surfaces a little better. In Meshmixer, Import your .stl, then go to Edit, Hollow and I used an Offset Distance of 1 mm for this model, but that may vary. Then I exported it as a new .stl.

## Cleaning up the model
Because of the shape of the protein structure, I ended up with some large blobs inside that really aren't going to be visible or do anything except waste filament. I don't have a good technique for getting rid of those. Meshmixer wasn't working too well for me (but I don't really know how to use it). I also tried Blender -- same problem. I ended up using the 3D modeling app I use for designing objects from scratch, Shapr3d for the iPad (highly recommended, and it's free with a .edu email). I cut it down the middle first (this is how I'll print it). There were some totally free-floating parts that I just deleted, and then I was able to chop off a lot of the other unnecessary protrusions one by one (using a plane and "split body"), and then export it again, as a .3mf file.
![UEA-I model in Shapr3D](/myfiles/uea-1_shapr3d_2.png)
![UEA-I model in Shapr3D](/myfiles/uea-1_shapr3d.png)

## Slicing and printing
In PrusaSlicer I decided to use 0.20mm layers and supports "everywhere" since there are some big overhanging areas that will definitely need it.

## Adding lights and final assembly
![UEA-I print before and after cleanup](/myfiles/uea-1_before_and_after_cleanup.png)

Once I had cleaned off all the support material (not trivial!) and given it a quick heat treatment to get rid of small hairs with my hot air gun, I was ready to try fitting in some lights and a power cable. I found [this](https://a.co/d/5RIWmkG) USB cord with a switch on it on Amazon. I also made a stand out of *sparkly black* for it to sit on and to route the wire. I used Shapr3d again to design the base and cut out the indentations to hold the model. I guess I forgot to give a little extra room when I cut it out (usually I scale up the removed part by 1% first) so it's a tight fit, but it snaps in and isn't going anywhere. Now that I think about it, I could have just cut the protein flat and glued it on to a flat base and it would probably be indistinguishable... oh well. I made an internal tube as well to guide the cable up through the base and into the protein through a hole where it wouldn't be visible. I also put a pause in the base print to give me a chance to drop some lead shot into the infill to give it enough weight to stay put. It only makes a slight maraca sound when you move it. When it was done I also added some sticky rubber feet. Now for the lighting. I went with your simple standard LEDs in blue and orange. They just need resistors so they can run happily off the 5V USB supply. Solder it up, use a little hot glue to keep things in place, and finally very carefully glue the halves together. I'm using super glue and it cures instantly so I held it together and then carefully added glue around the edges. I forgot to take pictures of the inside with all the wiring in place.
![UEA-I open bottom half on base](/myfiles/uea-1_base_and_bottom.png)
![UEA-I finished model](/myfiles/IMG_1128.png)
![UEA-I finished model](/myfiles/IMG_1129.png)
The orange lights spotlight the two areas of the protein that bind to its target sugar molecule. In the end this came out a little thicker than I wanted, so not much light is getting through, but that's OK.

## What's next
Here is another I made, using translucent PLA to represent the cell membrane that the protein sits in, which also doubles as a base for it to sit on and a place for the power cable. For this one, I used 5V COB LED strip wrapped around inside. Hard to see but the protein surface is modeled in reverse inside the "membrane". I'll keep thinking of new things to try. Maybe something that involves movement (F1 ATPase, flagella, secretion system or some other kind of large protein complex)?
![MelB finished model](/myfiles/IMG_1130.png)
![MelB finished model](/myfiles/IMG_1131.png)
![MelB finished model](/myfiles/IMG_1132.png)


