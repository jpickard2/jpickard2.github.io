---
title: 3D printed terrain and topographical maps
date: 2024-12-22 19:00:00
categories: [Projects]
tags: [3d printing, maps] #use lowercase, comma separated
#file has to be named yyyy-mm-dd-<post name>.md in order to show up
---
# How to 3D print topographical maps

## Well, I finally got a 3D printer...

I actually had a good reason to get it. I needed a custom housing to hold together my cloud chamber project (see previous post). and 3D printing sounded like the best method to do this (rather than wood or metal working, which needed tools, space and materials). I was reluctant to get a 3D printer since I don't have much space, and I'm well aware of the number of 30-40 year old guys who have bought one and never really used it, or worse, actually tried to make money off of it by printing small Yodas to sell at their local craft fair. Being cautious, I first got my friend to print a couple prototypes on his machine. Those were looking promising, so when he was ready to sell it I was happy to take it off his hands. It's a Prusa MK3S+. That was over a year ago and I have nothing but positive things to say about it. It's a really reliable machine, and it's so amazing to design an object and have it in your hands in a matter of hours. You can make nice art objects but also really useful functional parts, which is incredible. Anyway, that's my plug for Prusa and 3D printing in general. This project is more toward the art side. I wanted to print some topographical maps.


## Finding and converting the data for large scale maps

I started out with larger terrain, specifically the entire state of Washington. I found a nice web tool called [TouchTerrain](https://touchterrain.geol.iastate.edu). You can just browse around the world map for an area you like and select a rectangular border around it, or you can find an outline of a specific region (such as a state or country border) and import that. These are .kml files and can be found all over the web, although I did have some problems with them sometimes (boundary lines going all over the place - not sure why). You can also draw boundaries in Google maps and export them as a .kml, which you then import into Touch Terrain. I found a publically available boundary file for WA state and imported that.
![WA state border in Touch Terrain](/myfiles/touchterrain_wa_screen1.png)

Next, adjust the options on the right for converting the map data to an .stl file. You can select the data source, size of the print, nozzle diameter (my nozzle is 0.4mm, standard), vertical exaggeration (this is recommended to help things stand out better, I put 1.5X). It can automatically add some extra layers to the base (I put 2mm). You can also have it automatically split your print into separate tiles if you're planning something really big. Then just hit "Export Selected Area and Download File" and it will go to work.
![Touch Terrain export options](/myfiles/touchterrain_options.png)

Once you have your .stl downloaded, import it into your slicer program (PrusaSlicer for me) and adjust those settings. I did the highest resolution available to me (0.1mm layers).
![WA state map in PrusaSlicer](/myfiles/wa_prusaslicer.png)

For that print, using PLA and taking up the whole sheet and at highest resolution, I had to wait 16 hours. I think it looks pretty cool right off the printer! The best thing is that the layer lines (which are usually an annoyance) are perfectly suited to a topographical map look.
![Raw WA state print](/myfiles/wa_print.png)
![Raw WA state print closeup](/myfiles/wa_print2.png)

PLA prints will always have some stringing (little hairs connecting points around the print) even in the best conditions (if there's a lot you may need to adjust some settings). These can be cleaned up with a little heat. If you happen to have a hot air solder station it works wonders. Just use a light touch, you don't want to melt your entire print. Hair dryer or lighter will also probably work in a pinch.

## Higher detail maps

That was looking pretty nice for big terrain, but when I began looking at cities and zooming in on houses I found that the resolution wasn't good enough. That's when I started exploring LiDAR data. Compared to satellite data which is usually on the order of 10 meter resolution, LiDAR (typically done from a plane I believe) can have sub meter resolution, so you can see individual houses and trees and roads clearly. Much better for the complex geometry of urban environments. I moved over to [Open Topography](https://opentopography.org). This is a great repository of public LiDAR data (although some of it requires an academic email to access). They also have a nice [tutorial](https://opentopography.org/learn/3D_printing) on converting their data to 3D prints, which I followed. You'll need to download a free program, QGIS, but that's about it. I won't go into much more detail since they cover it quite well. As you would expect, these files tend to be bigger and take more time to process and print. This one took almost 46 hours but I think it was worth it!
![3D print of LiDAR data from a residential neighborhood](/myfiles/neighborhood1.png)
![3D print of LiDAR data from a residential neighborhood](/myfiles/neighborhood2.png)
I even went all out and mounted it in a frame with some lighting.
![Mounted 3D print of LiDAR data from a residential neighborhood](/myfiles/neighborhood3.png)

This is definitely pushing the resolution limits of FDM printing (fused deposition modeling). Resin-based printing (SLA - stereolithography) is capable of finer detail, but there are some downsides (possibly toxic liquid substrate vs harmless filament, for one) so I'm not ready for that yet. Not in a small apartment with cats at least.

## What's next

I have been experimenting with painting these maps. If I have any success I'll post it.


