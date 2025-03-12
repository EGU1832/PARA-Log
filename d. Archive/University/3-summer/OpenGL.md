
# Concepts

#### Shader Pipeline Overview
- Vertex Shader: 각 Vertex 들의 Final Position 계산
- Fragment Shader: 각 Fragment의 Final Color 결정
스크린에는 Vertex Shader와 Fragment Shader가 합쳐진 Final Image가 화면에 나타난다.
프로그램은 Vertex Shader `.vert`와 Fragment Shader `.frag`를 OpenGL에 묶어주는 역할을 한다.

#### Uniform Variable
- Uniform: OpenGL 변수 타입으로 Shader로 전달되는 읽기 전용 값으로, 주로 Matrix, Lighting, Parametmer, Color 등의 값을 저장하는데 사용된다.

# OpenGL Shading & Modeling Start

#### `5.3.7 Tiger_Shading_PS_SC_BL_GLSL`
> GLSL로 쓰여진 Phong Shader를 1개 이상의 광원과 함께 적용
> 광원에 Blind 효과 구현

## 1. Loading Shader

### 1.1 Shader Variables

#### Simple Shader 관련 변수
> 아무런 빛 효과를 적용하지는 않지만 Shader를 쓸 때 통일성을 두기 위해서 쓰는 것 같다.
```cpp
GLint h_ShaderProgram_simple
GLint loc_ModelViewProjectionMatrix_simple, loc_primitive_color;
```

#### Gouraud Shader 관련 변수
> `5.2.Tiger_Shading_GS_GLSL` 코드 참고
```cpp
GLint h_ShaderProgrma_GS;
#define NUMBER_OF_LIGHT_SUPPORTED 4 
GLint loc_global_ambient_color;
loc_light_Parameters loc_light[NUMBER_OF_LIGHT_SUPPORTED];
loc_Material_Parameters loc_material;
GLint loc_ModelViewProjectionMatrix_GS, loc_ModelViewMatrix_GS, loc_ModelViewMatrixInvTrans_GS;
```

#### Phong Shader 관련 변수
> Gouraud에 비해 필요한 변수가 늘어난다.
```cpp
GLint h_ShaderProgram_PS;
#define NUMBER_OF_LIGHT_SUPPORTED 4 
GLint loc_global_ambient_color;
loc_Light_Parameters loc_light[NUMBER_OF_LIGHT_SUPPORTED];
loc_Material_Parameters loc_material;
GLint loc_screen_effect, loc_screen_frequency, loc_screen_width;  // screen 효과를 위한 것으로, 기본적인 퐁 쉐이딩에선 필요없다.
GLint loc_blind_effect;  // 이 역시 마찬가지다.
GLint loc_ModelViewProjectionMatrix_PS, loc_ModelViewMatrix_PS, loc_ModelViewMatrixInvTrans_PS;
```
- loc_screen_effect, loc_screen_frequency, loc_screen_width: 화면에 나타나는 빨간색 Screen에 관련된 변수
- loc_blind_effect: 빛이 가려지는 Blind 효과와 관련된 변수
- loc_ModelViewProjectionMatrix_PS, loc_ModelViewMatrix_PS, loc_ModelViewMatrixInvTrans_PS: Model-View 행렬은 객체의 위치와 방향을 정의하고, 그 역전치 행렬은 법선 벡터를 변환할 때 사용된다.

#### 조명 관련 변수
> Uniform 변수에 전달하기 위해 조정되는 조명 전역 변수
```cpp
Light_Parameters light[NUMBER_OF_LIGHT_SUPPORTED];
```

**Plus Alpha**
조명의 변수가 겹친다고 같이 쓸 수 있는게 아니라 Shader마다 서로 다른 변수를 쓴다고 생각하면 된다.

### 1.2 Preparing Shader Program
> 해당 코드의 `prepare_shader_program()` 함수 참고
```cpp
ShaderInfo shader_info[] = {};
LoadShaders();  // LoadShaders.cpp
glGetUniformLocation();
```
- `ShaderInfo shader_info[]`: LoadShaders 함수를 사용하기 위해 자료구조 정리
- `LoadShaders()`: 주어진 Shader 파일들을 로드하고 컴파일 하며, 최종적으로 링크하여 Shader 프로그램 반환
- `glGetUniformLocation()`: 위에서 설명한 변수에 Shader 프로그램에 대한 Uniform 변수의 위치를 가져온다.

