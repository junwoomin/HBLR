# BEV Label Consistency Analysis

## What is being corrected

HBLR visualizes BEV values and representations from frequently used autonomous-driving models, including LAV, TransFuser, Think2Drive, Roach, and ThinkTwice. The four cases concern the construction of BEV labels and maps. A false marking in a generated target can become training supervision, so an apparent perception failure should first be separated from an error in the target itself. The camera views and original/revised BEV pairs support qualitative inspection of that distinction.

## Map geometry and rendered appearance

CARLA road topology, visible road surfaces, and a custom BEV rasterizer are different representations of a scene. A connected lane in a map does not automatically establish that a painted line is visible at every point along it.

The [CARLA Python API](https://carla.readthedocs.io/en/latest/python_api/#carlamap) describes `get_topology()` as a minimal graph of OpenDRIVE waypoint connections. It is not an image of road paint. The [lane-invasion sensor documentation](https://carla.readthedocs.io/en/latest/ref_sensors/#lane-invasion-detector) also explains that discrepancies between OpenDRIVE and the visible map can produce crossings of markings that are not visible in the scene.

This suggests inspecting both the map data and the rasterization procedure before attributing every mismatch to a learned detector. The exact faulty code paths are not identifiable from these comparison images alone.

## Case 1: solid and dashed markings

The examples show a difference in center-line type between the original and revised labels. A useful diagnostic is to inspect left/right marking attributes separately from lane-center geometry and then check how each marking type is rasterized.

The visual revision demonstrates the intended appearance. It does not establish that every map location or marking transition has been corrected.

## Case 2: missing and additional markings

The examples cover intersections, bends, and other road layouts where the BEV includes an extra marking or omits one. Potential causes to examine include disagreement between map metadata and visible assets, treatment of junctions, sampling gaps, and coordinate or drawing errors. These are diagnostic possibilities rather than confirmed causes.

Comparison panels associated with LAV, TransFuser, and Think2Drive help show the problem in several BEV representations. Different class definitions, scale, and color palettes must be accounted for before comparing them.

## Case 3: drivable geometry

Road surfaces and lane markings require separate checks. A line can look plausible while the surrounding drivable polygon still has the wrong shape. The examples show why road width, boundaries, and intersection geometry should be inspected alongside marking placement.

## Case 4: elevation and overlapping roads

A projection that keeps only horizontal position can collapse roads at different heights into the same BEV cells. The overpass examples show the original representation containing a road layer inconsistent with the ego vehicle's current surface, and a revised representation that removes that overlap.

An elevation-aware treatment must retain the appropriate road connectivity on ramps and slopes as well as distinguish stacked surfaces. A global height threshold is a possible design choice, but these images do not establish the implementation or its behavior at transitions.

## Rendering mode and BEV generation

[CARLA no-rendering mode](https://carla.readthedocs.io/en/latest/adv_rendering_options/#no-rendering-mode) skips scene rendering. Cameras and GPU-based sensors return empty data in that mode. CARLA's `no_rendering_mode.py` example draws an aerial view from structured simulation information using Pygame.

A BEV drawn from map and actor data can therefore belong to a different pipeline from a rendered camera or segmentation image. Off-screen rendering still computes sensor images and should not be confused with no-rendering mode.

## BEV generation background

The research notes consider CARLA API/OpenDRIVE drawing, `carla-birdeye-view`, and BEV representations used around LBC/LAC, InterFuser, TransFuser, Roach, TCP, ThinkTwice, and DriveAdapter. They also consider the difference between raster labels and vector map representations around Bench2Drive-related work.

The dependency relationships and elevation handling should be checked against specific repository versions before treating this exploratory comparison as a complete model genealogy.

## Evidence represented here

The examples support four categories of label mismatch and show the intended revisions. They do not provide the label-generation implementation, a counted benchmark, or a controlled downstream driving experiment. See the [main README](../README.md) for the complete model visualizations and comparisons, including the colored circles and arrows.
