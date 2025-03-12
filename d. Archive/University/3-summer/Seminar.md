
# 2024. 07.26 (金)
#### Into the Lecture & TODO
```
Sponza {
	mirror sphere | statue | mirror sphere
}
```

요즘 추세임
gltf도 기본적으로 이를 쓴다.


Vulkan의 Geometry: TRIANGLEs, AABBs
- AABBs $(x_{min}, y_{min}, z_{min}, x_{max}, y_{max}, z_{max})$
- Intersection Shader를 쓴다.
- Sphere에 해당하는 AABB를 Intersection하는 Shader를 다뤄야한다.

-> 즉, Shpere AABB의 Intersection Shader와 Anyhit Shader를 만드는 것이 당장의 목표

#### `raytracingIntersection.cpp`
```cpp
createBuffers() {
	// 1024개의 구 생성
	// 반지름과 Color는 Random
	// 반지름이 1인 원 안에서 구 4개를 만들고 이를 24배하여 WC로 보냄
	// (샘플링시 자주 쓰이는 방법)
	// 반지름이 1인 구 안에서 Random Uniform Nnumber 생성
	do {
		// 정육면제 안에서 Random Uniform Sample Number 생성
	}
	
	std::vector<AABB> aabbs{};
	// 1024개의 각각의 Sphere에 대해서 (min, max) Array를 쫙 만듦
	
	// Spheres
	createBuffers();
	// ?
}

createBottomAcceleration..() {
	// geometry count = 1
	// SizeInfo
	// GeometryInfo
	// RangeInfo
}
```

`? StagingBuffer가 뭐임`
`? scratchsideBuffer`
`? ASBuffer` - `BottomLevelASBuffer`

각각의 BLAS를 만드는데 Scratch memory도 만들어야되고 AS Buffer도 만들어야된다.
- BLAS - {SC, AS}

여기서의 메모리 최적화
-> `157-163`: 일단 계속 물어보기만해서 가장 큰 값 하나만 만들고 그걸로만 정규화해서 Scratch Memory를 만든다
- 참고 코드: RTXon 코드

#### `Raygen shader`
```

```


CSE4170 강의자료 5 43p.
###### Setup for Viewing Volume
> glm::mat4 perspective(float fovy, float aspect, float zNear, float zFar);
![400](../../../z.%20Docs/img/Pasted%20image%2020240514123644.png)
- `fovy`: 위 아래 각도
- `aspect`: $w/h$ 가로 세로 비율
- `zNear`: 앞 절단 평면까지의 거리
- `zFar`: 뒤 절단 평면까지의 거리

#### $w \times h$ pixel의 $(x, y)$번쨰 pixel

**방법1**
- 코드를 보며 이해해보자.ㅏ
- 보통 중앙에 Ray 하나를 쏘는데, 영화에선 256개까지도 쏜다고 한다 (!!)
- raygen Shader에서는 `LanuchIDEXT.xy`
- 목적: 그 pixel의 Ray점에 해당되는 $1\times 1$ NDC의 위치에 대응되는 WC의 d 방향의 좌표

```
vec4 origin = cam.viewIncerse * vec4(00, 0, 0, 1); // 
vec4 target = cam.projincerse * vec4(..) 
	// -> { x_e/w_c, y_e/w_c, z_e/w_c, 1/w_c }^t
vec4 direction = cam.viewInverse * vec4.. // 
```
- EC의 d는 MV의 역행렬을 곱하면 WC로 감

**방법2**
- 강의자료 방법

되도록 방법1로 하는게 Rasterization과 Raytracing을 통합하기에 좋다.

#### `Intersection Shader`

`PrimitiveID`: 몇번째 게 걸렸는가
우리가 다룰 건 Sphere이므로, 다음 Sphere Intersection을 상기하고 가자.

###### Ray-Object Intersection: Sphere
> Sphere와 Ray의 교차를 계산하는 방법
> $\text{Ray: }p(t)=e+td$
> $\text{Sphere Center: }g = (g_x, g_y, g_z)$

**Method:**
- 필요한 배경 지식은 이차 방정식의 근의 공식과 판별식이다.
- Ray 벡터와 Sphere 중심 벡터로 이차 방정식을 세우고,
- 판별식을 통해 근의 유무를 판별한 뒤,
- 근이 있다면 근의 +, - 성질에 따라 교차점에 해당하는 값을 반환한다.

