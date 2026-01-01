---
title: CD scanning tool
date: 2026-01-01 12:00:00
categories: [Projects]
tags: [python, programming] #use lowercase, comma separated
#file has to be named yyyy-mm-dd-<post name>.md in order to show up
---
# Writing a program to quickly identify CDs from their spines

## Background

CDs were the dominant music format when I was young. Then, solid state devices like the iPod came along (no skipping!), and finally streaming has taken over all regular music listening for most people. I held on to my old CD collection all these years but I recently decided to sort through it and see if there was anything of value (besides the priceless nostalgia factor). I sorted through the boxes, enjoying the memories, and logged each disc in a list on the Discogs website. If you're not familiar, Discogs is an exhaustive catalog of any music in all physical formats. It currently has something like 16 million entries and it's continuously updated and maintained by a large community of users. In addition, it's a marketplace for buying and selling. Somehow the nostalgia particles got to me, and although I started with the intent to clear out my collection, I somehow ended up browsing thrift stores for new CDs, and then started hunting for gems that might have resale value.

![A very stylish DG release from the late 80s](/myfiles/peer_gynt_DG.jpg){: w="300"}

Beautiful...

## Streamlining my thrift store hunting

Discogs also maintains a pretty decent app that includes a barcode scanner. So if you're flipping through a rack of discs, you can pick one up and within a few seconds get to the release page and all the info available on it, including the crucial sales info. This is OK, but if you're looking at racks of hundreds or thousands of discs, it's not going to be practical to pick up and scan each one. I wanted a way to speed up this process. In stores, CDs are typically arranged with their spines out (like books). The barcodes are on the back, so those won't be visible, but the spine usually has the record label, artist, title and catalog number. My idea was to take a picture with my phone and use optical character recognition (OCR) to quickly read as many spines as possible, search the Discogs database, and highlight the ones that had the best potential for resale.

![CDs on shelves in a store](/myfiles/CDs_on_shelf.jpeg){: w="500"}


## Workflow

The general steps I ended up with were:
- Take picture
- Segment into individual spines
- Extract text with OCR
- Search Discogs and retrieve all relevant info (number for sale, price, etc.)
- Return some score for each and mark the best ones on the original picture

This should all be done from my phone and with reasonable speed so I can get results while standing in the store.

## Segmenting image into individual spines

I was vaguely familiar with computer vision tools where, for instance, the computer could identify a certain type of object and draw a box around it (object detection).

<a title="(MTheiler), CC BY-SA 4.0 &lt;https://creativecommons.org/licenses/by-sa/4.0&gt;, via Wikimedia Commons" href="https://commons.wikimedia.org/wiki/File:Detected-with-YOLO--Schreibtisch-mit-Objekten.jpg"><img width="512" alt="Detected-with-YOLO--Schreibtisch-mit-Objekten" src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/38/Detected-with-YOLO--Schreibtisch-mit-Objekten.jpg/512px-Detected-with-YOLO--Schreibtisch-mit-Objekten.jpg?20190114114019"></a>

