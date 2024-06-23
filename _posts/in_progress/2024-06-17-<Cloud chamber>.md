---
title: Cloud chamber project
date: 2024-06-17 19:00:00
categories: [Projects]
tags: [photography, electronics, radiation] #use lowercase, comma separated
#file has to be named yyyy-mm-dd-<post name>.md in order to show up
---
# Building a Wilson Cloud Chamber to Visualize Radioactive Particles

## Overall Idea

Being able to see invisible radiation that is in the air all the time is really appealing to me. The general principle of the cloud chamber is that you create a saturated fog of isopropanol or another similar substance (ethanol, methanol probably work? Isopropanol is easiest to get, least toxic?). At the bottom of the chamber you need a very cold (-35 C/-31 F) surface. This causes the vapor to become supercooled, making it poised to condense into a liquid state. When a radioactive particle passes through it, it can trigger the vapor to condense and form a visible trail. While cloud chambers can be made with a simple setup of dry ice under a metal sheet, I liked the idea of a device that would work right away with just the flip of a switch. Peltier coolers convert electrical potential into a temperature difference between their two sides. One side gets as hot as the other side is cold, and it happens very fast. A key problem is to remove that heat so the cold side can keep getting colder. I looked at repurposing old computer heatsinks but the sizes weren't right. Instead I found a company that makes aluminum water cooling blocks that are perfectly sized to fit 4 standard Peltier coolers. Doubling that up seemed like it would give me a decent enough surface area. Cold water is pumped through the blocks from a reservoir. A glass case goes on top to contain the vapor. Add some lighting and it was pretty much ready to go. I did a lot of planning, incremental changes and learned a lot from other peoples' designs on the internet, and I was really pleased to see some awesome looking trails pretty much from the first time I turned it on.

Check out the finished product at my YouTube channel here: www.youtube.com/@josephpickard1495

## Design details

(A full supply list is found at the end)

I started my design by thinking about the cooling surface. This would determine the dimensions of everything else and would define the viewing area. I wanted to make the cold surface as large as feasible, so I could have the best chance of seeing some particles. Of course, a big sheet of metal on top of dry ice would be simpler, cheaper, and have a bigger viewing area. But I didn't want to go that route! For me, 8 Peltier coolers (40 x 40 mm/1.57 x 1.57 in) seemed like a reasonable size (80 x 160 mm/3.15 x 6.3 in), and I found a company that makes aluminum water cooling blocks for this purpose. On top of the Peltier coolers would be a layer of copper to join them all together, protect them from the isopropanol, and enlarge the cold surface a bit to 105 x 185 mm/4.13 x 7.28 in. With this decided, I needed to make or buy the enclosure. I could make it myself in any size from acrylic, but isopropanol attacks acrylic, so I decided to go with glass. I looked for ready-made display cases, but in the end just had one custom made for a reasonable price. The internal height of the chamber would be 121 mm/4.75 in.
The inside of the chamber needed at least one key component: a reservoir for liquid isopropanol which would vaporize to fill the chamber. For this, I simply hot glued (which thankfully seems to be totally resistant to isopropanol) some black felt around the inside of the "ceiling". It was possible that the isopropanol would need some application of heat to encourage vaporization (this could be done electrically with some nichrome wire) but it wasn't necessary. The height of the chamber is sufficient that the ceiling is near room temp while the bottom is cold.
I needed a tight and temperature conducting interface between the aluminum blocks, Peltier coolers, and copper sheet. This is where a custom 3D-printed housing to hold everything together was a big help. This was my first experience designing and printing anything, so it went through many iterations, test prints, and dry-fitting of the parts. Finally, I printed it in black PLA and melted in some threaded brass inserts. 
<img src="/myfiles/projects/cloud_chamber_pics/IMG_8790.jpeg" alt="3D print with brass inserts">
I used a piece of acrylic as the bottom, and screwed it on with the aluminum blocks in place (oops, one corner shattered when I drilled the hole - no worries).
<img src="/myfiles/projects/cloud_chamber_pics/IMG_8793.jpeg" alt="3D print with acrylic piece">
I ran hot water through the blocks and spread out a layer of thermal paste, and placed the Peltier coolers on, feeding their wires out through the grooves in the housing. One thing I never figured out was a really clean wire management strategy, but oh well. 
<img src="/myfiles/projects/cloud_chamber_pics/IMG_8813.jpeg" alt="peltier coolers assembled">
Testing with a temperature probe pushed against the Peltier coolers showed very promising temps in the -35 C area! I added a smaller 3D-printed piece on top which held down the wires and allowed me to precisely tweak the height of the top level (without reprinting the entire housing). I wanted to make sure that the copper sheet on top was firmly pressed against the coolers.
<img src="/myfiles/projects/cloud_chamber_pics/IMG_8815.jpeg" alt="more assembled peltier coolers">
Finally, I applied more thermal paste and sandwiched on the copper sheet, and screwed it down firmly. The copper had been given a few coats of matte black enamel paint. To make sure the enclosure was air tight, I put a bead of silicone sealant around the rim for a perfect fit with the glass top.
<img src="/myfiles/projects/cloud_chamber_pics/IMG_8947.jpeg" alt="fully assembled cooling base">
Next, the glass top needed some work. As with the copper base plate, I gave the back wall and part of the sides some black spray enamel on the inside surface. Lighting angle and brightness seems to be very crucial in these devices. I wanted light coming across right at the level of the cold surface. I used LED strip lights hot glued to the outside of the glass, and 3D-printed shades to cover them from above on the inside, so no excess light would come up towards the viewer. I also painted to allow only a narrow band of light through, and used black electrical tape to stop light leakage on the outside. These were 12V LEDs and powered off the same supply as the Peltiers.
<img src="/myfiles/projects/cloud_chamber_pics/IMG_9267.jpeg" alt="glass enclosure with LED strip lights">
<img src="/myfiles/projects/cloud_chamber_pics/IMG_9268.jpeg" alt="glass enclosure with LED strip lights">
Speaking of power: I used a secondhand computer power supply for this (750 W). Each Peltier is 12 V and listed as drawing 8 A, so max 768 W. They don't actually use that much. I measured 531 W for all 8, at 12 V. Some say you shouldn't run them at max voltage, but I haven't had any issues yet. In any case, I figured out which wires were for the power supply's 12 V rails (there were 4) and tied those together, and the same for 4 ground wires. These I soldered to thick wire to come into a distribution block, where I could screw in all the wires to the Peltiers, LEDs, and anything else I wanted to power from the same supply. These wires do get noticably warm during operation but nothing worrisome. I also added a push button switch to short the wire that turns on the power supply.
Now I could finally do a real test. I poured some 91% isopropanol onto the felt, put the lid on top, and turned it on... In less than a minute, a thick fog was swirling around on the cold surface. A few trails appeared! But not many... There was one more thing to add.

