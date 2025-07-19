---
title: Bat receiver
date: 2025-07-18 22:00:00
categories: [Projects]
tags: [electronics] #use lowercase, comma separated
#file has to be named yyyy-mm-dd-<post name>.md in order to show up
---
# Designing and building a device to hear bats' ultrasonic sounds

## Background

It is common knowledge that bats are not birds, nor bugs (Calvin!), but a third thing, similar to a flying mouse. It is also well known that they use sound to "see" their way around in the dark -- echolocation. The sounds they make and receive are mostly above the human hearing range. Human ears are sensitive to sounds of roughly 20-20,000 Hertz (or cycles per second). Mine only goes up to about 15,500 Hz (15.5 kHz) now, as happens with age. Bats, however, can hear and make "ultrasonic" sounds above 20 and possibly up to 200 kHz. So is there any way for us to hear these sounds?

## Bat "detectors"

This is another project idea I came across with quite a few examples scattered around the internet. I guess it's thematically related to the cloud chamber and my hobby of macro photography in that it's revealing something that's normally hidden. Devices to convert ultrasonic sound to the audible range are most commonly called ["bat detectors"](https://magenta2000.co.uk/product/bat4-bat-detector/). I don't like that name too much. A better name I've seen is "bat listener" as seen on this site github won't let me link to: integraonline.com/~wolfden/bat-listener-iv-sch.jpg. 

![Bat listener](/myfiles/bat-listener-iv-sch.jpg){: w="500"}

I decided to call mine a "bat receiver" -- I think that captures the purpose better, and the circuitry is related to radio receivers, as we'll discuss. [Similar devices](https://www.arrl.org/files/file/Technology/PLN/Ultrasonic_Pinpointer.pdf) can be used to find power line arcs, as those also put out ultrasonic frequencies. There are some nice versions you can buy, from simple analog ones up to fancier digital devices or plug-ins for your phone that can record and display the sounds. My goal was to design it myself as a way of learning about electronics, especially audio stuff (I've also been building analog synthesizer modules -- more on that soon hopefully). Anyway, rather than buying one or just copying someone's design, I wanted to understand the theory and design it from the ground up myself, even if I wasn't really inventing anything groundbreaking.

## General concept

