![동그라미로 표시한 차선 BEV 비교](assets/comparisons/ko/case1-02.png)

# HBLR

[English](README.md)

HBLR은 **CARLA 기반 자율주행 연구에서 사용하는 BEV segmentation ground truth(GT)의 생성 오류를 분석하고, 더 정확하고 일관된 GT를 만들기 위해 개발한 모듈 및 분석 프로젝트**입니다.

기존 BEV 생성 방식에서는 점선·실선이 바뀌거나, 없는 차선이 생성되고 있는 차선이 누락되며, 주행 가능 영역이 실제 도로 형상과 다르게 표현되는 문제가 관찰됐습니다. 고도 정보를 제대로 반영하지 못해 고가도로와 하부 도로가 겹치는 사례도 있습니다. 시뮬레이터에서 생성한 데이터라도 장면과 일치하는 정답이 되는 것은 아닙니다.

이 문제는 GT 생성 단계에 그치지 않습니다. 여러 기존 자율주행 모델에서 활용하는 BEV 표현을 비교하면서 같은 유형의 불일치를 확인하고, 잘못 생성된 라벨이 학습 정답이나 정책 입력으로 이어지는 문제를 분석했습니다. 이 README는 어떤 부분이 잘못됐는지 설명하고, 기존 GT·BEV 표현(`Ori`)과 HBLR의 수정 결과(`Our`)를 카메라 관측 및 모델별 시각화와 함께 비교합니다.

색깔 동그라미는 카메라 관측과 BEV에서 서로 비교할 위치를 표시합니다. `Ori`는 기존 표현, `Our`는 HBLR의 수정 표현입니다.

## 연구 논문과 구현 코드

이 저장소는 HBLR 연구 논문의 전체 내용을 설명하기 위해, BEV GT 생성의 문제 정의와 개선 방법, 모델별 비교 사례를 정리한 자료입니다. 실제 CARLA 데이터 수집 및 BEV GT 생성 코드는 **AGILEQ-Training의 `data_gen` 모듈**에 있습니다.

