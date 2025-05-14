# Raytracing
> Ray Tracing은 가상적인 광선이 물체의 표면에서 반사되어, 카메라를 거쳐 다시 돌아오는 경로를 계산하는 것이다.

아래는 서강대학교 임인성 교수님이 세미나 형식으로 강의하신 Ray Tracing 강의자료를 공부하고 정리한 것이다.

# 강의자료 1

## 1.1 Introduction to Ray Tracing

#### Offline Ray Tracing
> Offline Ray Tracing은 실시간이 아닌 환경에서 수행되는 Ray Tracing이다. 속도를 포기하고 품질에 초점을 둔다.

#### Real-Time Ray Tracing
> Real-Time Ray Tracing은 빛의 물리적 특성을 실시간으로 시뮬레이션하여 이미지를 생성하는 기술이다. 빛의 반사, 굴절, 그림자, 반사광 등을 포함한 다양한 광학 효과를 자연스럽게 재현할 수 있게 해준다.

**Nvidia OptiX 7**
- Nvidia가 제공하는 **C++** 기반의 고성능 Ray Tracing 프레임 워크이다.
- **RTX GPU**의 하드웨어 가속을 최대한 활용하여 Ray Tracing 연산을 가속화한다.
- 고도로 **Programable**한 Shading 언어를 지원하여 개발자가 자신만의 Shader를 작성하고 다양한 광학 효과를 구현할 수 있게 한다.
- **유연한 API**를 제공하여 다양한 응용 프로그램에서 Ray Tracing을 손쉽게 구현할 수 있다.
- **AI 기술**과 통합하여 Ray Tracing 이미지의 품질을 향상시킬 수 있다.

OptiX 7의 **Computation Pipeline**은 다음과 같다.
![](../../../Z.%20Docs/img/Pasted%20image%2020240715170413.png)

#### 5 Types of Ray Tracing Shaders
> 크게 다른 Shader를 컨트롤하는 **Ray Generation Shader**와,
> Object Shape를 정의하는 **Intersection Shader**와,
> Per-Ray Behavior를 컨트롤하는 **Miss, Clossest-hit, Any-hit Shader**로 나뉜다.
- **Ray Generation Shader**: Define how to start Tracing Rays
- **Intersection Shader(s)**: Define how Rays intersect geometry
- **Miss Shader(s)**: Shading for when rays miss geometry
- **Closest-hit Shader(s)**: Shading at the intersection point
	- Closest-hit Shader는 Reflection, Shadow와 같은 다른 Ray를 생성할 수 있다.
- **Any-hit Shader(s)**: Run once per hit(e.g., for Transparency)

#### Rendering
> 가상의 세상을 가상의 카메라를 이용하여 사진을 촬형하는 적절한 방법을 통하여  2D 이미지를 생성하는 과정
>> Rendering Equation과 Volume Rendering Equation에 의해 기술되는 물리적인 현상을 **실용적인** 렌더링 알고리즘을 사용하여 **효과적으로** 시뮬레이션하여 **사실적인** 영상을 생성한다.