### 1.3 Set up Light
> 해당 코드의 `initialize_lights_and_material(), set_up_scene_lights()` 함수 참고
```cpp
glUseProgram();
glUniformi_();
glUseProgram(0);
```
- `loc_global_ambient_color`: 전역 환경광, 모든 물체에 균일하게 적용되는 기본 조명이다.
- `loc_light`: `NUMBER_OF_LIGHT_SUPPORTED` 만큼 있으며, 다음과 같은 설정이 있다.
	- `light_on`: 조명 on/off
	- `position`: 조명의 위치
	- `ambient color`: 조명의 ambient_color
	- `diffuse_color, specular_color`: 조명의 난반사 정반사 색깔, 보통 똑같이 설정
	- `spot_direction, spot_exponent, spot_cutoff_angle, light_attenuation_factors`: 스폿 조명인 경우의 설정들이다. 스폿 조명을 off 하고 싶다면 **spot_cutoff_angle**을 **180.0f**으로 설정하면 된다.
- `loc_material`: 물체의 재질 속성으로, 물체의 표면이 어떻게 빛을 반사하는지 정의한다.	![450](https://antonimediaproject.blogs.lincoln.ac.uk/files/2017/11/Light-definitions.png)
	- `ambient_color, diffuse_color, specular_color`: 위 사진 참고
	- `emissive_color`: 객체 자체가 발광하는 색
	- `specular_exponent`: 반사광의 정도
- Etc: 각종 효과를 위한 Uniform 변수
	- `loc_screen_effect, loc_screen_frequency, loc_screen_width`: Screen 효과와 관련된 변수
	- `loc_blind_effecct`: Blind 효과와 관련된 변수

**Plus Alpha**
`glUseProgram(loc_light, 상수)` 초기화 할 때는 loc_light를 직접 설정하고
`glUseProgram(loc_light, light)` 그 다음에 scene을 세팅할 때는 light를 바꾸고 이른 glUseProgram에 전달하는 것이 좋다.

#### Set up - MC, EC, WC
>  MC: 각 정점이 물체 좌표계 기준으로 어디에 있는지
>  -`Modeling Transformation`-> WC: 각 정점이 세계를 기준으로 어디에 있는지
>  -`Viewing Transformation`-> EC: 각 정점이 카메라(눈)에서 볼 때 어디에 있는지
>  -`Projection Transformation`-> CC -> NDC: 정규 디바이스 좌표계
>  -`Viewport Transformation`-> WdC: 윈도우 좌표계

일단 기본적으로 Shading은 EC 상의 Light를 기준으로 계산된다. 따라서 light.position만을 설정한다면 **EC에서 배치**하는 것을 생각하는 것이고, 그 외에는 추가 작업을 해줘야 한다.
또한, EC가 아닌 다른 좌표로 설정할 경우 shader에 따라 spot_direction, 즉 **빛이 비추는 방향**도 함께 추가 작업을 해줘야 하는 경우가 있다.

- **MC에서 배치**: 모델을 원점으로 생각해서 빛을 어딘가에 두고싶다
	- `light.position`을 설정할 때 모델 좌표계를 기준으로 좌표를 설정한다고 생각하고 값을 세팅한다.
	- 그 다음 위의 Modeling Transformation과 Viewing Transformation을 수행하여 EC로 변환해준 뒤 `glUniform`으로 값을 유니폼 변수에 링크해준다.
- **WC에서 배치**: 세상을 기준으로 생각해서 빛을 어딘가에 두고시다
	- `light.position`을 설정할 때 세상 좌표계를 기준으로 좌표를 설정한다고 생각하고 값을 세팅한다. `glUniform`까지 수행한다.
	- 그 다음 위의 Viewing Transformation에 해당하는 `ViewMatrix`로 EC로 변환해준 뒤 `glUniform`으로 값을 수정해준다.
- **EC에서 배치**: 카메라(눈)를 원점으로 생각해서 빛을 어딘가에 두고싶다
	- 그냥 `light.position`만 설정해주면 된다.

간단하게 생각하자면 EC에서 좌표계가 멀어질 수록 단계가 추가되는 것이다.

#### Set up - Point, Spot
- spot_direction을 설정하는 shader라면 위와 마찬가지로 EC 좌표계가 아니라면 추가적인 변환이 필요하다.

### 1.4 Drawing Object with Shader
> 해당 코드의 `display()` 함수 참고
```cpp
glUseProgram();
set_material();
ModelViewMatrix, ModelViewProgectionMatrix, MdodelViewMatrixInvTrans;
glUniformMatrix();
draw();
```
- `glUseProgram()`: 어떤 Shader를 쓸지 결정
- `set_material()`: draw에서 설정해줬던 Shader와 관련된 변수를 Shader의 Uniform 변수에 링크
- `ModelViewMatrix,..`: WC -> EC를 위한 Affine Transform $M_V$ 수행
- `glUniformMatrix()`: 위에서 변환한 Matrix들을 Shader의 Matrix Uniform 변수에 링크
- `draw()`: Bind 함수와 VAO, 그리고 OpenGL draw 함수로 오브젝트를 그림. 자세한 것은 밑에 기술

## 2. Loading Model

### 2.1 Drawing Object with No Texture
#### OpenGL에서 직접 오브젝트 그리기
> `5.3.7 Tiger_Shading_PS_SC_BL_GLSL`의 axes, floor, screen 관련 코드 참고

**Shading X**
- Prepare:
	- 물체를 그릴 Vertex Data를 담은 VBO Array를 직접 입력
	- VAO가 따로 없는 경우 그냥 VBO와 1대 1로 매칭 시켜서 사용하는 것 같다.
	- VBO, VAO initialize
- Draw:
	- Bind 함수를 통해 VAO 사용할 준비
	- Simple Shader와 연결된 `loc_primitive_color` 색깔 설정 후, 그리기

**Shading O**
> Material Parameter가 생긴다.
- Prepare:
	- 전체적인 방식은 위와 같다. 다만 Normal Attribute와 Ambient, Diffuse, Specular, Emissive Color를 설정하는 것이 추가된다.
- Set Meterial:
	- 위에서 직접 설정한 재질 특성(Colors, Specular Exponent)을 Shader 프로그램에 `glUniformi(type)` 함수를 이용하여 연결한다.
- Draw:
	- 전체적인 방식은 위와 같다. 텍스쳐를 사용할 때는 추가 작업이 필요함을 알고가자.

**Plus Alpha**
바인딩을 했으면 바인딩을 해제하는 것을 잊지 말자.
Shader와 관련된 변수는 Light까지 모두 포함하여 따로 설정해주어야 한다. `_simple, _PS, _GS 등과 같이 구분자 추가`

#### OpenGL 외부에서 데이터를 가져와 오브젝트 그리기
> `5.3.7 Tiger_Shading_PS_SC_BL_GLSL`의 tiger 관련 코드 참고
> Animated Object라고 가정하며, 여기서 텍스처는 무시한다.

- Prepare:
	- 보통 우리는 `fp`로 파일을 불러들여서 그 파일의 정보를 이용해 오브젝트를 그리게 된다. 물체를 그리는 기본 단위는 **삼각형**이다. 
	- 파일의 구조는 `n_triangles | data | data | ...`와 같기에 정보를 읽어오려면 우리는 삼각형 당 바이트 크기를 알아야 된다. $$\begin{align}\text{n bytes per triangle} &= 3\times \text{n bytes per vertex} \\ &= 3 \times (\text{* of vertex + * of normal + * of texcoord}) \times \text{sizeof(float)} \\&= 3 \times 8\times \text{sizeof(float)}\end{align}$$
	- 파일을 읽어올 때는 `.geom` 이진 모드 `"rb"`로 연다.
	- 결과적으로 우리가 위에서 만들었던 VBO와 같은 정보를 담고 있는 배열이 만들어지게 된다.
	- 나머지 VBO, VAO 할당 과정은 `직접 오브젝트 그리기-Shading O`와 같다.
- Set Material:
	- `직접 오브젝트 그리기-Shading O`와 같다.
- Draw:
	- Animated 오브젝트는 Frame이 여러개이기에 `glDrawArrays`로 오브젝트를 그릴 때 각 프레임을 그리기 시작할 Offset과 각 프레임 당 삼각형의 개수도 필요하다. 이 값은 Prepare에서 미리 처리한다.

**Plus Alpha**
정적인 오브젝트를 그리는 방법은 Animated 오브젝트를 그리는 방법을 알면 자연스럽게 이해가 될 것이다.

### 2.2 Drawing Object with Texture

Texture Coordinate: 일반적으로 2D 좌표로 표현되며, 3D 상의 정점이 2D 텍스처 이미지의 어느 좌표에 연결되는지 나타낸 정보이다.

## 3. Shadows