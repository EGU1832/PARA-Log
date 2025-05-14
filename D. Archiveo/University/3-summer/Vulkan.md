#### Reference
- [Introduction - Vulkan Tutorial (vulkan-tutorial.com)](https://vulkan-tutorial.com/)
- [Vulkan/examples/triangle (GitHub)](https://github.com/SaschaWillems/Vulkan/blob/master/examples/triangle/triangle.cpp)

# Introduction

#### Vulkan
> Khronos 그룹의 새로운 그래픽 API로, OpenGL의 발전된 버전이라고 생각하면 된다.
> Window, Linux, Android 용으로 동시에 개발이 가능하다.

이렇게 보면 편리한 API의 끝판왕 같지만, 실상은 그렇지 않다.
내가 Application에서 다뤄야할 부분의 영역이 훨씬 더 넓어진다는 것이다.
- Initial Frame buffer Createion
- Memory Management for Buffer of Texture Images

`따라서 Vulkan은 좀 더 '컴퓨터 그래픽' 적인 것이다.
`따라서 단순히 게임 개발에 관심이 있다면 OpenGL 또는 Direct3D를 다루고 싶을 것이다.
`그러나 추후 동향을 고려하면 Unreal Engine이나 Unity 같은 엔진을 사용해서 높은 수준의 API로 Vulkan을 다루는 것이 더 좋을 것이다.

Vulkan을 다루기 위한 전제 조건은 다음과 같다.
- Vulkan과 호환된느 그래픽 카드 및 드라이버 `NVIDIA, AMD, Intel Apple Silicon`
- C++ 사용 경험
- C++17을 지원하는 컴파일러 `Visual Studio 2017+`
- 3D 컴퓨터 그래픽스에 대한 경험

일단 우리는 기본 API에 대한 경험을 쌓기 위해 **C API**를 사용할 것이다.
C++을 사용하는 경우 최신 Vulkan-Hpp 바인딩을 사용하는 것을 권장한다.

#### Tutorial Structure
> Vulkan의 작동 방식과 화면에 첫 번째 삼각형을 띄우기 위한 작업에 대한 개요

**개발 환경 세팅 (Window)**
- Visual Studio 17+ 사용
- Vulkan SDK
- GLM Library `for Linear Algebra Operation`
- GLFW `for window creation`

**첫 번째 삼각형을 띄우기위한 Step**
- 새로운 개념과 그 목적 소개
- 모든 관련 API 호출과 프로그램에 통함
- Helper Functions로 일부 추상화

Vulkan으로 첫 번째 삼각형을 띄우는 데 성공하면, Linear Transformation, Texture, 3D Models로 확장해 나갈 것이다.
한 번 삼각형을 그리고 나면 다음 단계는 그닥 복잡하게 느껴지지 않을 것이다.

그럼 이제 시작해보자!

# Overview

우리가 배워나갈 Step은 다음과 같다.
- Vulkan의 기원
- 삼각형을 그리기 위하여
	1. Instance and Physical Device Selection
	2. Logical Device and Queue Families
	3. Window Surface and Swap Chain
	4. Image Views and Framebuffers
	5. Render Passes
	6. Graphics Pipeline
	7. Command Pools and Command Buffers
	8. Main Loop
	9. Summary
- API
	- Coding Conventions
	- Validation Layers

삼각형 하나 그리는데 뭐가 많다. `코드 상으로 1000줄 이상이라 카더라`

각각의 Step에 대해서 간략하게 설명해보겠다.

#### Vulkan의 기원

GPU에 대한 **크로스 플랫폼** 추상화를 제공하기 위해 설계되었다.
**구체적인 API**를 사용하여 프로그래머가 의도를 명확히 지정할 수 있도록 하여 드라이브 오버헤드를 줄이고, 여러 **스레드가 병렬**로 명령을 생성하고 제출할 수 있도록 한다.
**단일 컴파일러**를 사용하여 표준화된 바이트 코드 형식으로 셰이더 컴파일의 불일치를 줄인다.
현대 그래픽 카드의 범용 처리 기능을 채택하여 그래픽 및 컴퓨팅 기능을 하나의 API로 통합한다.

#### 삼각형을 그리기 위하여
> Vulkan으로 삼각형을 그리기 위한 큰 그림

**1. Instance and Physical Device Selection**

Vulkan 어플리케이션은 `VkInstance`를 통해 Vulkan API를 설정하는 것으로 시작한다.
Instance를 생성하면, Hardware를 선택하고 작업에 사용할 하나 이상의 `VkPhysicalDevice`를 선택한다.

**2. Logical Device and Queue Families**

사용할 올바른 하드웨어 장치를 선택한 후에는 `VkDevice(logical device)`를 생성하여 `VkPhysicalDeviceFeatures`를 통해 더 상세하게 지정하여준다.

또한 사용할 큐 패밀리도 지정해주어야 한다. Vulkan에서는 거의 모든 Operation들이 `VkQueue`로 보내져 비동기적(asynchronous)으로 수행된다.
- 그래픽을 위한 큐 패밀리
- 계산과 메모리 이동 명령을 위한 큐 패밀리
- 물리적 디바이스 선택의 구별 인자를 위한 큐 패밀리 등

**3. Window Surface and Swap Chain**

렌더링된 이미지를 표시할 윈도우를 만드는 Step이다. 기본적으로 윈도우 기본 플랫폼 API나 GLFW 및 SDL과 같은 라이브러리를 사용하여 생성할 수 있다. `여기선 GLFW 사용`

윈도우에 실제로 렌더링하기 위해서는 추가로 두 가지 구성 요소가 필요하다.
- **윈도우 표면** `VkSurfaceKHR`: 윈도우에 렌더링하기 위한 크로스 플랫폼 추상화, 일반적으로 `HWND`와 같은 네이티브 윈도우 핸들에 대한 참조를 제공하여 인스턴스화된다.
- **스왑제인** `VkSwapChainKHR`: 여러 개의 렌더 타겟의 모음. 간단히 말하자면 표시할 이미지와 렌더링 중인 이미지를 분리하는 것이다. 프레임을 그릴 때마다 스왑체인에 렌더링할 이미지를 요청해야하며, 프레임을 그린 후에는 그 이미지를 스왑체인에 반환하여 나중에 화면에 표시될 수 있도록 한다. 총 몇장 (double/triple)을 쓸지도 지정해줄 수 있다.
`*KHR: 윈도우 확장 기능의 일부`

몇몇 플랫폼에선 `VK_KHR_display`와 `VK_KHR_display_swapchain` 확장을 통해 윈도우 매니저와 상호작용하지 않고 직접 디스플레이에 렌더링할 수 있다.

**4. Image Views and Framebuffers**

