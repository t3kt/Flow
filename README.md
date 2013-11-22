# Flow

The Flow visual performance system, built with TouchDesigner.


videos:
https://vimeo.com/album/2507048

updates, images, videos, etc:
http://t3kt.net/Projects/Flow

-------------

## General Structure
The Flow system is comprised of several, relatively self-contained sub-systems. These sub-systems, and the general data flow are roughly the same as in the LinearChaos and DynamicStructure projects.
* Geometry (3D)
* Materials / Lights / Camera (/Action!)
* Rendering
* Post-Processing (2D)
* Control Routing / Processing / MIDI
* Control GUI
* Audio Input / Analysis
* Output / Recording

The primary data flow is arranged like this:
![data flow](http://t3kt.net/Content/Projects/Flow/data-flow.svg "primary data flow")

```
               Geometry --> \
                             ---> Rendering ---> Post-Processing ---> Output/Recording
Materials/Lights/Camera --> /
```

The secondary control data flow is arranged like this:
```
                                                Audio Analysis ---> \
                                                                     ---> Geometry / Materials / etc
Config/Defaults ---> \                                              /
                      ---> GUI ---> Control Recording/Playback --->
     MIDI Input ---> /                                              \
                                                                     ---> MIDI Output
                               
```

-------------

## Geometry Systems
The Geometry system in Flow is based on the interplay of forces acting on a particle system. There are only a few visible geometry elements:

1.  the particle system
2.  the force fields
3.  the core collision sphere
4.  the grid (though this isn't used much and might be removed)

### The Particle System
The particle system is displayed as light blue/white particle sprites. The small size and density of the particle field tends to make these look more like a fluid than individual particles.
The particles have several parameters which control their behavior and the impact of various forces upon them.
*   __fluidity__ - controls the rigidity of the particles and how easily they move and how heavily forces and turbulence influence them
*   __mass__ - controls the mass of the particles, which in turn impacts their momentum.

    > NOTE: Using a high mass along with a high fluidity tends to result in the particles getting flung out away from the core.

The particles are initially dispursed on a heavily randomized grid (it started as a grid and now looks like a multi-layered spiky thing). From there a series of influences are applied which modify their position and state.

### Turbulence, Wind, and Collisions
The turbulence forces are randomly (but smoothly) oscillating to some extent at most times. The __turbulence__ parameter at both high and low extremes result in strong small- or large-scale turbulence, whereas in the middle there is very weak turbulence.
The wind is applied in sine waves which oscillate at relatively constant rates, in order to keep the particles moving to some extent at all times.
The core collision sphere is a surface which the particles stick to when they hit it. They remain stuck to the surface of the sphere until they die off.

### Primary (Attractor) Force Fields
The primary (attractor) force fields are a set of two triangles with a metaball on each corner. The two triangles alternately rotate around the center and the metaballs attached to each vertex rotate along with them. The triangles are never both rotate at the same time. Because they overlap, all six metaballs end up forming a single blob shape. Each of the six force fields applies a force to the particles which either pulls them toward the field's center, or pushes them away from it. One group of the fields is the main force, and the other is a weaker opposite force (pushing when the main one is pulling, etc). The __attractor pull__ parameter controls the strength and direction (push vs pull) of these forces. These forces are applied as part of the particle calculations, and therefore are subject to things like the collision surface and __fluidity__ and __mass__.

### Secondary Warp Fields
The secondary warp fields are a set of force fields, several of which are based on enlarged copies of the attractor fields, apply a second phase of "force" to the particles. They apply rotational forces which mean that they behave very differently from the primary force fields in several ways. First, they are applied after the particle calculations, so they are not directly impacted by collision, etc (one neat result of this is that particles which are stuck to the core still get warped out of their "stucK' position). Second, they do not direclty impact the state of the particles, so they do not cause changes in momentum.


## Audio Reactivity
In order to create my entry for the MakeArtNow October/November challenge (<a href="https://www.facebook.com/groups/makeartnow/permalink/584149691633884/" target="_blank">on facebook</a>), and also for general awesomeness, I implemented audio reactivity in Flow.
The audio stream is analyzed in real-time. The analysis data is grouped into 4 bands (all of which are based on stereo data merged into mono):

1.  Raw
2.  High Band
3.  Mid Band
4.  Low Band

Each band runs through 4 types of analysis (so there are 16 analysis channels in total):

1.  Levels (RMS power)
2.  Peaks (highest peak value per window)
3.  Envelopes (based off of peaks, normalized over a short period of time
4.  Pulses (based off of each time the noramlized peaks go over a threshold)
  
At various points throughout the geometry and post-processing, the audio analysis data is pulled in and mapped onto parameters that modify behavior. This includes:
*  Mid band pulses trigger switching between group rotation of the primary attractor fields, and level values are used to influence the rotation rates for a short span of time after each pulse
*  Mid band pulses, trigger brief reversals in the polarity of the push/pull forces of the attractor fields. The pulses are rate limited (so they don't trigger too often).
*  Low band pulses (also rate-limited) trigger the time distortion in post-processing, causing it to momentarily jump backwards, then ramp forwards a bit, then resume normal time flow. When triggered, the time distortion suppresses all of the secondary delay taps, so only a single delay tap is visible (so instead of "echoes", it's more like stutters).



