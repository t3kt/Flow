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