- [구현 코드: AGILEQ-Training / data_gen](https://github.com/SungjinDavidLee/AGILEQ-Training/tree/main/data_gen)
- [한국어 실행 방법 및 BEV 생성 설명](https://github.com/SungjinDavidLee/AGILEQ-Training/blob/main/data_gen/README_ko.md)

## GT 오류와 모델 학습

BEV segmentation 모델은 생성된 GT를 정답으로 삼아 학습합니다. 예를 들어 [TransFuser의 공개 학습 코드](https://github.com/autonomousvision/transfuser/blob/2022/team_code_transfuser/model.py)는 BEV 예측과 `bev` 라벨 사이의 cross-entropy를 손실로 사용합니다. 따라서 라벨에 없는 차선이 그려져 있거나 실제 차선이 빠져 있다면, 학습 과정은 그 잘못된 라벨에 예측을 맞추도록 유도합니다. BEV를 정책 입력으로 사용하는 경우에는 부정확한 도로 표현이 모델의 관측으로 전달됩니다.

HBLR은 이런 오류를 모델 구조의 문제로만 해석하기 전에, 학습·입력 데이터의 생성 과정부터 점검해야 한다는 문제의식에서 출발했습니다. 모델별 비교에는 BEV 입력·예측·특징 시각화가 포함되므로 각 결과의 역할을 구분해 살펴봅니다. 상세한 생성 방식과 오류 분석은 [기술 분석](docs/analysis.md)에 정리했습니다.

## 관련 연구: SimBEV (2025)

HBLR은 SimBEV와 비슷한 시기에 CARLA 기반 BEV GT 생성의 부정확성을 인지하고, 오류 사례 분석과 생성 방식 개선을 연구한 작업입니다. 이러한 문제의식은 2025년에 공개된 **SimBEV**에서도 다뤄집니다.

SimBEV 논문의 Related Work와 §3.4, Figure 5는 waypoint에만 의존할 때 도로 GT가 부정확해질 수 있고, 상공 카메라도 차량·식생·구조물에 가려 불완전한 GT를 만들 수 있음을 설명합니다. 고가도로처럼 높이가 다른 도로가 겹치는 상황도 일반적인 생성 방식의 한계로 다룹니다.

이는 **BEV GT의 정확성과 생성 방식 자체가 최근 연구에서도 다뤄지는 연구 과제**임을 보여줍니다. HBLR은 차선 유형·존재 여부, 도로 영역, 고도에 따른 불일치를 비교하고 수정하는 데 초점을 두었습니다.

- [SimBEV 논문: A Synthetic Multi-Task Multi-Sensor Driving Data Generation Tool and Dataset](https://arxiv.org/abs/2502.01894)
- [논문 본문: GT 생성 문제와 처리 방법](https://arxiv.org/html/2502.01894v2)
- [SimBEV 구현 코드](https://github.com/GoodarzMehr/SimBEV)

## 모델별 BEV 시각화

| 모델 | 본문에 포함한 예시 |
| --- | --- |
| LAV | BEV 시각화, 차선 존재 여부, 도로 고도 |
| TransFuser | 차선 존재 여부 비교 |
| Think2Drive | 차선 존재 여부 비교 |
| Roach | 도로 고도와 고가도로 예시 |
| ThinkTwice | Roach와 함께 살펴본 BEV 입력 및 CNN 특징 시각화 |

## 네 가지 비교 사례

| 사례 | 관찰한 문제 | 수정 방향 |
| --- | --- | --- |
| 1. 차선 유형 | 중앙선의 점선과 실선이 잘못 표현됨 | 장면에 보이는 차선 유형 유지 |
| 2. 차선 존재 여부 | 없는 차선이 생성되거나 있는 차선이 누락됨 | 실제 차선의 존재와 연결 구조 반영 |
| 3. 주행 가능 영역 | BEV 영역이 실제 도로 형상과 다름 | 도로 경계와 교차로 형상 정합성 개선 |
| 4. 고도 | 고가도로와 하부 도로가 BEV에서 중첩됨 | 자차가 위치한 도로 높이에 맞는 영역 분리 |

## 사례 1: 차선 유형

**기존·수정 비교 · `Ori` / `Our`.** 점선과 실선이 카메라 관측과 일치하는지 비교합니다. 동그라미는 중앙선 유형이 다르게 표현되는 위치를 표시합니다.

![기존·수정 BEV 점선과 실선 비교](assets/comparisons/ko/case1-01.png)

![빨간 동그라미로 표시한 차선 유형 비교](assets/comparisons/ko/case1-02.png)

### LAV BEV 시각화

LAV의 BEV 표현을 주행 장면과 함께 놓고 비교합니다.

![LAV 주행 장면과 BEV 시각화](assets/comparisons/ko/lav-bev.png)

## 사례 2: 차선 존재 여부

실제로 보이는 차선이 BEV에 포함되는지, 차선이 없는 곳에 차선이 생성되는지 비교합니다. 교차로, 회전교차로, 곡선 도로의 예시를 포함합니다.

**`Ori` / `Our` 비교 및 LAV BEV 시각화.**

![동그라미로 표시한 차선 누락과 추가 비교](assets/comparisons/ko/case2-01.png)

### TransFuser

TransFuser의 BEV 시각화를 기존 표현 및 HBLR의 수정 표현과 함께 보여줍니다.

![TransFuser BEV 시각화와 회전교차로 기존·수정 비교](assets/comparisons/ko/case2-transfuser.png)

### Think2Drive와 LAV

오른쪽 위는 Think2Drive, 아래쪽은 LAV의 BEV 시각화입니다. 왼쪽 위는 `Ori` / `Our` 비교입니다.

![Think2Drive와 LAV BEV 시각화 및 기존·수정 비교](assets/comparisons/ko/case2-think2drive-lav.png)

### LAV 및 추가 비교

동그라미는 주행 장면의 위치와 BEV의 해당 영역을 연결해서 보여줍니다.

![LAV 차선 존재 여부 비교](assets/comparisons/ko/case2-02.png)

![차선 존재 여부 비교 3](assets/comparisons/ko/case2-03.png)

![차선 존재 여부 비교 4](assets/comparisons/ko/case2-04.png)

![차선 존재 여부 비교 5](assets/comparisons/ko/case2-05.png)

![차선 존재 여부 비교 6](assets/comparisons/ko/case2-06.png)

## 사례 3: 주행 가능 영역

**기존·수정 비교 · `Ori` / `Our`.** 주행 가능 영역의 모양을 실제 도로 경계 및 교차로 구조와 비교합니다. 색깔 동그라미는 카메라와 BEV에서 대응하는 영역을 표시합니다. 아래쪽에는 추가 BEV 시각화를 배치했습니다.

![색깔 동그라미로 표시한 주행 가능 영역 비교](assets/comparisons/ko/case3-01.png)

![교차로 형상 비교와 추가 BEV 시각화](assets/comparisons/ko/case3-02.png)

## 사례 4: 도로 고도

고가도로와 하부 도로 주변의 BEV 표현을 비교합니다. 하나의 평면으로 투영하면 자차가 주행 중인 도로와 다른 높이의 도로가 겹쳐 표현될 수 있습니다.

### LAV

색깔 동그라미로 주행 장면과 LAV BEV의 대응 위치를 표시했습니다.

![색깔 동그라미로 표시한 LAV 고가도로 BEV 시각화](assets/comparisons/ko/case4-lav.png)

### Roach와 ThinkTwice

Roach와 ThinkTwice의 주행 장면, BEV 표현과 특징 시각화를 함께 보여줍니다.

![Roach와 ThinkTwice 주행 장면·BEV·특징 시각화](assets/comparisons/ko/case4-roach-thinktwice.png)

![Roach 고가도로 주변 BEV 시각화](assets/comparisons/ko/case4-roach.png)

### 기존·수정 비교

`Ori`와 `Our`를 통해 자차 주변에서 서로 다른 높이의 도로가 어떻게 표현되는지 비교합니다.

![도로 고도에 따른 기존·수정 BEV 비교](assets/comparisons/ko/case4-01.png)

![고가도로 하부 기존·수정 BEV 비교](assets/comparisons/ko/case4-02.png)
