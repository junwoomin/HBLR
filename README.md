![Annotated lane-marking BEV comparison](assets/comparisons/en/case1-02.png)

# HBLR

[한국어](README_ko.md)

HBLR is **a module and analysis project developed to investigate errors in BEV segmentation ground-truth (GT) generation for CARLA-based autonomous-driving research and produce more accurate, consistent labels**.

The inspected BEV generation approaches showed swapped solid/dashed markings, additional or missing markings, and drivable regions inconsistent with the visible road geometry. Examples also showed overpasses and lower roads overlapping when elevation was not properly represented. Simulator-generated labels are not automatically consistent with the scene.

The issue extends beyond label generation. Comparisons of BEV representations used by several existing driving models revealed similar inconsistencies, motivating an analysis of how incorrect labels can enter training supervision or policy inputs. This README explains the observed problems and compares the original GT/BEV representations (`Ori`) with HBLR revisions (`Our`), alongside camera observations and model-specific visualizations.

The colored circles mark the locations being compared in the camera views and BEV panels. `Ori` is the existing representation; `Our` is the HBLR revision.

## Research paper and implementation

This repository documents the full scope of the HBLR research paper, including the problem definition for BEV GT generation, the proposed corrections, and model-specific comparisons. The CARLA data collection and BEV GT generation code is maintained in the **`data_gen` module of AGILEQ-Training**.

- [Implementation: AGILEQ-Training / data_gen](https://github.com/SungjinDavidLee/AGILEQ-Training/tree/main/data_gen)
- [English setup and BEV generation documentation](https://github.com/SungjinDavidLee/AGILEQ-Training/blob/main/data_gen/README.md)

## GT errors and model training

BEV segmentation models learn against generated ground-truth labels. For example, the [public TransFuser training code](https://github.com/autonomousvision/transfuser/blob/2022/team_code_transfuser/model.py) uses cross-entropy between BEV predictions and the `bev` labels. If a label contains a nonexistent marking or omits a real one, the training objective encourages predictions to match that incorrect target. When BEV is used as a policy input, inaccurate road representations instead enter the model's observations.

HBLR starts from the need to inspect the training and input-data generation pipeline before attributing these errors solely to model architecture. The model comparison panels include BEV inputs, predictions, and feature visualizations, whose roles should be distinguished. See the [technical analysis](docs/analysis.md) for the generation background and error analysis.

## Related research: SimBEV (2025)

HBLR investigated inaccurate CARLA-based BEV GT generation around the same period as SimBEV, analyzing error cases and improving label generation. Similar concerns are addressed in **SimBEV**, released in 2025.

Its Related Work and §3.4, Figure 5 discuss inaccurate waypoint-only road labels, overhead-view occlusions from vehicles, vegetation, and structures, and limitations around roads at different elevations such as overpasses.

This establishes **BEV GT accuracy and generation as a research problem also addressed in recent literature**. HBLR focuses on comparing and correcting marking type, marking presence, drivable-area geometry, and elevation-related inconsistencies.

- [SimBEV paper: A Synthetic Multi-Task Multi-Sensor Driving Data Generation Tool and Dataset](https://arxiv.org/abs/2502.01894)
- [Full text: GT generation issues and methods](https://arxiv.org/html/2502.01894v2)
- [SimBEV implementation](https://github.com/GoodarzMehr/SimBEV)

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
