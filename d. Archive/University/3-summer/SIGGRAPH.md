# 3D Gaussian Splatting for Real-Time Radiance Filed Rendering

본 필기는 Xoft의 설명에 의거한다.

## 1. Introduction

#### Topic
약 100장 정도의 이미지가 카메라 Pose와 함께 주어졌을 때 이로 3D 처럼 보이는 카메라 Pose에 대한 이미지를 합성하는 것이다.
쉽게 말해 파노라마 사진의 3D 버전인것이다.

![500](https://velog.velcdn.com/images/dusruddl2/post/19bf78b9-c60d-4407-9014-c68682aa1453/image.png)
- PSNR: 생성 이미지 퀄리티 `MipNeRF360`
- Train: 학습 속도 `Ours7K`
- FPS: 실시간 렌더링 속도 `Ours7K`
- 위 표를 분석해보면 `MipNeRF`에 비해 `Ours`가 월등히 성능이 좋은 것을 볼 수 있다.

#### Reference
- [gsplat](https://gsplat.tech/): 해당 알고리즘을 통해 생성된 이미지를 실시간 렌더링 할 수 있는 사이트
- [GitHub - graphdeco-inria/gaussian-splatting: Original reference implementation of "3D Gaussian Splatting for Real-Time Radiance Field Rendering"](https://github.com/graphdeco-inria/gaussian-splatting)
- [논문 리뷰: 3D Gaussian Splatting (SIGGRAPH 2023) : 랜더링 속도/퀄리티 개선 (tistory.com)](https://xoft.tistory.com/51)

#### Concept
> "3D Gaussian Splatting for Real-Time Radiance Filed Rendering" 이라는 제목을 분석하며 필요한 개념들에 대해 알아보자.

1. **3D Gaussian**
![500](https://velog.velcdn.com/images/dusruddl2/post/99938b31-98af-4169-bb69-36da215790f0/image.png)
- 2차원일 때 $k = 2$, 3차원일 때 $k = 3$
- 3D Gaussian을 이용하며, 평균을 중심으로 분산만큼 퍼지는 1D Gaussian 함수처럼 특정 지점에 모여있고 전체적으로 퍼진다. `초록 타원 처럼`

2. **Splatting**
![500](https://velog.velcdn.com/images/dusruddl2/post/7b79cc72-538b-457c-be9a-cddd211783a8/image.png)
- 위에 보여지는 것처럼 초록 타원들이 흩뿌려진다(Splat) 라고 생각하면 된다.
- 실제로 이미지를 촬영하여 렌더링하게 되면 학습되지 않은 부분은 실바늘처럼 생긴 가우시안 타원이 흩뿌려져 있는 것을 볼 수 있을 것이다.
- 렌더링 결과는 다음 사이트에서 확인 가능하다. [코드 빌드: 3D Gaussian Splatting (tistory.com)](https://xoft.tistory.com/48)

3. **Radiance Field**
![500](https://velog.velcdn.com/images/dusruddl2/post/79e26799-e337-41af-98b4-4086f93163b2/image.png)
- NeRF(Neural Radiance Field): 신경망을 사용하는 Radiance Field를 의미한다. 눈에 보이는 가시광선 공간을 모델링한다고 보면 된다.
- NeRF는 장면의 3D 구조와 Ray Tracing을 결합하여 신경망을 통해 장면의 Voxel 정보를 학습한다. 여기서 각 Voxel은 색상과 밀도 정보를 가지며, 신경망을 이를 통해 특정 시점에서의 2D 이미지를 생성한다.
- **Radiance Field**: 옛날엔 Real-Time Rendering에 NeRF를 사용했다면 여기선 NeRF를 사용하지 않고 Radiance Field만 사용한다고 생각하면 된다. 후술할 NeRF 세부 과정을 보면 알겠지만, MLP를 사용하지 않는다고 생각하면 된다.

#### NeRF
> NeRF란 대체 무엇이냐? 성능 비교하며 나왔던 퀄리티는 높으나 효율은 떨어지는 기술이라고 생각하면 될 것 같다.
> [논문리뷰: NeRF-Representing Scenes as Neural Radiance Fields for View Synthesis 리뷰 (velog.io)](https://velog.io/@minkyu4506/NeRF-Representing-Scenes-asNeural-Radiance-Fields-for-View-Synthesis-%EB%A6%AC%EB%B7%B0)

![500](https://velog.velcdn.com/images%2Fminkyu4506%2Fpost%2F6fd568c1-a267-4e59-bab8-1956894d16b0%2F%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-03-03%20%EC%98%A4%ED%9B%84%208.20.59.png)
1. 3D Point의 Sampled Set 생성 (바라 볼 관점들 지정)
2. 생성한 Sampled Set과 Sampled Set에 해당하는 2D Viewing Direction을  MLP에 넣어 해당 위체에서 바라본 객체의 Color과 Density 얻기
3. Classical Volume Rendering Techniques로 획득한 Color와 Density를 2D 이미지에 축적하기

`plus alpha: MLP`
- 인공 신경망(Artificial Neural Networks)의 가장 기본적인 형태
- Input Layer, 하나 이상의 Hidden Layers, Output Layer로 구성된 피드포워드 신경망이다.

#### Comparison with Other Techniques
![500](https://velog.velcdn.com/images/dusruddl2/post/d067a0f5-d9e6-406c-a4b8-a3e4c7491dc5/image.png)

|                                   NeRF                                    |                              Point Cloud                               |               3D Gaussian Splatting               |
| :-----------------------------------------------------------------------: | :--------------------------------------------------------------------: | :-----------------------------------------------: |
|                           신경망을 통해 뇌로 그림을 그리는 느낌                           |                          도화지에 점으로만 그림을 그리는 방식                          |             가우시안 타원을 이용하여 그림을 그리는 방식              |
| 각 픽셀에 대해 Ray를 Marching하고 Ray 상의 128개의 균등한 3차원 점에 대해 샘플링을 수행하기에 연산량이 엄청나다. | Dense한 Point로만 Scene을 구성하기 위해 수없이 많은 Voxel을 계산하게 되기 때문에 엄청난 계산량이 요구된다. | Point Cloud의 점이 좀 더 고성능하고 큰 가우시안 타원으로 바뀐다고 생각해보자. |
|                       Color &  Density가 없는 공간 까지 고려                       |                     Color &  Density가 없는 공간 까지 고려                      |            Color &  Density가 없는 공간 무시             |

#### Why Fast?
> 왜 다른 기술에 비해서 빠를까?
1. **3D Gaussian Modeling**: 점 단위로 계산하던 걸 확률에 기반하여 좀 더 넓은 단위로 계산할 수 있게 되어 연산량이 줄어든다.
2. **Explicit Scene Representation**: 3D Gaussian을 Position, Opacity, Convariance로 Explicit하게 설계하고 Color를 Spherical Harmoni 함수로 설계하였다. 
3. **Tile-based Rasterization**: 이미지를 $16 \times 16$ 픽셀의 Tile 형태로 분할하고 Tile마다 각 Gaussian이 Projection 되고, Sorting 되고, Depth 별 Alpha Composition이 되는 구조를 갖는다. Tile 단위로 CUDA에서 병렬처리를 하게 되며 빠른 속도를 낼 수 있는 것이다.

## 2. Overview

세부 개념은 To be Continued..
[[논문 리뷰] 3D Gaussian Splatting 상세 리뷰 - YouTube](https://www.youtube.com/watch?v=Ncs5wOaetYg)