**Rendering Equation** (Surface, 표면 반사만 일어남)
![300](../../../Z.%20Docs/img/Pasted%20image%2020240715152030.png)
$$\begin{align} L_O(x, \vec \omega) &= L_e(x, \vec\omega) + L_r(x, \vec\omega) \\ &= L_e(x, \vec\omega) + \int_\Omega f_r(x, \vec\omega', \vec\omega)L_i(x, \vec\omega')(\vec\omega'\cdot\vec{n})d\vec\omega' \\ &= \text{x가 카메라 방향으로 스스로 내는 빛} + \text{x에서 카메라 방향으로 방사되는 빛}&\end{align}$$
- $L_O(x, \vec\omega)$: Outgoing Radiance
- $L_e(x, \vec\omega)$: Emitted Radiance
- $L_r(x, \vec\omega)$: Reflected Radiance
- $f_r(x, \vec\omega', \vec\omega)$: 물질의 반사 특성과 관련된 식


**Volume Rendering Equation** (Participating Media, 표면 반사와 매질 투과가 일어남)
![450](../../../Z.%20Docs/img/Pasted%20image%2020240715152100.png)
![450](https://dapeng-xu.github.io/html/vis_avs/four_kinds_of_interaction.JPG)
$$\begin{align} L(x, \vec \omega) &= L(x + s\vec\omega, \vec\omega)e^{-\int^s_0\sigma_t(x+s'\vec\omega)ds'} + \int^s_0{L_e(x + s'\vec\omega)e^{-\int^{s'}_0\sigma_t(x+t\vec\omega)dt}ds'}\\ &+\int^s_0\{e^{-\int^{s'}_0\sigma_t(x+t\vec\omega)dt}\sigma_z(x+s'\vec\omega)\int_{\Omega_{4\pi}}p(x+s'\vec\omega, \vec\omega', \vec\omega)L(x+s'\vec\omega, \vec\omega')d\vec\omega'\}ds'\end{align}$$
- $L(x, \vec \omega)$: Outgoing Radiance
- $L(x + s\vec\omega, \vec\omega)$: Incoming Radiance
- $e^{-\int^s_0\sigma_t(x+s'\vec\omega)ds'}$: Extinction due to absorption and out-scattering
- $\int^s_0{L_e(x + s'\vec\omega)e^{-\int^{s'}_0\sigma_t(x+t\vec\omega)dt}ds'}$: Emission
- 나머지 부분: In-scattering

`Plus Alpha: Participating Media`
- 빛과 물질이 상호작용하는 매질(Volume)을 의미한다.
- 공기, 물, 연기, 구름 등과 같은 투명하거나 반투명한 물질을 포함하며, 이 매질 내에서 빛은 픕수되고, 산란되며, 방출될 수 있다.

#### Local and Global Illumination
![600](https://d3i71xaburhd42.cloudfront.net/53a3f2a5f629745643a42658aebd9519e0fbf290/8-Figure1-1.png)

**Local Illumination(LI)**:
- 관찰자와 광원과 물체 간의 직접적인 관계만 고려한다.
- 사실성이 낮은 이미지를 생성하나 계산량이 적다.
- GPU 성능 향상으로 현실감을 높이기 위한 추가적인 조명 효과를 생성하고 있다.
	- Shadow Mapping: 그림자 생성
	- Environmental Mapping: 물체가 주변 환경을 반사하는 것처럼 보이게 하는 기법
	- Ambient Occlusion: Scene의 깊이와 음영을 더 사실적으로 보이게 하는 기법 `그림을 그릴 때 검정색 오버레이로 그림자 보정을 하는 느낌인가보다.`

**Global Illumination(GI)**:
- 관찰자와 광원과 물체 간의 직접적인 관계와 주변의 상황도 고려한다.
- 사실성이 높은 이미지를 생성하나 계산량이 많다.
- 다음과 같은 방법들이 있다.
	- **Path Tracing**: Global Illumination을 시뮬레이션하기 위한 가장 기본적이고 널리 사용되는 방법 중 하나이다.
	- **Whitted-style Ray Tracing**: 클래식한 Ray Tracing 방법으로, 주로 직접 조명과 간단한 반사 및 굴절 효과를 구현한다.
	- **Distributed Ray Tracing**: Ray Tracing의 확장된 형태로, 다양한 랜덤 요소를 도입하여 더 사실적이 효과를 구현한다.
	- **Photon Mapping**: 두 단계로 구성된 Global Illumination 기법으로, Photon을 Scene에 방출하여 조명 정보를 수집한다.
	- **Radiosity**: 주로 면 대 면의 에너지를 교환하는 방식을 사용하여 Scene의 Global Illumination을 계산하는 기법이다.

이제 Global Illumination을 고속으로 구현하려는 것이 **Real-Time Global Illumination**이다. 즉 이를 연구하면 매트릭스 세계관에 한 발짝 더 다가가는 것이다.

#### Frequently Used Rendering Algorithms
- **Rasterization-based Redering**
	- Object Space Method
	- GI를 생성하기엔 자연스럽진 않으나, 실시간 영상이 필요한 곳에 주로 쓰인다.
	- OpenGL, OpenGL ES, Vulkan, DirectX, Metal APIs
- **Ray Tracing**
	- Image Space Method
	- GI를 자연스럽게 생성하며, 주로 실시간이 필요하지 않은 CG산업에 쓰인다.
	- 최근 Real-time 연구가 진행되고 있다.
- **Pixar REYES Architecture**
	- Object Space Method
	- GI를 생성하기에 쉽진 않으나, 주로 실시간이 필요하지 않은 CG산업에 쓰인다.

#### Rasterization vs Ray Tracing
**Rasterization**
- Object Space에서 객체의 변환과 투영을 기반으로 2D 이미지 생성
**Ray Tracing**
- Image Space에서 화면을 통과하는 광선을 추적하여 2D 이미지 생성

#### Ray Tracing in View of Rendering Equation
![300](../../../Z.%20Docs/img/Pasted%20image%2020240715185755.png)


## 1.2 Classical (Whitted-style) Ray Tracing

일단 Whitted-style Ray Tracing 기법으로는 자연스러운 Diffuse Interreflection 효과를 생성하지 못한다.  
기본적으로 Path Tracing 기법을 적용하거나 Photon Mapping, 또는 Radiosity 기법을 통하여 보완을 해야한다.

$$\begin{align} I_{\lambda}(P) &= I_{\lambda}^{local}(P)+k_s\lambda \cdot I_{\lambda}(P_r)+k_{t\lambda}\cdot I_\lambda(P_t) \\ &= \text{광원 빛} + \text{정반사 방향 빛}+\text{굴절각 빛} \\ &=\text{local shading} + \text{reflection} + \text{refraction(transmission)}\end{align}$$
이 중 Relfection과 Refraction의 Child를 **Recursive**하게 돌리며 계산해나간다.

#### Recursive Ray Tracing
> Whitted Ray Tracing에서 다루는 Ray의 종류를 소개한다.

- **Primary Rays (Eye rays)** `E`
	- Ray Tree의 Root 같은 Ray로, 화면의 픽셀을 통해 빛을 눈으로 직접 전달하는 Ray
- **Secondary Rays** `S, R, T`
	- **Shadow Feelers**: 광원으로 부터 직접 오는 Ray
		- **Shadow Rays**: 광원에서 직접 오는 광선, 물체가 광원을 가리는지 여부를 판단하여 그림자를 형성하는 데 사용한다.
		- **Illumination Rays**: 물체 표면에서 반사되거나 투과됭 후 다른 물체에 도달하는 광선, 물체의 색상과 밝기를 결정하는 데 중요한 역학을 한다.
	- **Reflection Rays**: 입사 광선의 방향으로 완벽하게 반사될 수 있는 광선, 반사 표면의 광택과 반사 효과를 구현하는 데 사용된다.
	- **Refraction (Transmission) Rays**: 입사 광선의 방향으로 완벽하게 전달될 수 있는 광선, 투명한 물체를 통과하는 빛의 경로를 계산하여 굴절 효과를 구현하는 데 사용된다.

#### Ray Tree Structure
> 강의자료의 그림 자료를 참고할 것

**Ray Tree Search는 언제 멈출까?**
1. Ray가 물체와 교차하지 않을 때
2. 주어진 Maximum Bounce Depth에 이르렀을 때
3. 현재의 Ray의 영향이 미미할 때

#### Recursive Computation
> Whitted Ray Tracing에서 실제 Ray의 계산은 어떻게 이루어질까?

**어떤 입사 광선이 고려될까?**
1. Shadow Feelers, 광원에서 직접 들어오는 빛
2. Reflection Rays, 정반사 방향으로부터 들어오는 빛
3. Refraction Rays, 굴절각 방향으로부터 들어오는 빛

**Ray Tracing 기법의 문제점은 무엇일까?** `GPT`
- **간접 조명 (Indirect Illumination)**:
	- 전통적인 Ray Tracing인 Whitted Ray Tracing은 간접 조명을 잘 처리하지 못한다. 즉, 물체 표면에서 다른 표면으로 반사된 빛을 고려하는 데 한계가 있다.
	- 간접 조명은 주로 Global Illumination 기법에서 처리되며, 이는 Path Tracing이나 Photon Mapping과 같은 더 복잡한 알고리즘이 필요하다.
- **산란 (Scattering):**
	- Ray Tracing은 주로 표면에서의 반사와 굴절만을 처리하며, 매질 내부에서의 산란 (예: 구름이나 연기와 같은 반투명 물질 내부의 빛 산란)은 처리하지 않는다.
- **광학적 두께 (Optical Thickness)**:
	- 표면이 아닌 매질의 광학적 특성, 예를 들어 공기나 물과 같은 매질을 통과하는 빛의 흡수와 산란 등은 전통적인 Ray Tracing에서 직접적으로 고려되지 않는다.

**Recursive Formula**
![300](../../../Z.%20Docs/img/Pasted%20image%2020240719133432.png)
$$\begin{align} I_{\lambda}(P) &= I_{\lambda}^{local}(P)+k_s\lambda \cdot I_{\lambda}(P_r)+k_{t\lambda}\cdot I_\lambda(P_t)\end{align}$$
- $I_{\lambda}(P)$: 
- $I_{\lambda}^{local}(P)$: Shadow Feelers, 광원에서 직접 들어오는 빛
- $I_{\lambda}(P_r)$: Reflection Rays, 정반사 방향으로부터 들어오는 빛
- $I_\lambda(P_t)$: Refraction Rays, 굴절각 방향으로부터 들어오는 빛
- $P_r$: 정반사 방향에 있는 점
- $P_t$: 굴절각 방향에 있는 점
- $N$: 점 $P$ 표면에 수직인 normal vector
- $R$: 정반사 방향으로의 vector
- $T$: 굴절각 방향으로의 vector

#### Ray Generation
> 가상의 Ray를 만들어보자.
> $p(t, e, d) = p(t) = e + td$

먼저 CSE4170에서 배웠던 내용을 복기하며 다음 내용을 보자.

![350](../../../Z.%20Docs/img/Pasted%20image%2020240719142857.png)
`gluLookAt(*)`의 결과로 다음과 같은 output이 나온다.
$$\begin{align}e &= (e_x, e_y, e_z) \\ u &= (u_x, u_y, u_z) \\ v &= (v_x, v_y, v_z) \\ n &= (n_x, n_y, n_z)\end{align}$$
- $e$: 카메라(눈)의 위치
- $u$: 카메라(눈)의 오른쪽 벡터
- $v$: 카메라(눈)의 위쪽 벡터
- $n$: 카메라(눈)의 뒤쪽 벡터

이렇게 설정된 **카메라**(눈)에서 Focal Length $d$ 만큼 떨어진 거리에 **가상의 창**이 있다고 하자. 그 창의 **정 중앙의 좌표**를 다음과 같이 나타낼 수 있다.
$$w_c = (w_x, w_y w_z)$$

그 다음 `gluPerspective(fovy, aspect, *, *)`을 통해 다음과 같은 output을 얻는다.
$$\begin{align}&w_w\\ &w_h\\ &a=(a_x, a_y, a_z)\\&b = (b_x, b_y, b_z)\\ &c = (c_x, c_y, c_z)\end{align}$$
- $w_w, w_h$: 가상의 창의 width, height $\text{Image Resolution} = w\times h$
- $a$: 여기서 $a$는 카메라 공간을 형성하는 위가 잘린 피라미드 형태에서 원평면(멀리 있는 평면으로, 카메라와 이 평면까지의 거리는 $d$이다.)의 왼쪽 아래 코너의 점이라고 생각하자.
- $b, c$: 이는 일단 넘어가자.

그 다음 `glViewport(*, *, w, h)`를 통해서 **가상의 창의 i, j 번째 pixel**을 가져온다.
최종 output은 가상의 창 위의 한 점으로, 다음과 같다.
$$s = (s_x, s_y, s_z)$$

#### Ray Calculation for (i, j)-th Image Pixel
> 이제 위의 과정을 실제 식으로 나타내보자.

![300](../../../Z.%20Docs/img/Pasted%20image%2020240719142857.png)![300](../../../Z.%20Docs/img/Pasted%20image%2020240514123644.png)
$$\begin{align} &e, u, v, n \\
&w_c = e - d \cdot n \\
&w_h = 2d \tan({\frac{fovy}{2}}), w_w = aspect \cdot w_h \\
&a = w_c - \frac{w_w}{2}u - \frac{w_h}{2}v \\
&s = a+(i + 0.5)\frac{w_w}{w}u+(j + 0.5)\frac{w_h}{h}v \\
&d = \frac{s-e}{||s-e||}=(d_x, d_y, d_z) \\
&p(t) = e + td = (e_x + t \cdot d_x, e_y + t \cdot d_y, e_z + t \cdot d_z)\end{align}$$
- 가상의 창의 정 중앙의 좌표 $w_c$를 구하고, 그 창의 width $w_w$와 height $w_h$를 구한다음,
- 위에서 구한 $w_c, w_w, w_h$로 창의 왼쪽 아래 코너의 점 $a$를 구한다.
- $\frac{w_w}{w}, \frac{w_h}{h}$: $w_w \times w_h$ 크기의 창을 $w \times h$ pixel로 나눈 것이다. $+ 0.5$를 하는 이유는 픽셀의 정 중앙을 나타내려고 하는 것이다.
- 이로써 $(i, j)$ 번째 픽셀의 좌표 $s$가 구해졌다.
- 마지막으로 $e$에서 $s$를 가리키는 벡터를 정규화한 $d$로 (그림 상의 초록색 벡터) 화면 속의 물체 위의 한 점 $p$에 쏜 Ray를 $p(t) = e + td$ 로 나타낼 수 있다.
- 여기서 $t$의 값이 커질 수록 Ray의 길이가 늘어난다고 생각하면 될 것 같다.

이로써 우리는 **Primary Ray**를 구하였다.
```cpp
for (j = 0; j < h; j++) {
	// for each scan line
	for (i = 0; i < w; i++) {
		// Trace current ray
	}
}
```

#### Simple Ray Caster
> 이제 간단한 Ray Caster의 실제 코드를 살펴보자.

$$\begin{align} &e, u, v, n \\
&w_c = e - d \cdot n \\
&w_h = 2d \tan({\frac{fovy}{2}}), w_w = aspect \cdot w_h \\
&a = w_c - \frac{w_w}{2}u - \frac{w_h}{2}v \\
\end{align}$$
```cpp
typedef struct {
	float e[3], u[3], v[3], n[3];
	float fovy, aspect; 
} CAMERA;

CAMERA cam;

typedef struct {
	int w, h;
	float d;
	float wc[3], wh, ww;
	float a[3];
	unsigned char *pixmap;
} WINDOW;

WINDOW win;

void set_window(int w, int d) {
	int i;
	
	win.w= w; win.h= d;
	
	win.pixmap= (unsigned char *)
		malloc(win.w*win.h*3*sizeof(unsigned char));
		// 3 곱한건 RGB로 된 pixmap이니까
	
	win.d= 1.0; // d = 1.0이라 가정
	
	for (i= 0; i< 3; i++ )
		win.wc[i] = cam.e[i] -win.d*cam.n[i]; // w_c
		
	win.wh= 2.0*win.d*tan(cam.fovy/2.0); // w_h
	win.ww= cam.aspect*win.wh; // w_w
	
	for (i= 0; i< 3; i++ ) {
		win.a[i] = win.wc[i] win.ww*cam.u[i]/2.0 -win.wh*cam.v[i]/2.0;
		// 왼쪽 아래 코너 좌표 a
		win.b[i] = cam.u[i]; // 카메라 오른쪽 벡터
		win.c[i] = -cam.v[i]; // 카메라 위쪽 벡터
	}
}
```

$$\begin{align}
&s = a+(i + 0.5)\frac{w_w}{w}u+(j + 0.5)\frac{w_h}{h}v \\
&d = \frac{s-e}{||s-e||}=(d_x, d_y, d_z) \end{align}$$
```cpp
Typedef struct {
	float eye[3], dir[3]; 
} RAY;

RAY ray;

void compute_ray_dir(int i, int j) {
	int k;
	float tmp[3], leng;
	
	for (k = 0; k < 3; k++)
		tmp[k] = win.a[k]  // s 좌표
				+ (i + 0.5) * win.ww * cam.u[i]/win.w
				+ (j + 0.5)* win.wh * cam.v[i]/win.h- cam.e[k];
		leng = sqrtf(tmp[0]*tmp[0] + tmp[1]* tmp[1] + tmp[2]*tmp[2]);
		// d 정규화를 위한 식
		
	for (k = 0; k < 3; k++) ray.dir[k] = tmp[k]/leng; // d 방향
} 
```

$$p(t) = e + td = (e_x + t \cdot d_x, e_y + t \cdot d_y, e_z + t \cdot d_z)$$
```cpp
void main(void) {
	…
	set_object();
	set_camera();
	set_window();
	
	ray.eye[0] = cam.e[0], ray.eye[1] = cam.e[1], ray.eye[2] = cam.e[2];
	
	for (j = 0; j < win.h; j++) {
		for (i = 0; i < win.w; i++) {
			compute_ray_dir(i, j); // d 계산
			ray_hit(&which_obj, &t);  // 여기서 p(t)가 들어가는 듯
			shade_and_store(which_obj, t, i, j); 
		}
		printf("\nRAY_CASTER: DONE with scanline no. %d ...\n", j);
	}
	dump_pixmap();
}
```

이제 Ray를 쏠 준비, 즉 **Primary Ray**의 세팅은 끝났다. 이제 실제로 Local Shading과 Reflection, 그리고 Refraction을 계산해보자.

#### Computation of Recursive Formula
> $\begin{align} I_{\lambda}(P) &= I_{\lambda}^{local}(P)+k_s\lambda \cdot I_{\lambda}(P_r)+k_{t\lambda}\cdot I_\lambda(P_t)\end{align}$ 각각의 인자의 계산

**Computation of $I_{\lambda}^{local}(P)$**
- **Phong의 Local Illumination Model**을 사용하여 계산한다. 자세한 것은 CSE4170 - Phong Illumination model 변형3, HW4 참고.
- *Phong의 Loacl Illumination Model*이다. Phong Shader가 아님을 주의하자.
$$I_\lambda = I_{a\lambda} \cdot k_{a\lambda}+ \sum\limits_{i=0}^{m-1} S_i \cdot f_{att}(d) \cdot I_{l\lambda} \cdot \{ k_{d\lambda} \cdot (N \circ L)+k_{s\lambda} \cdot (N \circ H)^n \}$$
$$\begin{align}S_i = \begin{cases}1, \text{if the ith light source is visible,} \\ 0, \text{otherwise.}\end{cases} \\
S_i = \begin{cases}1, \text{if the ith light source is visible,} \\
0-1, \text{otherwise.}\end{cases} \end{align}$$

**Computation of $I_{\lambda}(P_r)$**
- 입사각 $V$와 법선 벡터 $N$ 반사각 $R$은 다음과 같은 관계를 가진다.
$$R=2(V \circ N)N - V$$
- 마치 $p(t, P, R)$, 즉 반사각으로 튕겨져 나가는 Ray가 Primary Ray(Eye Ray)인 것처럼 가정하고 Recursive하게 $I_{\lambda}(P_r)$를 계산한다.
- $I_{\lambda}(P_r)$에 해당 광선이 지나온 거리에 대하여 Light Attenuation 효과(빛의 감쇠 효과)를 적용하기도 한다.

**Computation of $I_\lambda(P_t)$**
- 방식은 위와 같다. 다만 굴절각 $T$를 구하는 방식이 특별하다. `Snell's Law`
- 마치 $p(t, P, R)$ 즉 투영각으로 나가는 Ray가 Primary Ray(Eye Ray)인 것처럼 가정하고 Recursive하게 $I_\lambda(P_t)$를 계산한다.
- **Snell's Law**:
	![250](../../../Z.%20Docs/img/Pasted%20image%2020240719170357.png)
	- 빛이 한 매질에서 다른 매질로 진행할 때, 빛의 굴절 현상을 설명하는 법칙이다. 이 법칙은 빛의 입사각과 굴절각 사이의 관계를 설명하며, 빛이 두 매질의 경계면에서 굴절되는 각도를 계산하는 데 사용된다.
	- Snell's Law는 다음과 같이 수학적으로 표현된다.$$\begin{align}n_i\sin\theta_1 = n_2\sin\theta_2 \\ \frac{\sin\theta_1}{\sin\theta_2} = \frac{\eta_2}{\eta_1} = \eta_{21}\end{align}$$
	- $n_1, n_2$: 첫 번째 매질과 두 번째 매질의 굴절률(refractive index)
	- $\theta_1$: 입사각(incident angle), 첫 번째 매질의 법선에 대한 입사 광선의 각도
	- $\theta_2$: 굴절각(refracted angle), 두 번째 매질의 법선에 대한 굴절 광선의 각도
	- $\eta_{i}$: 진공에 대한 매질 $i$의 굴절률
- **Calculate $T$ with Snell's Law**
	![250](../../../Z.%20Docs/img/Pasted%20image%2020240719171832.png)![300](https://redirect.cs.umbc.edu/~rheingan/435/pages/res/illum/illum16e.gif)
	- 굴절각 $T$를 구하는 식은 다음과 같다.$$\begin{align}&T = \sin{\theta_2}M - \cos{\theta_2}N \\ &I_{perp} = I + \cos{\theta_1}N = I + c_1N \ \ (c_1 \equiv \cos{\theta_1}) \\ &M = \frac{I_{perp}}{||I_{perf}||} = \frac{I+c_1N}{\sin{\theta_1}} \\ &T = \frac{\sin{\theta_2}}{\sin{\theta_1}}(I+c_1N) - \cos{\theta_2}N = \eta I + (\eta c_1 - c_2)N \\ &\text{where } \eta \equiv \frac{\sin{\theta_2}}{\sin{\theta_1}} = \frac{n_1}{n_2} \text{ and } \\ &c_2 \equiv \cos{\theta_2} = \sqrt{1 - \sin^2{\theta_2}} = \sqrt{1 - \eta2\sin^2{\theta_1}} = \sqrt{1 - \eta^2(1 - c^2_1)}\end{align}$$
	- 즉 필요한 값은 두 매질 사이의 굴절률 $\eta_i$의 비율(ratio) $\eta$와, 입사각$\theta_1$과 굴절각 $\theta_2$이다.

#### Total Internal Reflection (TIR)
> 빛이 고굴절률 매질에서 저굴절률 매질로 진행할 때 특정 조건에서 발생하는 현상으로, 빛이 매질 경계를 넘지 못하고 완전히 반사되는 현상
>> Ray Tracing에선 $T$ Ray와 관련된 특정 현상이라고 생각하면 된다.

![250](../../../Z.%20Docs/img/Pasted%20image%2020240719184622.png)  ![](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSgoVJAlUaOi_Zguzjj_RGfIivM3fIdGZJ52j2znk6-7y4SC9o4q-5A_0dM-DP_zrAtZVE&usqp=CAU)
먼저 Snell's Law의 공식을 상기해보자.
$$\frac{\sin\theta_1}{\sin\theta_2} = \frac{\eta_2}{\eta_1} = \eta_{21}$$

**Critical Angle(임계각)**: $\theta^c_1 = \sin^{-1}(\frac{n_2}{n_1})$
- $\eta_1 > \eta_2$일 때, 임계각을 넘어가는 순간 TIR 현상이 일어난다. (빨간색 화살표)
- 이를 식으로 정리해보면 다음과 같다.$$\begin{align}\sin{\theta_2} = \frac{\eta_1}{\eta_2}\sin{\theta_1}&>1 \\
\sin{\theta_1 }&> \frac{\eta_2}{\eta_1} \\
\theta_1 &> \sin^{-1}(\frac{\eta_2}{\eta_1}) \\ 
\theta_1 &> \theta^c_1\end{align}$$

#### Fresnel Equations
> 빛이 서로 다른 굴절률을 가진 두 매질의 경계면에 입사할 때, 반사와 굴절되는 빛의 세기를 계산하는 데 사용된다.
>> 즉, Ray Tracing에선 $R$와 $T$ Ray의 세기, $t$를 계산하는 데 사용되는 것이다.

[reference 1](http://hyperphysics.phy-astr.gsu.edu/hbase/phyopt/freseq.html)
[reference 2](https://blog.demofox.org/2017/01/09/raytracing-reflection-refraction-fresnel-total-internal-reflection-and-beers-law/)

**Fact**
- 빛이 경계면에 도착하면 일부는 반사되고 일부는 투과된다.
- 반사율을 $R$이라 하고 투과율을 $T$라 했을 때, $R+T = 1$이 성립한다.

**Fresnel Equations**
- 서로 다른 광학 매질 사이의 경계에 입사할 때 빛의 반사와 투과를 설명한다.
- 빛의 편광 상태에 따라 두 가지 방정식으로 나뉜다.
	1. **수직 편광 (s-polarized light)**: $$R_{\perp}(\theta_1) = \{r_{\perp}(\theta_1)\}^2 = \{\frac{\eta_1\cos{\theta_1}-\eta_2\cos{\theta_2}}{\eta_1\cos{\theta_1}+\eta_2\cos{\theta_2}}\}^2$$
	2. **수평 편광 (p-polarized light)**: $$R_{\parallel}(\theta_1) = \{r_{\parallel}(\theta_1)\}^2 = \{\frac{\eta_2\cos{\theta_1}-\eta_1\cos{\theta_2}}{\eta_2\cos{\theta_1}+\eta_1\cos{\theta_2}}\}^2$$
- Ray Tracing에선 단순하게 **두 식의 평균**으로 구해진다.$$\begin{align}R(\theta_1) &= \frac{R_{\perp}(\theta_1) + R_{\parallel}(\theta_1)}{2}\ or\ \{\frac{r_{\perp}(\theta_1) + r_{\parallel}(\theta_1)}{2}\}^2 \\ T(\theta_1) &= 1 - R(\theta_1)\end{align}$$
- 물론 TIR 현상이 일어날 경우, $R(\theta_1) = 1$이 되므로 Fresnel Equation을 적용할 필요가 없을 것이다.

이를 바탕으로 다음 그래프를 보자.
![600](../../../Z.%20Docs/img/Pasted%20image%2020240719192135.png)
잘 보면 엄청 작은 점이 보일 것이다. 그것이 바로 Schlick의 근사를 이용해 근사한 것이다.
- $\eta_1 = 1, \eta_2 = 1.33$:
	- 대표적인 예로 `공기 -> 물`이 있다.
	- 물을 비스듬히 보면 더 잘 반사되어 보이는 현상을 상상해보자.
	- 핸드폰 화면을 비스듬하게 봐도 관찰이 가능한 현상이다.
- $\eta_1 = 1.33, \eta_2 = 1$:
	- 대표적인 예로 `물 안쪽 -> 공기`가 있다.
	- Critical Angle $49\degree$를 넘어가면 100% 반사되는 것을 볼 수 있다.

#### Schlick's Approximation to Fresnel Equation
> Fresnel 방정식의 반사율을 간단하게 근사하는 방법으로 Real-time Rendering 환경에서 자주 사용된다.
> Fresnel 반사 계수를 계산하는 데 필요한 복잡한 수식을 단순화하여 계산 비용을 줄이면서도 현실감 있는 결과를 제공한다.

**Calculation**
- Schlick의 근사식은 다음과 같다:$$\begin{align}R_0 &= (\frac{\eta_1 - \eta_2}{\eta_1 + \eta_2})^2\\
R_{Schlick}(\theta_1) &= R_0 + (1 - R_0)(1 - C)^5 \end{align}$$
- 이를 다음과 같이 경우를 나누어 $No$인 경우 $C$ 값을 설정하고 근사식을 수행한다:$$
\begin{CD}
	\eta_1 > \eta_2? @>>No> C = \cos{\theta_1} \\
	@VVYesV \\
	TLR\text{ occurs?} @>>No> C = \cos{\theta_2} \\ 
	@VVYesV\\
	R_{Schlick}(\theta_1) = 1
\end{CD}
$$
- 다음과 같이 $\eta_2$가 증가하면 근사치가 부정확해진다.
	![250](../../../Z.%20Docs/img/Pasted%20image%2020240719200213.png)

#### A Simple Whitted-style Ray Tracer
> 이제 위에서 공부한 것을 바탕으로 간단한 Whitted-style Ray Tracer를 작성해보자.

```cpp
void TraceRay(start, dir, depth, color)
point start, dir;
int depth;
colors *color;
{
	int ray_hit(); // First-hit를 계산한다. (Ray Intersection)
	point hit_point, refl_dir, trans_dir; // point P, vec R, vec T
	colors local_color, refl_color, trans_color; // S Ray, R Ray, T Ray
	object hit_object;
	
	if (depth > MAXDEPTH) *color = BLACK;
	// Maximum Bounce Depth에 이르렀을 때 Ray Tree Searching 멈추기 (Ray == 검정)
	// 보통 Max Bounce = 2~3 정도이다.
	else {
		if (ray_hit(start, dir, &hit_object, &hit_point)) {
		// 이 부분이 강의자료 2에서 배울 Ray Intersection에 해당하는 부분이다.
		// 이 ray_hit()에서 Ray Tracing의 대부분의 시간이 소요된다.
		// Ray가 충돌하면 Ray 계산
			shade(hit_object, hit_point, &local_color);
			// Shadow Feeler S 계산 (보통 Phong의 Local Illumination Model)
			calculate_reflection(hit_object, hit_point, &refl_dir);
			// Reflection Ray R 계산
			calculate_transmission(hit_object, hit_point, &trans_dir);
			// Refraction Ray T 계산
			TraceRay(hit_point, refl_dir, depth+1, &refl_color); // R Ray 재귀
			TraceRay(hit_point, trans_dir, depth+1, &trans_color); // T Ray 재귀
			Combine(hit_object, local_color, refl_color, trans_color, color);
			// 최종적으로 Recursive Formula를 수행한다.
		}
		else *color = BACKGROUND_COLOR;
		// Ray가 충돌하지 않으면 배경색으로 Ray 결정 (Ray == 배경색)
	}
}

// 물질의 성질에 따라 TraceRay를 선택적으로 수행하는 조금 더 효율적인 Ray Tracer
void TraceRay(start, dir, depth, color)
point start, dir;
int depth
colors *color;
{
	int ray_hit();
	point hit_point, refl_dir, trans_dir;
	colors local_color, refl_color = 0, trans_color = 0;
	object hit_object;
	
	if (depth > MAXDEPTH) *color = BLACK;
	else {
		if (ray_hit(start, dir, &hit_object, &hit_point)) {
			LocalShade(hit_object, hit_point, &local_color);
			
			if (hit_object->material.ks > 0) { // 오브젝트에 정반사 비율 존재
				calculate_reflection(hit_object, hit_point, &refl_dir);
				TraceRay(hit_point, refl_dir, depth+1, &refl_color);
			}
			if (hit_object->material.kt > 0) { // 오브젝트에 투과성질 존재 (투명한 물체)
				calculate_transmission(hit_object, hit_point, &trans_dir);
				TraceRay(hit_point, trans_dir, depth+1, &trans_color);
			}
			Combine(hit_object, local_color, refl_color, trans_color, color);
		}
		else *color = BACKGROUND_COLOR;
	}
}

main() {
	…
	for (i = 0; i < width, i++)
		for (j = 0; j < height, j++) {
			calculate_primary(eye, i, j, window, dir);
			// 화면의 각 Pixel에 Ray를 쏜다. 즉, Primary Ray를 생성한다. (dir 결정)
			TraceRay(eye, dir, 0, &colorbuffer[i][j]); //Ray 계산
		}
	…
}
```
- first-hit이란, ray를 쏘고 object를 ray가 투과해나갈 때 가장 먼저 부딪히는 지점을 계산하는 것을 말한다.

# 강의자료 2

이 부분을 상기하고 시작하자.

**5 Types of Ray Tracing Shaders**
1. **Ray Generation Shader**: Define how to start Tracing Rays
2. **Intersection Shader(s)**: Define how Rays intersect geometry
3. **Miss Shader(s)**: Shading for when rays miss geometry
4. **Closest-hit Shader(s)**: Shading at the intersection point
5. **Any-hit Shader(s)**: Run once per hit(e.g., for Transparency)

우리는 강의자료 1에서 Primary Ray를 생성하는 **Ray Generation Shader**와,  
Local, Reflection, Refraction Ray와 관련된 **Miss, Clossest-hit, Any-hit Shader**에 대해서 배웠다.  
색깔 계산은 배웠는데, Ray가 특정 방향으로 날라가면 특정 물체와 부딪힐지 말지 아는 방법은 아직 안 배웠다.  
이제 Ray Tracing에서 대부분의 시간을 차지하는 **Intersection Shader**에 대해서 배워보자.

## 2.1 Ray-Object Intersection

**Types of Ray-Object Intersection** `ㄹㅇ 많다`
1. Sphere
2. Rectangle
3. Parallelogram
4. Triangle
5. Convec Planar Polygon
6. Axis-Aligned Bounding Box

#### Ray-Object Intersection: Sphere
> Sphere와 Ray의 교차를 계산하는 방법
> $\text{Ray: }p(t)=e+td$
> $\text{Sphere Center: }g = (g_x, g_y, g_z)$

**Method:**
- 필요한 배경 지식은 이차 방정식의 근의 공식과 판별식이다.
- Ray 벡터와 Sphere 중심 벡터로 이차 방정식을 세우고,
- 판별식을 통해 근의 유무를 판별한 뒤,
- 근이 있다면 근의 +, - 성질에 따라 교차점에 해당하는 값을 반환한다.

**Calculate**
![300](../../../Z.%20Docs/img/Pasted%20image%2020240722171017.png)
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

#### Ray-Object Intersection: Rectangle
> Rectangle과 Ray의 교차를 계산하는 방법
> $\text{Ray: }p(t)=e+td$

내적의 다음 성질을 살짝 상기하고 가자.
![150](https://upload.wikimedia.org/wikipedia/commons/thumb/3/3e/Dot_Product.svg/220px-Dot_Product.svg.png)

**Method**
- 먼저 평면 상의 벡터이며, 서로 수직인 벡터 $u, v$로 평면 상의 법선 $n$을 구한다.
- 법선 $n$과 평면 상의 코너 점 $a$에서 교차점 $p$ 방향의 벡터를 내적하면 수직이므로 0이 나오는 것을 이용해 평면을 나타내는 Plane Equation을 세우고,
- Plane Equation의 $p$에 Ray 함수를 대입하여 방정식을 계산한다.

**Calculate**
![400](../../../Z.%20Docs/img/Pasted%20image%2020240722171042.png)
- 법선 벡터 (Normal Vector): $$n = \frac{u\times v}{||u \times v||},\ (u \circ v = 0)$$
- 방정식의 형성: $$\begin{align}&\text{Plane equation: } n\circ(p-a) = n \circ p - n \circ a = 0 \\ &\text{Ray-plane intersection: } n\circ (e+td)-n\circ a = 0 \\&\rightarrow (n\circ d)t = n \circ (a-e) \\\end{align}$$
- 방정식의 계산:
$$
\begin{align}
&\text{if } (n \circ d == 0) \text{\quad \{ return NO\_INTERSECTION; \}} \\
&\text{else \quad} t = \frac{n \circ (a-e)}{n \circ d}; \\ \\
&\text{if } (t < 0)\text{\quad\{ return NO\_INTERSECTION; \}} \\
&\text{else \quad} p = e + td; \\ \\
&\alpha = (p-a) \circ u,\ \beta = (p - a) \circ v; \\ \\
&\text{if } ((0 \leq \alpha \leq u \circ u) \text{ \&\& } (0 \leq \beta \leq v \circ v)) \text{\quad\{ return (t, n); \}} \\
&\text{else \quad return NO\_INTERSECTION;} \\
\end{align}
$$
**Result**
- Ray와 Rectangle이 교차하지 않는 경우:
	1. $n\circ d$: 평면과 Ray가 평행한 경우
	2. $t<0$: 이미 Ray가 평면을 지나있는 경우
	3. $(0 \leq \alpha \leq u \circ u) \text{ \&\& } (0 \leq \beta \leq v \circ v)$: 교점이 Rectangle 범위 밖인 경우
- Ray와 Rectangle이 교차하는 경우:
	- 그 외 모든 경우

#### Ray-Object Intersection: Parallelogram
> Parallelogram(평행사변형)과 Ray의 교차를 계산하는 방법

**Method**
- $u, v$가 서로 수직이 아니라는 점만 빼면 기본 방정식을 세우는 방법은 Rectangle과 동일하다.
- 다만 마지막에 교차점과 평행사변형의 관계를 분석하는 부분이 Rectangle과는 다를 뿐이다.
- 또한 3차원 -> 2차원 투영시 최적화 개념도 나오니 이 부분도 잘 보고 가도록 하자.

**Calculation**
![400](../../../Z.%20Docs/img/Pasted%20image%2020240722213000.png) ![](../../../Z.%20Docs/img/Pasted%20image%2020240725173622.png)
- 법선 벡터 (Normal Vector): $$n = \frac{u\times v}{||u \times v||},\ (u \circ v \neq 0)$$
- 방정식의 형성: $$\begin{align}&\text{Plane equation: } n\circ(p-a) = n \circ p - n \circ a = 0 \\ &\text{Ray-plane intersection: } n\circ (e+td)-n\circ a = 0 \\&\rightarrow (n\circ d)t = n \circ (a-e) \\\end{align}$$
- 방정식의 계산:
$$
\begin{align}
&\text{if } (n \circ d == 0) \text{\quad \{ return NO\_INTERSECTION; \}} \\
&\text{else \quad} t = \frac{n \circ (a-e)}{n \circ d}; \\ \\
&\text{if } (t < 0)\text{\quad\{ return NO\_INTERSECTION; \}} \\
&\text{else \quad} p = e + td; \text{\quad (여기까지는 Rectangle과 동일)}\\ \\
&\alpha u+\beta v = p - a \text{ for unknowns } \alpha \text{ and } \beta; \\
&\text{Build a } 2\times 2 \text{ linear system and solve it for } \alpha \text{ and } \beta; \\ \\
&\text{if } ((0 \leq \alpha \leq 1) \text{ \&\& } (0 \leq \beta \leq 1)) \text{\quad\{ return (t, n); \}} \\
&\text{else \quad return NO\_INTERSECTION;} \\
\end{align}
$$
	- 세 번째 부분에서 $\alpha$와 $\beta$의 값을 알려면 벡터의 $x, y, z$ 세 요소 중 두 요소만 가지고 계산해도 충분하다. (3차원 -> 2차원으로의 투영이기 때문이다.)
	- 여기서 $u \times v$의 component 중 **절댓값이 가장 큰 요소를 빼고** 방정식을 세우면 계산의 안전성이 올라간다.
	- 그 이유는 3차원 -> 2차원 투영 시 가급적 평면 방향과 유사한 평면으로 투영해야 수치적 안정성이 올라가기 때문이다.

**Result**
- Ray와 Rectangle이 교차하지 않는 경우:
	1. $n\circ d$: 평면과 Ray가 평행한 경우
	2. $t<0$: 이미 Ray가 평면을 지나있는 경우
	3. $(0 \leq \alpha \leq 1) \text{ \&\& } (0 \leq \beta \leq 1)$: 교점이 Parallelgram 범위 밖인 경우
- Ray와 Rectangle이 교차하는 경우:
	- 그 외 모든 경우

`?: Rectangle / Parallegram u & v`
- 왜 Rectangle에선 normal vector라고 가정 안 하는데 Parallegram에선 normal vector라 가정함?

#### Ray-Object Intersection: Triangle
> Triangle과 Ray의 교차를 계산하는 방법
> $\text{Ray: }p(t)=o+td$ `Using the notation from PBR by Pharr & Humphreys`
> $\text{Triangle: }P = (p_0, p_1, p_2) \ where\ p_i = (p_{ix}, p_{iy}, p_{iz})$

먼저 시작하기에 앞서, Barycentric Coordinates의 개념을 알아보도록 하자.

##### Barycentric Coordinates
>> $p(t)$에서 normal을 얻는 방법

다음과 같이 세 변이 각각 $(p_0, p_1, p_2)$인 삼각형과 그 속에 점 $p$가 있다고 하자.
![160](../../../Z.%20Docs/img/Pasted%20image%2020240725181650.png)
- Barycentric Coordinates에선 점 $p$를 삼각형의 세 꼭짓점 $(p_0, p_1, p_2)$로 나타낸다.
- $p = s\times p_0 + t \times p_2 + r\times p_2$로, $s+t+r = 1$이다.
- 표기: $p(s, t, r)$또는 $p(s, t, 1-s-t)$또는 $p(s, t)$, 여기서는 $p(s, t)$를 쓰는 듯 하다.

$s, t, r$의 의미는 $p$를 기준으로 나뉘는 삼각형 $P$ 속의 방향성이 있는 면적의 비율이다.
![160](../../../Z.%20Docs/img/Pasted%20image%2020240725182427.png)
- $s=\frac{Area(p, p_1, p_2)}{Area(p_0, p_1, p_2)}$
- $t=\frac{Area(p, p_2, p_0)}{Area(p_0, p_1, p_2)}$
- $r=\frac{Area(p, p_0, p_1)}{Area(p_0, p_1, p_2)} = 1-s-t$

삼각형 $P = (a, b, c)$의 면적
- $Area(a, b, c) = \frac{||(b-a)\times(c-a)||}{2}$

Barycentric Coordinates의 활용은 다음과 같다:
- Point-Inside-Trangle testing
- 세 꼭짓점으로 표현된 Attribute의 Linear Interpolation
	- Color `Gouraud Shading`
	- Normal `Phong Shading`
	- Texture Coordintes `Texture Mapping`
- Example
	- $n = s\cdot n_0 + t \cdot n_1 + r\cdot n_2$ ($n$은 normalize 되어야 한다.)

이제 BArycnetric Coordinate System을 이용해 표현된 교차점 $p$를 생각하며 식을 세워나가보자.

**Calculation**
![350](../../../Z.%20Docs/img/Pasted%20image%2020240725185859.png)
- 방정식의 형성:
$$
\begin{align}
&p(b_1, b_2) = (1 - b_1 - b_2)p_0 + b_1p_1 + b_2p_2 = o + td = p(t) \\
&\begin{pmatrix}-d & e_1 & e_2\end{pmatrix}\begin{pmatrix}t\\ b_1 \\ b_2\end{pmatrix} = s \quad \text{(그냥 위 식 정리한 거)} \\
&\begin{pmatrix}t\\ b_1 \\ b_2\end{pmatrix} = \frac{1}{(d\times e_2)\circ e_1}\begin{pmatrix}(s\times e_1)\circ e_2\\ (d \times e_2)\circ s \\ (s\times e_1)\circ d\end{pmatrix} \quad (\text{Using Cramer's rule and some math})
\end{align}
$$
- 방정식의 계산:
$$$$

#### Ray-Object Intersection: Convex Planar Polygon
> Convex Planar Polygon(볼록 평면 다각형)과 Ray의 교차를 계산하는 방법

#### Ray-Object Intersection: Axis-Aligned Bounding Box
> AABB와 Ray의 교차를 계산하는 방법 `계산의 효율을 높이기 위해 구역을 잘게 쪼개는 경우`


----------------------------------------------
#### Ray Tracing을 통한 Motion Blur

**Motion Blur**
- 모션 블러는 시간의 흐름 동안 물체의 위치와 모양이 변화함을 시뮬레이션하여 실현된다.
- Ray Tracing에서 Motion Blur를 구현하기 위해서 각 픽셀에 대해 여러 시간 샘플을 사용하여 시간 축에 따라 Ray를 추적한다. 각 샘플을 서로 다른 시간에 Scene의 상태를 반영하여 이미지에 시간 축의 정보를 반영한다.

**구현**
1. **Time Sampling**:
	- 각 Frame은 여러 시간 샘플로 나뉜다. 예를 들어, 한 프레임이 1초라면 이 프레임 내의 여러 시간 Point에서 Scene을 샘플링한다.
	- 이 Time Sample은 렌더링하는 동안 물체가 어떻게 움직이는지를 반영한다.
2. **Interpolation of Object Positions**:
	- Time Sample마다 Interpolation을 통하여 물체의 위치를 계산한다.
3. **Ray Generation**:
	- 각 Time Sample에 대해 Ray를 생성한다. 생성된 각 Ray는 특정 시간에서의 물체 위치와 상호작용한다.
	- 여러 Time Sample에서 Ray의 결과를 합산하여 최종 픽셀 값을 결정한다.
4. **Accumulation of Results**:
	- 여러 Time Sample의 결과를 평균내거나 합산하여 최종 픽셀의 색을 결정한다.
	- 이렇게 하면 Motion Blur 효과가 나타난다.

**Mathematics**
> $L(x, t) = \frac1T\int^T_0{L(x, y, t)dt}$
- $L(x, y)$: 픽셀 $(x, y)$에서의 최종 Radiance
- $T$: 프레임의 시간 길이
- $L(x, y, t)$: 시간 $t$에서의 픽셀 $(x, y)$의 Radiance

컴퓨터에서 연속적인 성격의 적분을 계산하기란 불가능하다. 따라서 시간 $t$를 이산 샘플링하여 다음과 같이 근사한다.
> $L(x, y) \approx \frac1N\sum^N_{i = 1} {L(x, y, t_i)}$
- $N$: Time Sample의 개수
- $t_i$: 각 Sample의 시간 값

**Pseudo Code**
```cpp
Color traceRayWithMotionBlur(Ray ray, Scene scene, int numSamples, float frameTime) {
    Color accumulatedColor = Color(0, 0, 0);
    
    for (int i = 0; i < numSamples; i++) {
        float time = i / float(numSamples - 1) * frameTime;
        Ray timeSampledRay = generateRayAtTime(ray, time);
        Color sampleColor = traceRay(timeSampledRay, scene);
        accumulatedColor += sampleColor;
    }
    
    return accumulatedColor / numSamples;
}
```

