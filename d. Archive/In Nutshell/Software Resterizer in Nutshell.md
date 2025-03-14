# Software Rasterizer

## What is rasterizer?
GPU pipline의 첫 단계인 vertex shader의 output을 input으로 받는 두 번째 단계이다.
이 과정은 하드웨어 단계에서 해결이 가능하나, 어떤 일이 일어나는지 알아두면 fragment shader가 하는 일을 이해하기 편하다.

래스터라이저가 하는 일은 크게 5가지로 나뉜다.
- Clipping
- Perspective division
- Back-face culling
- Viewport transform
- Scan conversion

### Clipping
Clipping은 다음과 같은 동작을 수행한다
- View frustum을 투영 변환을 통해 clip space로 바꾼다.
- 공간 경계면 밖으로 일부분이 빠져나오거나 아예 밖에 존재하는 물체들을 clipping(가위치기)한다.
<p align="center">
	<img src="https://sos0911.github.io/assets/2020-03-06-CG7/2020-03-06-CG7_212809.png" width="" height=""/>
	t3와 같이 일부만 공간에 존재한다면 물체와 경계면이 만나는 선을 따라 새로운 정점을 추가하여 새로운 primitive를 만들어 clip해준다.
</p>

### Perspective Division
Perspective division이란 원근법을 구현하는 단계이다.
<p align="center">
	<img src="https://i.stack.imgur.com/q1SNB.png" width="" height=""/>
	Perspective projection과 Orthographic projection
</p>

## 용어 정리
- Primitive:
	Primitive은 기본적인 그래픽 요소를 말한다.
	점, 선, 삼각형 및 사각형과 같은 기본도형으로 이루어져 있으며, 3D 모델을 구성하는 데 사용된다.
- Frustrum:
	Frustrum(절두체)는 카메라가 화상하는 일부의 공간이다. 카메라는 이 frustrum 내부의 좌표들을 화면에 화상한다.
- NDC(Normalize Display Coordinate):
	NDC는 그래픽 시스템에서 화면 좌표를 일반화(Normalize)하여 정규화된 좌표 시스템으로 변환하는 프로세스를 말한다.

## Reference
- <https://blog.naver.com/PostView.naver?blogId=sjyfantasy&logNo=222792889189> (Projection Matrix(투영행렬))
- <https://sos0911.github.io/cg/CG7/> (레스터라이저, 고려대 한정현 교수님 CG 강의록 중)