### *High Voltage*
Some cloud chamber projects use it, some don't. There is no consensus on how or why it works, but somehow putting a high voltage electric field above the cold surface often enhances the number and visibility of trails. I was a little reluctant to try it, as I am not very comfortable with electrical stuff and would rather avoid anything dangerous. I was also concerned that an arc inside the saturated isopropanol atmosphere could cause an explosion. On the other hand, I wanted to see more and better trails!
I searched around for cheap/simple parts that could create a high voltage (~500 V or more). Others have used parts from bug zappers, the kind that are shaped like tennis rackets. These create a high voltage but also discharge it across the racket "strings", through a hapless insect. I thought this was slightly different than what I was after: a constant, high potential, but never a discharge (hopefully!). I stumbled upon another common device that produces high voltage but only very low current: air ionizers. I purchased a few units off AliExpress and tested them. They only produced ~400 V (much less than claimed) but possibly good enough to try. This particular type also had 2 output wires: one was ground, the other the high voltage. I connected one side to the copper cold plate, the other to a copper mesh suspended above it inside the chamber. When I turned it on, the result was clear: there was a dramatic enhancement of trails. I swapped the output wires (and thus the polarity of the field) and this made no difference - either way the trails were enhanced. I also put the mesh at different heights above the base plate. If I got too close (~2 in/5 cm) constant trails appeared where electrons were presumably discharging from the mesh to the base. Other than that, the distance didn't matter much, so I simply attached the mesh right on top of the felt, on the ceiling (~120 mm/4.75 in away). My best guess for why it works is that it's further "activating" the supercooled vapor, making it easier for a particle to trigger condensation. Whatever the reason, it clearly works in my setup.  
<img src="/myfiles/projects/cloud_chamber_pics/final_pic.jpeg" alt="fully assembled cloud chamber with camera on top">  

For filming from above, I cut a small rectangle out of the copper mesh. My 16-50 mm kit lens pressed against the glass was perfect for capturing the entire cold surface, albeit with some distortion around the edges.  

<img src="/myfiles/projects/cloud_chamber_pics/0004trimmedmore.gif" alt="animated GIF of radiation trails in the chamber">  

Mesmerizing, isn't it?


## What to improve if I make a new version

There are some air currents in the chamber that move the trails around a bit. This must be from temperature differences. LEDs produce some heat? Not sure how to improve.  
Some small constant discharges appeared on the base plate when high voltage is on. Not sure why this happens.  
The screws that hold down the base plate reflect light annoyingly, even though I covered them in matte black paint. Should redesign somehow to keep the screws out of the viewing area.  
Tidier cable routing/connectors.


## Supplies
**Remember, anything inside the chamber needs to be tolerant to isopropanol**

- [Double layered Peltier coolers (8)](https://a.co/d/7ZcCOHh)
- [Aluminum water cooling blocks (2)](https://coldandcolder.com/collections/featured-products/products/aluminum-water-block?variant=32615434846305)
- [Copper sheet](https://a.co/d/2FWNgq6)
- [Copper mesh](https://a.co/d/9GaSzDq)
- [Water pumps (2)](https://a.co/d/dFhMQUj)
- Silicone tubing
- Cooler, resusable ice packs
- [3D printed housing to form the base and contain the cooling parts](https://www.printables.com/model/912708-cloud-chamber-base)
- Plexiglass (acrylic) 2.4 mm/0.09 in thick for the bottom of the housing
- M4 screws
- [Threaded inserts for 3D printed parts](https://a.co/d/cnZu47s)
- Thermal paste
- Silicone sealant for plastics
- Glass enclosure (197 x 121 x 121 mm/7.75 x 4.75 x 4.75 in, 3 mm/0.125 in thick)
- Black enamel spraypaint (Rustoleum flat black)
- Power supply capable of ~600W and 12V (for example a computer power supply)
- [LED COB 12V strip lights](https://a.co/d/7FWNjIN)
- [Power distribution blocks](https://a.co/d/3aumFtu)
- Black felt
- 91% Isopropyl alcohol
- High voltage source such as for air ionization (search AliExpress for "Anion Generator DIY Ionizer Airborne Air Purifiers Negative Ion Module Cleaner N0PF")
