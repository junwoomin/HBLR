![Elevation-aware BEV comparison](assets/images/image47.png)

# HBLR

[한국어](README_ko.md)

Research on the consistency of CARLA bird's-eye-view ground-truth labels, with visual comparisons of existing and revised representations. The focus is whether lane markings, drivable geometry, and road elevation agree with the scene around the ego vehicle.

## Four labeling problems

| Case | Observed problem | Aim of the revision |
| --- | --- | --- |
| 1. Lane-marking type | Dashed and solid center lines are represented incorrectly. | Preserve the marking type visible in the scene. |
| 2. Lane presence | Nonexistent markings appear, or existing markings disappear. | Align the presence and connectivity of markings with the road. |
| 3. Drivable area | The rasterized drivable region differs from the road geometry. | Represent road boundaries and intersections consistently. |
| 4. Elevation | An overpass and the road beneath it overlap after projection. | Separate road surfaces relevant to the ego vehicle's elevation. |

The comparison images place camera views on the left and the original and revised BEV representations on the right. These are qualitative labeling examples. Aggregate accuracy or downstream driving improvements have not been quantified here.

## Lane-marking type

![Solid and dashed lane-marking comparison](assets/images/image14.png)

## Lane presence

![Missing and additional lane-marking comparison](assets/images/image32.png)

## Drivable geometry

![Drivable-area comparison](assets/images/image35.png)

## Road elevation

![Overpass and lower-road comparison](assets/images/image48.png)

## Analysis and examples

- [Technical analysis](docs/analysis.md): label generation, map consistency, and interpretation of the four cases.
- [Complete image gallery](docs/gallery.md): all 48 images, grouped by topic.

The research examines BEV generation around CARLA/OpenDRIVE and examples associated with LAV, TransFuser, Think2Drive, Roach, and ThinkTwice. Their visualizations provide comparison context. Differences in colors or raster formats alone are not evidence of a labeling error.
