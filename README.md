![Annotated lane-marking BEV comparison](assets/comparisons/en/case1-02.png)

# HBLR

[한국어](README_ko.md)

I visualized BEV values and representations from autonomous-driving models I frequently use, and compared their lane markings, drivable areas, and road elevation with camera observations. HBLR brings these model examples together with the original (`Ori`) and revised (`Our`) BEV comparisons.

The colored circles mark the locations being compared in the camera views and BEV panels. `Ori` is the existing representation; `Our` is the HBLR revision.

## Models and BEV visualizations

| Model | Examples in this README |
| --- | --- |
| LAV | Reference BEV visualization, lane presence, and elevation |
| TransFuser | Lane-presence comparison |
| Think2Drive | Lane-presence comparison |
| Roach | Elevation and stacked-road examples |
| ThinkTwice | BEV input and CNN feature visualization alongside Roach |

## Four comparison cases

| Case | Observed problem | Aim of the revision |
| --- | --- | --- |
| 1. Lane-marking type | Dashed and solid center lines are represented incorrectly. | Preserve the marking type visible in the scene. |
| 2. Lane presence | Nonexistent markings appear, or existing markings disappear. | Align markings with the road layout. |
| 3. Drivable area | The BEV drivable region differs from the road geometry. | Represent road boundaries and intersections consistently. |
| 4. Elevation | An overpass and the road beneath it overlap in BEV. | Separate road surfaces relevant to the ego vehicle's elevation. |

## Case 1: lane-marking type

**Original/revised comparison · `Ori` / `Our`.** Compare the solid and dashed markings with the camera views; the circles highlight the center-line mismatch.

![original and revised solid/dashed lane markings](assets/comparisons/en/case1-01.png)

![lane-marking mismatch highlighted with red circles](assets/comparisons/en/case1-02.png)

### LAV BEV visualization

BEV representations from LAV are shown alongside the driving views for comparison.

![LAV driving views and BEV representations](assets/comparisons/en/lav-bev.png)

## Case 2: lane presence

Check whether visible markings are present in BEV and whether markings are generated where the camera view shows none. The examples include junctions, roundabouts, and curved roads.

**`Ori` / `Our` comparison and LAV BEV visualization.**

![missing and additional markings highlighted with circles](assets/comparisons/en/case2-01.png)

### TransFuser

TransFuser BEV visualization is shown alongside the original and HBLR representations.

![TransFuser BEV visualization and original/revised roundabout comparison](assets/comparisons/en/case2-transfuser.png)

### Think2Drive and LAV

Think2Drive appears at the upper right and LAV in the lower rows. The upper-left panels compare `Ori` and `Our`.

![Think2Drive and LAV BEV visualizations alongside original/revised comparison](assets/comparisons/en/case2-think2drive-lav.png)

### LAV and additional comparisons

The circles connect the road locations in the driving views with the corresponding BEV regions.

![LAV lane-presence comparison](assets/comparisons/en/case2-02.png)

![lane-presence comparison 3](assets/comparisons/en/case2-03.png)

![lane-presence comparison 4](assets/comparisons/en/case2-04.png)

![lane-presence comparison 5](assets/comparisons/en/case2-05.png)

![lane-presence comparison 6](assets/comparisons/en/case2-06.png)

## Case 3: drivable area

**Original/revised comparison · `Ori` / `Our`.** Compare the drivable-region shape with the road boundaries and intersection layout. The colored circles highlight corresponding regions across the camera and BEV views; the lower row includes an additional BEV visualization.

![drivable-road geometry comparison with colored circles](assets/comparisons/en/case3-01.png)

![intersection geometry comparison and additional BEV visualization](assets/comparisons/en/case3-02.png)

## Case 4: road elevation

Compare BEV representations near overpasses and stacked roads. Projection onto a single plane can mix the road beneath the ego vehicle with a road at another height.

### LAV

The colored circles identify corresponding locations in the driving and LAV BEV views.

![LAV BEV visualization of stacked roads with colored circles](assets/comparisons/en/case4-lav.png)

### Roach and ThinkTwice

The panel shows driving views, BEV representations, and feature visualizations associated with Roach and ThinkTwice.

![Roach and ThinkTwice driving views, BEV and feature visualizations](assets/comparisons/en/case4-roach-thinktwice.png)

![Roach BEV visualization around an overpass](assets/comparisons/en/case4-roach.png)

### Original and revised comparison

`Ori` and `Our` compare road layers around the ego vehicle at different heights.

![original and revised BEV road-elevation comparison](assets/comparisons/en/case4-01.png)

![original and revised BEV comparison beneath an overpass](assets/comparisons/en/case4-02.png)

## BEV generation background

The following notes and excerpts give context for CARLA/OpenDRIVE map data and BEV generation. The diagram records the relationships considered during the investigation.

![BEV generation relationships and investigation notes](assets/comparisons/en/bev-generation-background.png)

### Rendering and aerial-map generation

![Rendering and aerial-map generation context 1](assets/images/image1.png)

![Rendering and aerial-map generation context 2](assets/images/image2.png)

![Rendering and aerial-map generation context 3](assets/images/image3.png)

![CARLA map visualization with English explanation](assets/comparisons/en/carla-map-background.png)

![Rendering and aerial-map generation context 6](assets/images/image6.png)

### Map topology

![CARLA map topology context 1](assets/images/image7.png)

![CARLA map topology context 2](assets/images/image8.png)

![CARLA map topology context 3](assets/images/image9.png)

### Lane-marking consistency

![Lane-invasion and visible-marking consistency context 1](assets/images/image12.png)

## Technical analysis

[BEV label consistency analysis](docs/analysis.md) covers map geometry, rasterization, and interpretation of the four cases. The examples here are qualitative comparisons; numerical evaluation is not included.