**Calculate**
![300](../../../z.%20Docs/img/Pasted%20image%2020240722171017.png)
$$
\begin{align}
&(x-g_x)^2+(y-g_y)^2+(z-g_z)^2-r^2 = 0 \\
&(p-g)\circ(p-g)-r^2 = 0
\end{align}
$$
1. 이차 방정식의 형성: $$\begin{align}&(e+td-g)\circ(e+td-g)-r^2 = 0 \\&(d\circ d)t^2+2d\circ(e-g)t + (e-g)\circ(e-g)-r^2 = 0\\&A=d\circ d\ (=1), B = 2d\circ(e-g), C = (e-g)\circ(e-g)-r^2\\&\rightarrow At^2+Bt+C = 0\end{align}$$
2. 근의 계산:
```cpp
D = B*B - 4*A*C; // A = 1 ?
if (D < 0.0) return NO_INTERSECT;
Dsqrt = sqrt(D);
t0 = 0.5*(-B - Dsqrt)/A;  // 이차 방정식의 근 중 큰 값
t1 = 0.5*(-B + Dsqrt)/A;  // 이차 방정식의 근 중 작은 값
if (t0 >= 0) return t0;
if (t1 >= 0) return t1;
return NO_INTERSECT;
```
- 교차점이 있다면 새로운 교차점의 법선 벡터도 새로 계산해주어야 할 것이다. 이는 구형이므로 다소 계산이 단순하다.

3. 법선 벡터 (Normal Vector): $$\begin{align}&p = e+td \\&n = \frac{p-g}{||p-g||} - \frac{p-g}{r} \end{align}$$
**Result**
- $t0, t1\ (t0<t1)$의 유무와 값에 따라 다음과 같은 경우의 수가 있을 수 있다.
	![600](https://www.scratchapixel.com/images/ray-simple-shapes/rayspherecases.png?)
	1. $t0>0$: 교차점은 $t0$이다. $t1$은 고려되지 않는다.
	2. $t0<0,\ t1>0$: 교차점은 $t1$이다. $t0$은 고려되지 않는다.
	3. $t1<0$: Ray의 경로에 물체가 존재하긴 하지만 교차되지 않는다.

여러가지의 Case를 모두 잘 생각하며 코드를 짜자.
```
float sphIntersect(const Sphere s, vec3 ro, vec3 rd) {
	vec3 oc = ro - s.center;
	float b = dot(oc, rd;
	float c = dot(oc, oc - s.radius * s.radius;
	float h = b * b - c;
	if (h < 0.0) {
		return -1.0;
	}
	h = sqrt(h);
	return -b - h;
}
```
-> 강의자료와 코드 flow가 조금 다른데, 이를 확인할 것

#### Intersection Shader & Any-Hit Shader
> AS를 따라가며 둘의 flow 관계를 확인해보자.
> GLSL_EXT_ray_tracing 강의자료 22p. 참고

`bool reportIntersectionEXT()`: intersettion shader에서 교차를 하더라 하고 알려주는 함수
- 교차하면 저 함수 호출한다.
- Intersection이 일어난 지점이 $t_{max}$ 값을 결정한다. 매 순간 $t_{min}, t_{max}$가 있는 것이다. $t_{min}$은 항상 정해져있다. 이를 ray interval이라고 한다.
- 새로 구한 ray Interval이 Current Interval보다 작으면 (여태 찾았던 Interval 보다 작으면) Any-Hit Shader가 수행된다.
- Any-Hit에서 Ignore되지 않으면 hitT값과 hitKind 값이 새로 업데이트 된다.

그니까 Intersection에서는 report 함수만 호출해주면 알아서 해준다는 뜻이다.
앞면에 부딪혔는지 뒷면에 부딪혔는지는 `Closet Shader`에서 `HitKindEXT`를 통해서 알 수 있다.

#### `Any-Hit Shader`
- `ignorelIntersectionEXT`를 통해서 무시를 하던지 안 하던지 둘 중 하나
- `hitAttribute`: 어디를 때렸다는 t 값 외의 정보가 들어가있다. $(s, t, r)$ `Object-Intersection: Triangle 상기`
	- 나중에 만약에 Texture를 쓰는 상황이 오면 그 때는 이 추가 정보들이 필요하다.
	- Texture Mapping 좌표 변환하기 위해서..
	- AABB에서는 딱히 필요 없는 것 같다.


> -> AABB 안에는 뭐든 올 수 있다! 안에 있는 물체에 따라 우리가 Intersection Shader를 잘 제공해줘야 하는 것이다.
