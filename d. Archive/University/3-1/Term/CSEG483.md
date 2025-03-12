
#### Architecture
Thread < Warp < Thread block < Grid

gridDim: TB의 개수
blockDim: T의 개수
blockIdx: TB idx
threadIdx: T idx

SM
	cache
	Warp scheduler
	register
	shared memory/L1 Cache
	CUDA core

1D
$$\text{현재 thread의 전체 idx} = blockDim.x \times blockIdx.x + threadIdx.x$$

2D
$$\begin{align}
\text{현재 thread의 전체 idx} = gridDom.x \times blockDim.x \times \\
(blockDim.y \times blockIdx.y + threadIdx.y) + \\
blockDim.x \times blockIdx.x + threadIdx.x
\end{align}$$

$$\text{전체 스레드 개수 }= (\text{전체 스레드 개수} + \text{스레드의 스레드 개수} - 1) / \text{스레드의 스레드 개수}$$

한 TB 안에 최대 thread 수는 1024개이다.

Thread들은 다음과 같이 처리된다.
	각각의 TB는 처음에는 1D row-major order로 정렬된다.
	TB안의 정렬된 스레드들은 32-thread warp로 쪼개진다.
	각각의 32-thread warp에는 항상 같은 명령이 수행된다.

$\text{Warp occupancy} = \frac{\text{the number of concurrently active warps}}{\text{the number of maximum possible warps}}$

FP Calculation Cautions
	1. 비슷한 숫자 사이의 뺄셈
	2. 아주 큰 수와 아주 작은 수와의 덧셈/뺄셈
	3. 아주 작은 수로의 나눗셈
	4. 기타

Single-precision floating-point (float) 6~7개 정도의 정밀도
Double-precision floating-point (double) 14 ~ 15개 정도의 정밀

GPU -> GPC -> TPC -> SM

각각의 thread는 SM의 register와 shared memory에 access
	register: thread 마다 사용되는 메모리
	shared memory: Block안의 모든 thread가 공유
	Global memory: Grid

DRAM의 Local, Global, Constant, Texture memory에 access

# 4. Tensor Core

## 4.1 Tensor Core

#### Basics
- FP16: Floating Point 16 bit, FP32: Floating Point 32 bit

#### Tensor Core
> $SM\ (\ Tensor\ Core\times 4,\ RT\ Core\times 1,\ FP64 \times 2\  )$ `GeForce RTX 4090 기준`
- FMA 연산을 한 Clock에 수행 가능하다. $D = A \times B + C$
- Matrix의 Size와 Type이 관건이다.
- FP16 * FP16 + FP32는 16끼리의 연산을 32로 늘려 정밀도를 유지하겠다는 것이다.

#### Type
- Volta: TC 4개, *512* $4 \times 4 \times 4$ FMA (FP16) / *1024* FP (FP16)
- Ampere: TC 8개, *1024* $8\times 4\times 8$ FMA (FP16) / *2048* FP (FP16)

#### CUDA: WMMA API
- CUDA는 FMA 연산을 WMMA API `mma.h` 를 통해서 warp-level로 쪼갠다.

##### 동시에 실행 가능한 명령들
- CPU에서 돌아가는 거
- GPU에서 수행되는 ALU
- H2D
- D2H
- D2D

##### Asynchronous한 명령들
- K
- memcpyAsync

아 근데 input이 다른 kernel 명령의 input으로 들어가면 Async 하게는 안되는 거 같은데?

##### Stream
- 동시에 서로 다른 음료수를 받기 위해 다른 호스에 넣는 거 같은 거

##### Event
- Async끼리 dependency 부여

##### Virtual Memory
- Pageable: Pinned 거쳐가야 함 `malloc()`, `cudaMemcpy()`
- Pinned: Async하게 수행하기 위해선 이거 써야 함 `cudaMallocHost()`, `cudaMemcpyAsync()`

- Sync하기 위해 CPU 잡고 있는 거: `cudaDeviceSynchronize()`
	- Kernel로 pinned memory에 계산 수행하는데 pinned memory는 안 기다려주기 때문에 위 명령어로 기다린 다음 pinned memory에서 다시 device나 host로 `memcpyAsync()`

- Kernel, Memcpy, Memset 명령어들이 있다. 다 Async하다.
- H2D -> K -> D2H(stream n에서 수행) 묶음을 다 끝날 때 까지 기다리고 싶으면 `cudaStreamSynchronize(n)`


- `cudaEventRecord(start, 0);` 0, 죽 default stream을 recording하라고 한 것은 모든 stream을 record하라는 것이다.

##### Parallel Scan
- Inclusive Scan: 내 위치 까지 더하는 것
- Exclusive Scan: 내 위치 전 까지 더하는 것
	- 더 impact 있게 배운 건 이 쪽
	- data, count, addr, data_result array가 있다.
	- data_result의 addr에 count만큼 data의 addr부터 시작해서 copy
	- 여기서 count/addr array의 크기만큼 thread를 띄워서 가속하는 것이다.

##### Iso Value
- Iso Value로 Signe Distance Field를 만든다.
- Iso Value가 커지면 토끼가 부푼다..! 를 상기하자.
- 점 사이의 Iso Value에 해당하는 값을 찾기 위해 다음과 같은 선형 보간 식을 쓴다.
$$P = P_1\frac{|v - v_2|}{|v_2 - v_1|}+P_2\frac{|v - v_1|}{|v_2 - v_1|}$$
- 모양의 종류는 Marching Cube 같은 경우 15가지가 나온다.