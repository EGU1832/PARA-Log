#### Reference
- NVIDIA Vulkan Ray Tracing Tutorial pdf
- [GitHub - nvpro-samples/vk_raytracing_tutorial_KHR: Ray tracing examples and tutorials using VK_KHR_ray_tracing](https://github.com/nvpro-samples/vk_raytracing_tutorial_KHR)

본 자료는 NVIDIA Vulkan Ray Tracing Tutorial pdf를 기반으로 정리한 내용이다.
이 pdf는 `VK_KHR_ray_tragin_pipeline` Extension을 이용해서 Vulkan sample에 Ray tracing을 적용하는 것에 대한 튜토리얼을 담고 있으며, 다음과 같은 내용을 제공한다.
- Basic Vulkan Application
- Modify & Add Methods & Functions

얻을 수 있는 최종 결과는 다음과 같다.
![400](https://nvpro-samples.github.io/vk_raytracing_tutorial_KHR/Images/resultRaytraceShadowMedieval.png)

# 1. Introduction

상기했듯이, 본 pdf는 원래 존재하는 Vulkan Application에 Ray Tracing 기능을 추가함으로써 Vulkan의 실무 지식을 전반적으로 알아본다. `의역`
Vulkan Application의 코드 길이는 `C++ API helpers`와 NVIDIA의 `nvpro-samples` 프레임워크로 축소 되었다.


# 2. Environment Setup