My needs were a little more strict, in that I wanted the box to not just surround the object, but tightly delimit the boundaries of the CD spine (segmentation). I thought this might be feasible using "primitive" computer vision processes -- edge detection and things -- kind of like (kind of literally the same as) adjusting different levels and applying filters in Photoshop until the edges of the CD cases "stick out", and then using those clear lines to crop the image. This worked OK (it should be a pretty simple task: all the objects are the same shape and size, contrasting colors, etc.) and yet it still wasn't performing well enough. Thus, I went to the next level: a full on neural network specialized for object detection, called YOLO ([here's](https://www.v7labs.com/blog/yolo-object-detection) a post with some background on that algorithm). This looked perfect, all I had to do was train it on my specific target objects. That meant drawing bounding boxes around hundreds and hundreds of CDs, but you gotta put in some work right? I initially used a web tool called Roboflow and then a Python program called X-AnyLabeling to draw the boxes and export them in YOLO format for training. 

![Annotating CDs with X-AnyLabeling](/myfiles/annotation.jpg){: w="700"}

Specifically I used the YOLOv8-OBB model (OBB is important, that's Oriented Bounding Boxes -- so they can be rotated at any angle, not just straight up and down, a *major advancement!*). I used default settings and fed in 40 or so pictures containing maybe 4000 CDs for training the model. Training was surprisingly fast on a MacBook M1 Max with 32GB RAM. That gave me a .pt file of weights that would be used for inference from new images. It was interesting to see how it did... there are some funny misfires, but overall it covers most of the CD spines in a typical image. We have the advantage that all spines will have almost the exact same dimensions, so some bad predictions can be easily thrown out by simply checking the length/width.

## Optical character recognition

Now that we have a nicely cropped single spine, we simply need to read the text: 
![A challenging spine to read](/myfiles/difficult_spine2.jpeg){: w="700"}

Oh come on! Well, some are just going to be impossible, but some are easier: 

![A better spine to read](/myfiles/easy_spine.jpeg){: w="700"}

Overall this is a harder problem than segmenting and it seems to be the weak point of the whole process. I tested a couple OCR options, from pre-processing the images myself and using the Pytesseract library, up to much more involved solutions for all-in-one whole document reading like [Donut](https://www.philschmid.de/fine-tuning-donut). Eventually I settled on [docTR](https://github.com/mindee/doctr), which has more features than we need but did the best job at reading difficult text. It outputs words grouped into lines, blocks, pages... and words have confidence scores assigned, so we can cut off at a certain quality level. I made a list of very common superfluous words to drop ("CD", "disc", "and", "digital", "etc", etc...) and also dropped most punctuation. I orient the spines horizontally but they may be upside down, so I check both ways and see which has the most good text and use that one. After struggling with this for a while I realized that although the artist/title/label are often in "creative" fonts, the catalog number is frequently much more clear (as in the example above). So if we prioritize that we should be better off. I put together some heuristics to decide if text is likely to be part of a catalog number, so if that looks good we can just use that for searching and disregard the rest. Doing that improved search accuracy a bit. Catalog numbers aren't always unique, but that's a risk we have to take.

## Searching Discogs

Discogs does have a pretty useful API. You need to get a token from your Discogs user account, and identify yourself with some unique name. Then you can submit queries and get back results (limited to 60 calls per minute though). One thing I'd like to dive deeper into is the actual search system they use. Also, I'm not sure if the API search is the same as what's on the website. The website search will try alternative queries if your search returns no results, and this has actually worked to rescue a bad search, but I don't believe the API search does this. Sometimes an extra character will poison an otherwise good search. This could use some more testing. I limit the search by format (CD) and type (release). Note that Discogs has "release" and "master" entries. So a bunch of different versions of an album will be grouped under a master, whether that's CD and vinyl versions or different countries, re-releases, etc. Our search returns a paginated list of release objects. I just take the first one, which I figure has the best shot of being correct. The release has different attributes that you can get, including some of the data we are interested in, for instance release.master will get the master entry, if there is one, and we can see how many releases it contains. If there are a ton of versions, that's already a sign that this is mass-produced and probably of low value. (Of course, one of those versions might be a super-rare remastered gold disc...) Anyway, after the initial search via the API, most data we will get by using the URL (release.url) and looking at the webpage directly.

## Webscraping

I hesitate to call this scraping since we're only looking at a very limited number of pages very slowly, but anyway... Most of the data about a release can be obtained through the API, but there were a couple things that weren't working consistently (now I forget which). But since all the data is clearly shown on the release page, I decided to look into scraping that, using the Selenium and BeautifulSoup Python libraries. This was a good reason to learn about this, which has all kinds of uses (such as checking for new job posts, if you're looking for a job like I'm supposed to be...). Selenium will handle opening a browser window, and Soup will parse the html and stuff and help us extract what we want. Selenium worked well to open a Chrome browser and go to the URL provided, and Discogs didn't seem to notice that it was automated. However, I couldn't get so-called "headless" mode to work, where it would open invisibly in the background. That was apparently blocked as suspicious or something. So when this is running, a Chrome window will be constantly open and updating. It's kinda fun to watch it open random releases pages though. Next, we save the entire html and look through it with BeautifulSoup. I took a look at a release page using "inspect elements" in my browser, and found that the data we want is nicely grouped together in a section called "release-stats" with a list under it. So BeautifulSoup lets us search for that and then dump those list elements into a Python dictionary for storage. Not as complicated as I thought! Later, we may try to access a different page with actual sales history data, but this is pretty good for now.


## The fun part?

Now we have all these data points to examine:

- Number of versions
- Lowest price for sale
- Number of copies for sale
- Low, Median and High past sale prices
- Number in users' "Want" and "Have" lists
- Days since last sale
- User rating info (average rating and number of ratings)

So how do we judge whether the disc is worth buying for, say, $1?
This has turned into the most difficult problem of the whole exercise. The data points are sparse and random. That is, a disc might have sold only once or twice over a decade, or never. And yet, if I list it for sale today, someone might pay $20 for it. I would like to gather a large amount of historical data and try to do some kind of linear regression or some more advanced machine learning on it, but there won't be enough future data to determine if the model is working well!
So, I basically went back to the vibes-based system I was using to begin with, with a little more math behind it. I do a linear equation with coefficients weighting the lowest "for sale" price, and the low, median and high past sales values. Historical sales price is probably the best predictor of future sales price, if we had to go by anything. Then, I manually subtract some value if it meets any of these conditions:
	only a single past sale (I can figure this out by checking if past low price == high price)
	too many for sale (>20)
	too long since last sale (>100 days)
	too many different versions released (>10)
And I also add a little value if it seems to be "in demand" (number in users' want lists is greater than in have lists). 

I cut this off at an arbitrary value (around 10), and highlight those discs on the original picture. This is not very scientific but it gives decent results and usually picks out a few from each picture that I send it, and then I can follow up on those manually.

![CDs with good ones highlighted](/myfiles/CDs_with_good_highlighted.jpeg){: w="400"}

Overall though, it's a surprisingly deep problem! If I were to train on all the historical sales data and all data available (even cover art could be fed in somehow), what would I use to validate the model? Arbitrarily pick a past date and use sales data after that point, I guess? But I still think the data are too sparse.

## One other detail

Finally, we need to bring this all together in a way that can be used from the phone. I looked into Swift, which is the iOS app language I guess, but I didn't really want to learn that right now. Maybe in the future. Luckily I found a workaround that seems fine, with only a slight time penalty compared to doing everything on the phone. iPhones have a pretty nice feature I had never used called Shortcuts. It allows you to chain together all kinds of different actions after one button press. So for this, clicking on an icon brings up the camera, you take a picture of some CDs, and this is renamed and saved to a shared cloud folder. Then the home computer, running our Python script, monitors this folder for any new .jpegs. When it sees one appear it processes it, and uploads the annotated picture for us to see in the same shared folder on our phone. It takes a bit to upload the pictures, depending on connection speed, but it's insignificant compared to the time spent querying Discogs. That's the major bottleneck, but there's not much to do about it besides optimizing OCR and search so we don't waste time with bad searches.


## Overall quality

While I can't really say how well it's predicting future sales value, we can look at one representative example of the whole process:

**CD spine segmentation:**

% of total boxes that are correctly identifying a spine: 64%

![All spine bounding boxes](/myfiles/CDs_processed_all_boxes.jpeg){: w="400"}

% correct after aspect ratio cleanup: 94%

![Only the correctly-sized boxes](/myfiles/CDs_processed_good_boxes.jpeg){: w="400"}

Final % of actual spines identified by a box: 96%

**OCR:**
Subjective, but of those spines that had "readable" text, 92% produced a decent OCR result

**Search:**
Of those that produced a "good" OCR product, 50% found the correct release

**Final:**
In the end, **41% (35/85)** of the CDs in the image were correctly identified

Definitely leaves room for improvement, especially in developing heuristics to clean up the OCR results and improve the search. 
But, still a useful tool when trying to scan racks and racks of CDs



## Future plans and improvements
- YOLO can be continually trained to get even better, hopefully. Another segmentation/identification model could be tried.
- Investigating search details to potentially improve search success.
- Scraping historical sales data and making a better value prediction model.
- Deciding how to price discs based on what's for sale and other variables is an interesting side problem.


Code here: [https://github.com/jpickard2/disctectiveV1](https://github.com/jpickard2/Disctective)

I did get help from LLMs (I know...) for troubleshooting errors, learning how to use various tools, and creating some code. I did my best to actually understand everything that was being done, at least at a surface level.
