# 기초 GPU 프로그래밍

# Project 1

#### reduce1 Kernel
#### thrust library
> CUDA에서 제공하는 library 중 하나 

- thrust라는 게 존재하는구나 정도로
- thrust로 reduction을 한 예제 코드를 보면 대충 저렇게 하는 거라는 것을 알 수 있을 것임
- reference: [Overview | Thrust (nvidia.github.io)](https://nvidia.github.io/cccl/thrust/)
- CPU memory allocation -> GPU copy -> GPU SIMD -> CPU copy
- thrust에서 제공하는 함수로 memory allocation을 할 수 있다.
- thrust의 벡터는 c++와 기본적으로 같다.
- `d_A = h_A;`: host memory에서 device memory로 copy를 이렇게 간단하게 할 수 있다.
- `d_th sum = thrust::reduce(d_A.begin(), d_A.end(), 0.0f, thrust::plus<float>())`: host에서 device로 copy하는 것
- 방법에 있는 함수의 format은 바꿔도 상관없다.
- reduce1는 sync 맞춰야 하는데 thrust는 굳이 안 맞춰줘도 괜찮은 것 같다.

#### cuBLAS library
> linear system 계산에 매우 최적화되어있는 라이브러리이다.

# Project 2

4, 7번 제외 코드는 다 강의자료에 있음
강의자료에 빈칸 메꾸기 개념으로다가 하면 됨
4, 7번은 github의 코드 구글링 해서 이해하고 코딩하며 됨
강의자료를 충분히 이해 한 다음 강의자료를 덮고 내가 코딩 해보다가 막히면 강의자료 보는 식으로 하면 이 과제를 충분히 활용하는 것이다.
아마 cuBlas가 제일 빠를 것이다.

누적하는 걸 double

해야 하는 계산
	A * B = C

방법
1. MM_HOST
2. MM_DEVICE_GM
3. MM_DEVICE_SM
4. MM_DEVICE_SM_MWPT
5. MM_DEVICE_TC_GM
6. MM_DEVICE_TC_SM
7. MM_DEVICE_SUBLAS

### Plus Alpha Project

우리가 CUDA를 배우며 알아야 할 중요한 Function은 다음과 같다.
Reduction
Scan(Prifix Sum)
Compact

#### `MarchingCubes.cpp`
> 우리가 실제로 지향해야 할 CUDA의 목적지 같은 코드이다. CUDA를 모르는 사람에게도 설명할 수 있을 수준으로 이해하면 이제 CUDA를 조금 전문적으로 안다고 할 수 있는 것이다.
- Thrust라는 NVDIA에서 제공하는 함수를 통해서 Scan한다.

# Project 3

강의자료 6 2p.부터 참고
GF를 연달아 10번 적용

1. 으아아
- 가우시안 필터링을 구현해보자.
- (a)는 수업시간에 제공한 코드이다.
- (b)와 (c)의 차이는 다음과 같다.
	- 이미지를 $16 \times 16$으로 분할 했다면 grid도 $16 \times 16$으로 분할하ㅕ 하나의 thread가 하나의 pixel을 맡는다.
	- (b) Shared Memory:
		shared memory에 상하좌우로 두 칸씩 더 가서 저장하는 방법
	- (c) More Work Per Thread:
		$16 \times 16$의 Thread가 $64 \times 64$(or more)의 pixel 즉, Thread하나 당 4개의 pixel을 처리하게 하는 것. 최적의 parameter를 찾아보자.

2. 자유연구
- N장으로 구성된(여기서는 이미지가 다 같음) 영상 집합이 있는데 한장 한장 가우시안 필터링을 적용하여 (GF_GLOBAL)
- (a) 영상 보내고 -> 가우시안 적용하고 -> 갖고오고
- (b) stream을 겹치게 하기 (얼만큼 겹치게 할지(M 값)는 내가 정하기)
	- ex) stream을 8개 쓰겠다. 0~7까지 쑤셔넣고 돌아가며 계속 H2D, K, D2H 를 넣던가.. 잘 구현해봐라.
- (d) 자유형, 안해도 됨. (b)를 더 빠르게 하는 방법이 있을까? 를 구상해봐라.
	- ex) 1/3 보내고 처리하고 가져오고 1/3 보내고 처리하고 가져오고 나중에 뭉치고 뭐 이런 방법.. 잘 구현해봐라.

**Plus Aplha**
- 각각의 커널을 만들 때 시간을 오래 돌아가게 하기 위해서 input -> output 이 과정을 11번 수행하라. (silent camera 코드 촬영 사진 참고)
- 보내준 데이터 중 큰 데이터와 작은 데이터로 실험
- 각각의 방법 당 output struct를 추가로 만들고 GLOBAL 만 쓴 결과 SHARED 만 쓴 결과를 jpg나 png로 따로 저장한 뒤 서로 빼면 검은색 이미지가 나와야 하는 걸로 제대로 계산되었는지를 확인한다.
- 2번에 stream을 쓸 때 어떤 방법을 쓸지.. 방법 (c) 같은 shared memory 안 쓴 것을 쓰자. 요는 kernel 도는 시간을 늘려 의미있는 결과값을 내자는 것이다.

