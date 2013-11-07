# Flow

The Flow visual performance system, built with TouchDesigner.


videos:
https://vimeo.com/album/2507048

updates, images, videos, etc:
http://t3kt.net/Projects/Flow

-------------

## Structure
The Flow system is comprised of several, relatively self-contained sub-systems:
* Geometry (3D)
* Materials / Lights / Camera (/Action!)
* Rendering
* Post-Processing (2D)
* Control Routing / Processing / MIDI
* Control GUI
* Audio Input / Analysis
* Output / Recording

The primary data flow is arranged like this:
```
               Geometry --> \
                             ---> Rendering ---> Post-Processing ---> Output/Recording
Materials/Lights/Camera --> /
```

## 