**I'm still not sure I fully grasp this stuff so take all explanations with a grain of salt**
The goal is to convert a higher frequency sound into a lower frequency sound. It would also be ideal to preserve the other characteristics of the sound, whatever that means. "Timbre" I guess. We can save that discussion for the music synthesis project. There are at least a couple methods for downconverting the frequency of a signal (defining a signal here as a repeating change in pressure waves, which we convert into voltages). One way is called a frequency divider, but this has the drawback (so I've read) that we lose a lot of the original character of the sound, which we want to preserve. Another way would be a completely digital method, which can do everything we want and more, although it has its own challenges too. But like I said, I'm interested in learning basic electronics so I wanted to do this with the simplest analog components possible so I could have some chance of understanding what's happening. So, I went with a method called "heterodyning" that is used in many bat detectors as well as radio receivers.

## Heterodyne receiver

Heterodyning refers to combining two *different* frequencies in order to get new ones. According to the helpful [Wikipedia article](https://en.wikipedia.org/wiki/Heterodyne), this concept was first used for early radio receivers to convert high frequncy Morse code signals directly into lower frequency audible sounds. As the article describes, two sine waves can be mathematically multiplied to produce their sum and difference frequencies. 

![Equation for multiplying two waves, from Wikipedia](https://wikimedia.org/api/rest_v1/media/math/render/svg/aada5e40d0c8b6590187f84bdad6f410c8caa103)

This is called mixing (not to be confused with audio mixing, which is more accurately called summing, and does not produce any new frequencies). So if we mix (*multiply!*) a high frequency radio signal at, say, [88.3 MHz](https://www.wcbn.org) with another local signal at 88.2 MHz, we will get out new frequencies at 0.1 MHz (100 kHz) and 176.5 MHz. For radio, there will be some further steps to demodulate (?) that signal to get out the original audio. We just want to bring down the ultrasonic sound into the audible range and listen to it directly. The heterodyne concept is still used in radios, because by adjusting the local signal, every station (frequency) can be conveniently converted to the *same* intermediate frequency for the radio to work on. That's useful because you can tune your filters and amplifiers to be perfect at that single intermediate frequency, instead of having to adjust them as you move the dial around. All that needs to be adjusted is the local oscillator and some front-end filtering/amplifying.

## Electronic implementations of mixers

So that's all well and good as a math proof, but how do we actually implement this? What circuit can perform the mathematical operation of multiplying two sine waves? There are a few common ones. One is a Gilbert cell circuit, explained [here](https://www.youtube.com/watch?v=7nmmb0pqTU0). I feel like I *almost* understand how it works, so I didn't feel too bad cheating by using a single chip that implemented it (SA/NE 602/612). It would be difficult to make out of discrete components anyway -- too many parts and hard to match the transistors. This chip was popular with amateur radio hobbyists for a long time -- it can also provide its own local oscillator (LO), if you add some extra components. Of course it's meant for MHz or GHz frequencies, but at least a couple bat detectors such as this [one](https://bertrik.sikken.nl/bat/ne612het.htm) have used it. Unfortunately, it's now expensive and hard to find. Vintage. I want cheap, plentiful, and above all simple. Amazingly I couldn't find any good successors to that chip.
The next option I considered is based around the switching concept, which I can wrap my head around better. This is probably what the Gilbert cell is doing too, I just can't visualize it. The idea is to use the LO to flip the incoming signal negative and positive. Doing this results in the appearance of the sum and difference frequencies in the resulting product. A circuit called a diode ring mixer can accomplish this by having the LO forward bias different diodes, thus allowing either the normal or inverse of the signal through. [Here](https://www.youtube.com/watch?v=junuEwmQVQ8&t=210s) is another excellent video explaining that implementation and going over mixing theory some more. Incidentally, the "ring modulator" effect you may have heard of from the analog synth world is a diode ring mixer. In fact, "ring modulator" is now used generically to refer to any mixing effect (real frequency mixing, not summing!), even if it doesn't use the actual ring of diodes. Anyway, I avoided the diode ring mixer because I read that it needs a lot of power to switch the diodes on and off, and I want this thing to be as efficient as possible. Luckily, I stumbled on a simple, elegant, cheap and low power way to do this with a classic 4000 series CMOS chip: the CD4051 analog multiplexer. [This](https://acidbourbon.wordpress.com/2021/04/11/a-74xx-defined-radio/) page gave me the idea and has another good review of mixers. This thing is just a tiny switch, where three input bits can control your choice of 8 different inputs/outputs going in/out of a common terminal. The only other thing we need is to get the inverse of our input signal somehow. Then we use the LO to flip between the regular and inverse and we get the mixed product out.

![CD4051](/myfiles/CD4051.png){: width="200"}

## The local oscillator

There are many types of oscillating circuits we could use. I was originally going to use a 555 timer to make a local oscillator whose frequency I could adjust with a potentiometer. We probably want our LO to be adjustable around, say, 20-120kHz. That way, if a bat is squeaking anywhere within that range, we should be able to get the difference frequency down into comfortable hearing range of single kHz. The sum at even higher frequencies will be filtered out or ignored, since we can't hear it anyway. I was a bit concerned about the wave shapes I would be mixing. If my LO produced anything other than a pure sine wave (not so trivial to get) it would have extra harmonics, which would add together with whatever the waveform of the bat-signal was (who knows what that is?). Would this be a problem? You can easily get a triangle or square wave out of a 555, but a sine wave takes significantly more components and it still won't be perfect. On the other hand, now that I went with the pure "switching" style mixer, the LO input *should* be as square ("digital") as possible (at least in my mind). It's essentially forced into squareness anyway... As I was switching (haha) over to the CD4051, I discovered another chip, the CD4047, that functions as a square wave oscillator, and has one big advantage over the 555: it always puts out a perfectly symmetrical 50% duty cycle square wave. The 555 is hard to get at 50%, and the duty cycle changes with frequency (annoying). Now, I'm still not sure if this would matter for the mixer, but I feel like it would be best to switch it between regular and inverse in equal measure -- if nothing else, for aesthetic reasons. All the CD4047 needs is one resistor and one capacitor to set the oscillation frequency. Through testing, I settled on values of 470 pF and 3.6k ohm plus a 20k ohm pot to adjust between roughly 20 and 120 kHz. I tested this on a breadboard by feeding different sine waves in from a signal generator, mixing them (via the CD4051) with the CD4047 LO, and looking at the frequency spectrum (FFT) of the output. Although the waves themselves looked like a mess, the FFT did show the appearance of a new peak that moved around when I changed the LO frequency -- the difference of the two signals!

## Ultrasonic microphones

Naturally, most microphones are optimized for the audio range, and have significant drop off above 10-20 kHz, so that's no good for our purpose. To my disappointment, a lot of the bat detector designs on the internet seem to get sloppy when it comes to the microphone, which seems like a big mistake. Everything depends on the quality of the signal coming in! Many people repurpose ultrasonic transducers as the microphones, which can't possibly be ideal. I looked around for true ultrasonic mics and found one that is no longer produced but still available from [some](https://www.win-source.net/products/detail/knowles/spu0410lr5h-qb.html) electronics resellers. Since it is a small surface mount package, there is even a [company in the UK](https://micbooster.com/product/ultrasonic-mic-board/?v=7885444af42e) that sells it on small breakout boards to make it easier to work with. They put a little picture of a bat on it too, hmmm... I bought one of those to test and then designed my own board, and grabbed a reel of them for about $0.50 each just in case. This was my first foray into surface mount reflow soldering with a stencil and solder paste. It went surprisingly well, even using a simple hot plate at work. So, this microphone should give us the best possible signal coming in, with good response up to at least 80 kHz.

![Picture of ultrasonic mic on breakout board](/myfiles/IMG_2065.JPG){: width="400"}



## Rest of the circuit

Now we have our quality signal coming in, mixing with a local oscillator, and the resultant products coming out, hopefully including some that have been downconverted from ultrasonic into hearing range. We need a couple other things: 1) some amplification and high pass filtering right after the mic. 2) low pass filtering after the mixer. 3) further amplification to output to headphones, as needed. For the initial amp/filter, I designed a common emitter amplifier. The coupling capacitor coming in was chosen to form a high pass filter already with the parallel R1/R2 of the amplifier. I also included an emitter capacitor which further increases the gain at higher frequencies. I tested with a signal generator and could see no amplification at 10 kHz but almost 5 times at 100 kHz. I initially left open the possibility of including a second amplifier stage (I had no idea how strong the real-life signal would be), but in the end one is sufficient. Next, we pass the signal through a coupling cap and into a buffer (creating another high pass filter on the way). This is just there so we can tap off the emitter to get the inverse of the signal. The signal and its inverse are then fed into the CD4051 as described. The LO comes in and switches between the two. From the output of that we go through a 10 kHz low pass filter, and into our last major component, which is a purpose made amplifier chip, the LM386. This one is still available. It provides 20x gain by default but I increased this to the max of 200x by adding a cap between pins 1 and 8. Volume is adjusted via a 10k pot (logarithmic taper is generally recommended but to be honest I don't notice the difference).

## PCB design

I also used this project to learn KiCad, which was actually a lot of fun. I did a pretty relaxed layout, keeping things positioned according to their function and place in the signal chain. All through-hole components. 2 layers, with the top copper pour as power plane and bottom as ground plane. No discrete vias added, just used the many plated through-holes for that purpose. I got prototypes from 3 different manufacturers (JLCPCB, PCBWay and Oshpark). All seemed fine. The separate mic board had some particular needs. First I downloaded a custom symbol/footprint for the part, which someone had made (maybe it came from the manufacturer?). It even had the recommended solder mask shape with empty "spokes". This mic has its opening on the underside, so there needs to be a hole in the PCB to let sound through. I opened that to the recommended diameter, and ordered the board half as thick, 0.8mm.

![PCB layout](/myfiles/PCB_layout.png){: w="400"}


## Enclosure design

I wanted the case to have comfortable hand-held size and shape, and that was just about perfect for the amount of parts inside, plus battery. I designed the case in Shapr3D on my iPad. A nice feature in KiCad is the ability to export a model (STEP file) of your populated PCB. That way you can design the case to fit around it perfectly. The mic board slides down into a slot in the case wall to get it as close to the outside as possible. I designed the board to connect via simple soldered wires to the two pots, power switch, mic board and battery, so that these could be moved around freely if I wanted to adjust their positions. Making removable lids for cases is an ongoing adventure. I have tried various snap fit designs before. For my synth module enclosures I've been using magnets, which work great but I didn't want to waste magnets on this. I tried a new design that slides along in a track. Then some small bumps lock it in place. Seems to work!


![Internals](/myfiles/IMG_2286.JPG){: w="400"}

![Populated PCB](/myfiles/IMG_2287.JPG){: w="400"}

![Case with back open](/myfiles/IMG_2222.JPG){: w="400"}

![Case held in hand](/myfiles/IMG_2227.JPG){: w="400"}


## Results

It draws about 15-17 mA at 9V on the bench supply. Thus, a typical 9V battery should last 29 hours. Incidentally, 9V batteries have pretty poor capacity compared to AAs and AAAs. I only went with 9V because of the convenient form factor. The power indicator LED adds about 1mA and it's very bright at night.
First, the high pass filtering and mic sensitivity do an excellent job attenuating normal audio range sounds. However, rubbing fingers together, scratching your skin, sniffing or exhaling through the nose, and walking through grass produce unexpectedly loud noise. Jangling keys make a noticably different and loud sound -- like porcelain wind chimes or something. This is the best test sound for checking function.
Finally it was time to take it outside. I sat on the grass at dusk where I had seen bats before, and listened, tuning the LO up and down. There was some background, not coincidentally like the sound of tuning a vintage radio. I guess this is produced by the LO interacting with noise and producing all kinds of product frequencies. By the way, multiplication is not perfect -- there are inevitably additional harmonics of the sum and difference frequencies produced. I don't know how to avoid it and it's not too annoying. But then, I heard some much louder clicks and pops. Bats, fluttering overhead as darkness fell. It was very satisfying to hear them after all that planning. 

**Listen to some recordings I made [here](https://youtu.be/ReN3e7uDQn8).**

The mic is *not* as directional as I feared, which was a pleasant surprise. You can hold it in front of you and any bats in the vicinity will be heard before they're seen.
I walked around and listened for anything else that might make interesting sounds. Bugs flying near the mic could be heard buzzing, although it's not much different than what you'd expect a bug to sound like. Again, the attenuation of regular noise was excellent -- I was next to a road but passing cars produced no sound in my headphones. I noticed a loud hiss from one motorcycle though. Fourth of July fireworks nearby were also not heard. Jangling keys in a passerby's pocket stood out clearly. A fountain produced pleasant white noise in the ultrasonic region too -- true white noise is supposed to be equally loud at frequencies from 0 to infinity, I think (we'll save that for the analog synth posts). I did hear one interesting call in the distance which I will try to track down in the future -- maybe a frog?

## Future plans and ideas

I haven't figured out a good way to put labels on my 3D-printed cases. Sharpie silver paint pen works but looks sloppy.

What about the other end of the sound spectrum -- sounds that are below our range of hearing: "infrasound"? Could these be multiplied up to audible range using the same principle?


## Schematic and parts list

![Bat receiver v1.0 schematic](/myfiles/bat_receiver_v1.0_schematic.png)

|Designator|Footprint|Quantity|Designation|Notes|
|----------|---------|---|-----------|---------|
|batt/power1|SolderWire-0.5sqmm_1x04_P4.6mm_D0.9mm_OD2.1mm|1|batt/power switch|
|C1,C3|C_Disc_D3.0mm_W1.6mm_P2.50mm|2|1n|
|C12|CP_Radial_D5.0mm_P2.50mm|1|10u|Polarized electrolytic|
|C2,C4,C5,C7,C8,C10,C11|C_Disc_D10.0mm_W2.5mm_P5.00mm|7|10n|
|C6|C_Disc_D3.0mm_W1.6mm_P2.50mm|1|470p|LO timing|
|C9,C13|C_Disc_D10.0mm_W2.5mm_P5.00mm|2|100n|
|D1|LED_D5.0mm|1|Red 2V Vf, 1mA target|
|freq1|SolderWire-0.5sqmm_1x02_P4.6mm_D0.9mm_OD2.1mm|1|to 20k linear pot|
|mic1|SolderWire-0.5sqmm_1x03_P4.6mm_D0.9mm_OD2.1mm|1|to mic board|
|out1|SolderWire-0.5sqmm_1x03_P4.6mm_D0.9mm_OD2.1mm|1|to stereo jack|
|Q1,Q2|TO-92_Inline|2|2N3904|
|R1|R_Axial_DIN0207_L6.3mm_D2.5mm_P7.62mm_Horizontal|1|47k|
|R10|R_Axial_DIN0207_L6.3mm_D2.5mm_P7.62mm_Horizontal|1|3k6 (using 3k57 because I have)|
|R11|R_Axial_DIN0207_L6.3mm_D2.5mm_P7.62mm_Horizontal|1|3k|
|R2,R13|R_Axial_DIN0207_L6.3mm_D2.5mm_P7.62mm_Horizontal|2|10k|
|R3|R_Axial_DIN0207_L6.3mm_D2.5mm_P7.62mm_Horizontal|1|4k7|
|R4|R_Axial_DIN0207_L6.3mm_D2.5mm_P7.62mm_Horizontal|1|1k|
|R5,R6|R_Axial_DIN0207_L6.3mm_D2.5mm_P7.62mm_Horizontal|2|22k|
|R7,R8|R_Axial_DIN0207_L6.3mm_D2.5mm_P7.62mm_Horizontal|2|470|
|R9,R12|R_Axial_DIN0207_L6.3mm_D2.5mm_P7.62mm_Horizontal|2|1k5|
|U1|DIP-16_W7.62mm_Socket|1|CD4051|
|U2|DIP-14_W7.62mm_Socket|1|CD4047|
|U3|DIP-8_W7.62mm_Socket|1|LM386|
|volume1|SolderWire-0.5sqmm_1x03_P4.6mm_D0.9mm_OD2.1mm|1|to 10k linear pot|


